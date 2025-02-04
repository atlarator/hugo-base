---
title: "SpinalHDL Basics"
date: 2025-02-03T16:37:17+08:00
draft: false
tags: ["COA"]
---

## Examples

```Scala
showRtl(new MyTop) //generate rtl, default Verilog
SpinalVerilog(new MyTop) //generate Verilog in current dir
```
in Spinal, ``Component`` is default, but ``type Module = Component`` is also okay  
config Global Settings in ``SpinalConfig``  

## Design Rules

never erase the previous assignment, except they're in different blocks, or use ``a.allowOverride`` to allow this action  

SpinalHDL will check the registers you used only depends on same/sync clock registers  
fix: add the tag  
```Scala
val regB = clkB(Reg(UInt(8 bits))).addTag(crossClockDomain)
```
or set the clocks synchronous together  
```Scala
clkB.setSyncronousWith(clkA)
```
bufferCC could go across clock domain  

Spinal doesn't allow combinational loop in code  

beware of the loop checking, or fix by  
```Scala
val a = UInt(8 bits).noCombLoopCheck
```

Check the Hierarchy violation  

## Datatypes

Spinal never forces IO Bundle, never forces instantiation by Module keywords, never remove unused names  
basic types: Bool, Bits, UInt, SInt; composite: Bundle, Vec  
```Scala
class Top extends Component 
{
    val my_bool = Bool()
}
showRtl(new Top)
```
edges: rise, fall, edge  
```Scala
val x = Bool()
val pulse1 = x.fall()
val pulse2 = x.rise()
val pulse3 = x.edge() 
```
set & clear: 
```Scala
class Top extends Component{
  val x = Bool()          
  val y = UInt(8 bits)
  when(x.edge){
      y.setAll
  }.otherwise{
      y.clearAll
  }
}
showRtl(new Top)
```
Bits: use downto to choose bits, use when to do conditional branch  
```Scala
val myBits = in Bits(8 bits)
  // cast a Bits to SInt
  val mySInt = myBits.asSInt

  // create a Vector of bool
  val myVec = myBits.asBools

  // Cast a SInt to Bits
  val myBits2 = B(mySInt)
```
Vec: a group of indexed symbols  
```Scala
val myVecOfSInt = Vec(SInt(8 bits),2)
myVecOfSInt(0) := 2
myVecOfSInt(1) := myVecOfSInt(0) + 3
```
use fold or reduce method to design FIFO  
interesting methods:  
```Scala
(1 to 10).foldLeft(0)((a,b)=>{println(s"$a-->$b");b})
//same like reduceLeft(), foldRight(), reduceRight()
```

## Combinational  

``:=`` is as ``<=`` in Verilog, ``\=`` is as ``=`` in Verilog, ``<>`` is for natrual link of same datatypes
Register value:  
```Scala
val c = Reg(UInt(4 bits)) init(0)
```
resize the value using ``resized`` or ``resize(Int)`` method  

Conditional:  
``when(){}.elsewhen(){}.otherwise{}``  
``switch(x){ is(0){} is(1){} default{} }``  
``Mux(cond, ..., ...)``  
``cond ? ... : ...``  

bitwise branch:  
```Scala
//mux
class Top extends Component{
    val io = new Bundle{
        val src0,src1 = in Bool()
    }
  val bitwiseSelect = UInt(2 bits)
  val bitwiseResult = bitwiseSelect.mux(
   0 -> (io.src0 & io.src1),
   1 -> (io.src0 | io.src1),
   2 -> (io.src0 ^ io.src1),
   default -> (io.src0)
  )
}
showRtl(new Top)
```

divide 128 bits into a 4-way Mux  
```Scala
val sel  = in UInt(2 bits)
val data = in Bits(128 bits)
val dataWord = sel.muxList(for(index <- 0 until 4) yield (index, data(index*32+32-1 downto index*32)))
val dataWord2 = data.subdivideIn(32 bits)(sel)
```

Package operations as methods improve abstract ability  
Internal methods can operate on all variables under Component  
Of course, it can also be package as an independent function, which is passed in as a parameter by the operation object  

## Sequential

Reg(type:Data), RegInit(resetValue:Data), RegNext(nextValue : Data), RegNextWhen(nextValue : Data, cond : Bool)  
RegNext(something) is equal to:  
```Scala
val something = Bool()
val value = Reg(Bool())
value := something
```
If you have a register containing a Bundle, you can use the init function on each element of the Bundle.  
```Scala
case class ValidRGB() extends Bundle{
  val valid = Bool()
  val r,g,b = UInt(8 bits)
}

class Top extends Component{
  val reg = Reg(ValidRGB())
  reg.valid init(False)  //Only the valid of that register bundle will have an reset value.
}
showRtl(new Top)
```

for initialization for simulation, using randBoot() method  

Memory: use Mem() class  
```Scala
val mem = Mem(Bits(32 bits),wordCount = 256)
```

memory could be blackboxed  
```Scala
val mem = Mem(Rgb(RgbConfig(8,8,8)),1024)
mem.generateAsBlackBox()
```
## Fixed-Point

```Scala
val c = a + b //return 8 bits without protection, may cause overflow
val c = a +^ b //return 9 bits, adder with carry
```
bits also can be rounded  
```Scala
val a = in SInt(16 bits) //source data is 16 bits 
val f = a.roundUp(2)     //round 2 bits to +Inf  return 15 bits
```
fixTo: a way same as round to inf  

width propagation is auto in Spinal.  
bit join:  
```Scala
c:= (a0 ## a1).asSInt
//assign c = {a0,a1};
```
in/out < Typename > or use master/slave  
jump wire:  
```Scala
xx :=  ctrl.counter.pull() //Jump wire auto through IO
```
SpinalHDL will never Pruned signals with names  
The ways you can use Scala functions to generate hardware are radically different than VHDL/Verilog for many reasons:  
You can instantiate registers, combinational logic and components inside them.  
You don’t have to play with process/@always that limit the scope of assignment of signals  
Everything is passed by reference, which allows easy manipulation. For example you can give a bus to a function as an argument, then the function can internaly read/write to it. You can also return a Component, a Bus, or anything else from scala and the scala world.  

define something inside the class is accepted  
Area is used to define a group of logic  
(TODO)
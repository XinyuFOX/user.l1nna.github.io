+++
title = "IDAAPI Notes"
date = " 2018-04-13"
description = "IDAAPI quick reference notes"
tags = ["Python", "IDA", "Reverse Engineering"]
author = "Steven"
image = "https://picsum.photos/1024/400/?image=1078"
+++

## Force creation of an assembly function: 

```python
# get address from unstripped file
def check():
    f = idaapi.get_func(ScreenEA())
    print [hex(f.startEA), hex(f.endEA)]

# create function in stripped file
def create(ea_start, ea_end):
    for addr in range(ea_start, ea_end): 
        idaapi.create_insn(addr)
    MakeFunction(ea_start, ea_end)
```

## Remove stack reference name in current function:

```python
def unstack():
    f = idaapi.get_func(ScreenEA())
    for addr in range(f.startEA, f.endEA): 
        for i in range(5):
            idc.OpOff(addr, i, 16)
```
---
cssclasses:
  - 杂记
---
## Vivado与VScode联合
正确启动python环境应在用户设置中添加以下设置
```
"terminal.integrated.env.windows": { 
"PYTHONHOME": null, 
"PYTHONPATH": null, 
"TCL_LIBRARY": null, 
"TK_LIBRARY": null }
```
## BRAM IP核
- 位宽8bit，地址自增加1；位宽32bit，地址自增加4；
- 最大地址=(存储器深度-1)$\times$(位宽(单位：bit) $\div$ 8)
## 函数
- `include "sleep.h"`
    - `sleep()`,休眠函数
- `include “xil_io.h"`
    - `Xil_Out32()`,指定寄存器地址写
## 问题汇总
### scanf函数
- 使用scanf函数可以直接读取uart接受数据存入字符串
- 使用串口写入scanf时要加 `\n` 字符
### printf函数
- printf是“标准库输出 + 缓冲”，需要加入"\n"才能实时刷新缓冲区。或遇到"return 0"程序结束强制刷新缓冲区。而Xilinx自己的print函数则无此种顾虑，因为它直通串口。后者和Stm32中对printf进行重定向是一个逻辑
### `Makefile`适用于Vits2024.2 Classic
``` makefile
COMPILER=
ARCHIVER=
CP=cp
COMPILER_FLAGS=
EXTRA_COMPILER_FLAGS=
LIB=libxil.a
 
RELEASEDIR=../../../lib
INCLUDEDIR=../../../include
INCLUDES=-I./. -I${INCLUDEDIR}
 
INCLUDEFILES=*.h
LIBSOURCES=$(wildcard *.c)
OBJECTS = $(addsuffix .o, $(basename $(wildcard *.c)))
ASSEMBLY_OBJECTS = $(addsuffix .o, $(basename $(wildcard *.S)))
 
libs:
	echo "Compiling simple_adder..."
	$(COMPILER) $(COMPILER_FLAGS) $(EXTRA_COMPILER_FLAGS) $(INCLUDES) $(LIBSOURCES)
	$(ARCHIVER) -r ${RELEASEDIR}/${LIB} ${OBJECTS} ${ASSEMBLY_OBJECTS} 
	make clean
 
include:
	${CP} $(INCLUDEFILES) $(INCLUDEDIR)
 
clean:
	rm -rf ${OBJECTS} ${ASSEMBLY_OBJECTS}
```
### 中断
- `Xil_ExceptionEnableMask(XIL_EXCEPTION_IRQ);`
- `Xil_ExceptionEnable()
- 异常注册使用Xil_ExceptionHandler类型，中断函数关联使用Xil_InterruptHandler类型
- `XUartPs_WriteReg(uart_ps1->Config.BaseAddress,XUARPS_ISR_OFFSET,XUARTPS_IXR_RXOVR);`
  中断标志清除机制被称为 **W1C (Write 1 to Clear)**

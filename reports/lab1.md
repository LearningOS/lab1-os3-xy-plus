# lab1 report

## 实现功能

修改 TaskControlBlock ，加入成员变量 TaskInfo 。

初始化时，TaskInfo.status 为 TaskStatus::Running 。由于查询的是当前任务的状态，因此 TaskStatus 一定是 Running ；TaskInfo.syscall_times 为 全 0 ；TaskStatus.time 用 get_time_us() 赋值。

每次调用 syscall 的时候，都先更新 TaskControlBlock.TaskInfo ，然后真正去调用 syscall 。

返回的 task_info.status 和 task_info.syscall_times 都是直接从 TaskControlBlock 复制；task_info.time 为 (get_time_us() - TaskControlBlock.TaskInfo.time) / 1000 ，从而体现出时间差以及时间单位换算。

## 简答作业

### 1

> [rustsbi] RustSBI version 0.2.2, adapting to RISC-V SBI v1.0.0

- ch2b_bad_address.rs
  - [ERROR] [kernel] PageFault in application, core dumped.
  - 访问错误地址
- ch2b_bad_instructions.rs
  - [ERROR] [kernel] IllegalInstruction in application, core dumped.
  - 执行非法指令
- ch2b_bad_register.rs
  - [ERROR] [kernel] IllegalInstruction in application, core dumped.
  - 执行非法指令

### 2

#### 1

__restore 是在 switch 的时候，通过设置 ra 寄存器调用的。在 switch 期间没有写过 a0 寄存器，因此 a0 在 __restore 时仍然是 switch 的第一个参数，unuse 或者 current_task_cx_ptr 。

在启用第一个进程和切换进程这两个场景下，都会需要通过 restore 来设置寄存器。

#### 2

特殊处理了 sstatus、sepc、sscratch 寄存器。

- sscratch：进入用户态时，保存内核栈地址。用户态返回内核态时，将 sp 设置为内核栈地址。
- sepc：当 Trap 是一个异常的时候，记录 Trap 发生之前执行的最后一条指令的地址
- sstatus：SPP 等字段给出 Trap 发生之前 CPU 处在哪个特权级（S/U）等信息

#### 3

x2 是 sp ，会在后面保存到 sscratch 。

x4 用户程序用不到，所以不用保存。

#### 4

sscratch 是内核栈地址，sp 是用户栈地址。

#### 5

sret 指令。

sret 的时候，CPU 会将当前的特权级按照 sstatus 的 SPP 字段设置为 U 或者 S ；CPU 会跳转到 sepc 寄存器指向的那条指令，然后继续执行。

#### 6

sp 是内核栈，sscratch 是用户栈。

#### 7

ecall

## 其它

作业题和 trap.S 的行有一些不一致。

# Design and Simulation of an 8-bit ALU Computer Architecture

**Student:** Abdallah Mohammed Abdelqader  
**Section:** 1  
**Supervisor:** Dr. Hesham Hamed Amen  

---

## 1.0 Introduction to the Arithmetic Logic Unit (ALU)

The **Arithmetic Logic Unit (ALU)** is a fundamental digital circuit and a cornerstone component of any **Central Processing Unit (CPU)**. It serves as the computational engine of the processor, responsible for executing the arithmetic and logical computations that form the basis of all computer programs. From simple addition to complex bitwise comparisons, the ALU's ability to manipulate data is central to a computer's function. The strategic importance of the ALU lies in its role as the direct executor of machine-level instructions passed down from software applications.

The objective of this report is to present the detailed design and simulated operation of a foundational **8-bit ALU**. This unit is designed to support four core operations essential for general-purpose computing:

- Addition  
- Subtraction  
- Bitwise AND  
- Bitwise OR  

Furthermore, to demonstrate its integration within a realistic processor architecture, the design accommodates three distinct addressing modes:

- Register  
- Direct  
- Indirect  

To contextualize the ALU's role, this report will first delineate the high-level system architecture in which it is designed to operate.

---

## 2.0 Core System Architecture

An ALU does not function in isolation; it is a critical component within an integrated system. To fully comprehend its role, one must understand its interaction with the **Control Unit**, **Memory**, and **Registers**, which collectively form a simplified processor model. The seamless coordination between these elements allows for the fetching, decoding, and execution of instructions.

### Main Components

- **Central Processing Unit (CPU):**  
  The encompassing unit that houses the core processing components. In this design, it includes the Control Unit, the Arithmetic & Logic Unit, and the Register File.

- **Control Unit (CU):**  
  Acts as the orchestrator of the entire system. It decodes program instructions and generates the control signals required to manage data flow and operations.

- **Arithmetic & Logic Unit (ALU):**  
  The computational core of the CPU, responsible for performing arithmetic and logical operations.

- **Register File:**  
  A small, high-speed storage array within the CPU that holds operands, intermediate results, and final outputs. Its low latency significantly accelerates computation.

- **Memory Unit (MU):**  
  Serves as the primary storage for program instructions and data. It provides large capacity at the cost of higher latency compared to registers.

---

## 3.0 ALU Functional Specification

A clear functional specification precisely defines what a component does before considering its implementation.

### 3.1 Supported Operations

The ALU executes four fundamental arithmetic and logical operations. Each operation takes two 8-bit operands and produces a single 8-bit result.

| Operation | Description |
|---------|-------------|
| ADD | Performs binary addition on two 8-bit operands |
| SUB | Performs subtraction using 2's complement methodology |
| AND | Performs a bitwise logical AND operation |
| OR  | Performs a bitwise logical OR operation |

---

### 3.2 Addressing Modes

Addressing modes define how the location of an operand is specified within an instruction.

#### 3.2.1 Register Addressing Mode

- **Example:** `ADD R1, R2`  
- **Operational Flow:** Operands are fetched directly from registers.  
- **Characteristics:** Fastest execution mode due to no memory access.

#### 3.2.2 Direct Addressing Mode

- **Example:** `ADD R1, [100H]`  
- **Operational Flow:** One memory read cycle is required to fetch the operand.  
- **Characteristics:** Slower than register addressing due to memory access.

#### 3.2.3 Indirect Addressing Mode

- **Example:** `ADD R1, [[200H]]`  
- **Operational Flow:** Two memory accesses are required to fetch the operand.  
- **Characteristics:** Slowest mode, enables pointer-based data structures.

---

## 4.0 Internal Hardware Design and Implementation

The ALU’s external behavior is realized through internal combinational logic and gate-level structures.

### 4.1 Arithmetic Unit

- **Addition:**  
  Implemented using eight cascaded Full Adders forming an 8-bit ripple-carry adder.

- **Subtraction:**  
  Implemented using 2's complement logic via XOR gates and a carry-in signal.

### 4.2 Logic Unit

- **AND Operation:**  
  Implemented using eight parallel AND gates.

- **OR Operation:**  
  Implemented using eight parallel OR gates.

### 4.3 Output Selection

An **8-bit wide 4-to-1 multiplexer** selects the final output based on control signals from the Control Unit.

---

## 5.0 Control Unit Signals and Logic

The Control Unit governs system behavior using binary control signals.

### 5.1 ALU Operation Control

Two control bits select the ALU operation:

| ALU_OP1 | ALU_OP0 | Operation |
|-------|-------|-----------|
| 0 | 0 | ADD |
| 0 | 1 | SUB |
| 1 | 0 | AND |
| 1 | 1 | OR |

---

### 5.2 Memory and Register Control

| Addressing Mode | Reg_Read | Reg_Write | Mem_Read | Mem_Write |
|----------------|----------|-----------|----------|-----------|
| Register | 1 | 1 | 0 | 0 |
| Direct | 1 | 1 | 1 | 0 |
| Indirect | 1 | 1 | 1 (two cycles) | 0 |

**Analysis:**  
- Register access is always required.  
- Memory read is needed only for Direct and Indirect modes.  
- Memory write is not required for any instruction considered.

---

## 6.0 Instruction Execution Case Study

### Example Instruction
```asm
ADD R1, [300H]

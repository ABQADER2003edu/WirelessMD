# Design and Simulation of an 8-bit ALU Computer Architecture

---

## Student Information

- **Student:** Abdallah Mohammed Abdelqader  
- **Section:** 1  
- **Supervisor:** Dr. Hesham Hamed Amen  

---

## 1.0 Introduction to the Arithmetic Logic Unit (ALU)

The **Arithmetic Logic Unit (ALU)** is a fundamental digital circuit and a cornerstone component of any **Central Processing Unit (CPU)**. It serves as the computational engine of the processor, responsible for executing the arithmetic and logical computations that form the basis of all computer programs.

From simple addition to complex bitwise comparisons, the ALU's ability to manipulate data is central to a computer's function. The strategic importance of the ALU lies in its role as the direct executor of machine-level instructions passed down from software applications.

The objective of this report is to present the detailed design and simulated operation of a foundational **8-bit ALU**. This unit supports four core operations essential for general-purpose computing:

- Addition  
- Subtraction  
- Bitwise AND  
- Bitwise OR  

Furthermore, to demonstrate its integration within a realistic processor architecture, the design accommodates **three distinct addressing modes**:

- Register  
- Direct  
- Indirect  

To contextualize the ALU's role, this report first delineates the high-level system architecture in which it operates.

---

## 2.0 Core System Architecture

An ALU does not function in isolation; it is a critical component within an integrated system. Understanding its role requires examining its interaction with the **Control Unit**, **Memory**, and **Registers**, which collectively form a simplified processor model.

### Main Architectural Components

- **Central Processing Unit (CPU):**  
  Encompasses the Control Unit, ALU, and Register File, forming the brain of the computational system.

- **Control Unit (CU):**  
  Decodes program instructions and generates control signals to manage data flow and component operations.

- **Arithmetic & Logic Unit (ALU):**  
  Executes all arithmetic and logical operations on data operands.

- **Register File:**  
  High-speed internal storage holding operands, intermediate results, and outputs. Its low latency makes it critical for performance.

- **Memory Unit (MU):**  
  Stores program instructions and data. It offers large capacity but higher latency than registers and interacts closely with the CPU.

---

## 3.0 ALU Functional Specification

A functional specification precisely defines what a component does before implementation.

### 3.1 Supported Operations

Each operation accepts two 8-bit operands and produces one 8-bit result.

| Operation | Description |
|---------|-------------|
| ADD | Binary addition of two 8-bit operands |
| SUB | Subtraction using 2's complement |
| AND | Bitwise AND operation |
| OR  | Bitwise OR operation |

---

### 3.2 Addressing Modes

Addressing modes define how operand locations are specified within instructions.

#### 3.2.1 Register Addressing Mode

- **Example:** `ADD R1, R2`  
- **Operational Flow:** Operands fetched directly from registers.  
- **Characteristics:** Fastest mode; no memory access.

---

#### 3.2.2 Direct Addressing Mode

- **Example:** `ADD R1, [100H]`  
- **Operational Flow:** Address extracted from instruction; one memory read cycle.  
- **Characteristics:** Slower than register addressing.

---

#### 3.2.3 Indirect Addressing Mode

- **Example:** `ADD R1, [[200H]]`  
- **Operational Flow:** Two memory reads—first for effective address, second for operand.  
- **Characteristics:** Slowest mode; enables pointer-based data structures.

---

## 4.0 Internal Hardware Design and Implementation

The ALU’s external functions are implemented using logic gates and combinational circuits.

### 4.1 Arithmetic Unit

- **Addition:**  
  Implemented using eight cascaded Full Adders (8-bit ripple-carry adder).

- **Subtraction:**  
  Uses 2’s complement logic:  
  \[
  A - B = A + (\lnot B) + 1
  \]  
  Implemented via XOR gates and control logic using the same adder hardware.

---

### 4.2 Logic Unit

- **AND Operation:**  
  Eight parallel AND gates (bitwise operation).

- **OR Operation:**  
  Eight parallel OR gates (bitwise operation).

---

### 4.3 Output Selection

An **8-bit wide 4-to-1 multiplexer** selects the final ALU output based on control signals from the Control Unit.

---

## 5.0 Control Unit Signals and Logic

The Control Unit manages CPU activity using binary control signals.

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
|---------------|----------|-----------|----------|-----------|
| Register | 1 | 1 | 0 | 0 |
| Direct | 1 | 1 | 1 | 0 |
| Indirect | 1 | 1 | 1 (two cycles) | 0 |

**Analysis:**  
- Register access is always required.  
- Memory write is never used.  
- Indirect mode requires two memory reads.

---

## 6.0 Instruction Execution Case Study

### Example Instruction  
`ADD R1, [300H]`

**Execution Steps:**

1. Instruction Decode  
2. Address Dispatch  
3. Operand Fetch  
4. Data Routing  
5. ALU Execution  
6. Result Write-back  
7. Flag Update (Zero, Carry, Negative)

This sequence demonstrates precise coordination between CPU components.

---

## 7.0 Conclusion

This report presented the complete design of a functional **8-bit ALU system**, covering architecture, functionality, and internal logic.

### Key Highlights

- Four arithmetic and logical operations  
- Three addressing modes  
- Centralized control via Control Unit  
- Modular, simulation-friendly design  

The architecture is well-suited for educational purposes and simulation tools such as **Logisim**, **Proteus**, and **Multisim**. Future enhancements could include pipelining or advanced logical units to improve performance.

---

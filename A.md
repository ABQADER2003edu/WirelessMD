# Design and Simulation of an 8-bit ALU Computer Architecture

**Student:** Abdallah Mohammed Abdelqader  
**Section:** 1  
**Supervisior:** Dr. Hesham Hamed Amen

---

## 1.0 Introduction to the Arithmetic Logic Unit (ALU)

The Arithmetic Logic Unit (ALU) is a fundamental digital circuit and a cornerstone component of any Central Processing Unit (CPU). It serves as the computational engine of the processor, responsible for executing the arithmetic and logical computations that form the basis of all computer programs. From simple addition to complex bitwise comparisons, the ALU's ability to manipulate data is central to a computer's function. The strategic importance of the ALU lies in its role as the direct executor of machine-level instructions passed down from software applications.

The objective of this report is to present the detailed design and simulated operation of a foundational 8-bit ALU. This unit is designed to support four core operations essential for general-purpose computing: Addition, Subtraction, bitwise AND, and bitwise OR. Furthermore, to demonstrate its integration within a realistic processor architecture, the design accommodates three distinct addressing modes—Register, Direct, and Indirect—to access the operands on which it operates. To contextualize the ALU's role, this report will first delineate the high-level system architecture in which it is designed to operate.

## 2.0 Core System Architecture

An ALU does not function in isolation; it is a critical component within an integrated system. To fully comprehend its role, one must understand its interaction with the Control Unit, Memory, and Registers, which collectively form a simplified processor model. The seamless coordination between these elements allows for the fetching, decoding, and execution of instructions. The primary components of this architecture are detailed below.

* **Central Processing Unit (CPU):** This is the encompassing unit that houses the core processing components. In this design, it includes the Control Unit, the Arithmetic & Logic Unit, and the Register File, forming the brain of the computational system.
* **Control Unit (CU):** The Control Unit acts as the orchestrator of the entire system. Its primary responsibility is to decode program instructions and generate the precise control signals required to manage the data flow and operations of all other components, including the ALU, registers, and memory.
* **Arithmetic & Logic Unit (ALU):** As the computational core of the CPU, the ALU is responsible for performing all arithmetic calculations (e.g., addition, subtraction) and logical operations (e.g., AND, OR) on data operands.
* **Register File:** The Register File is a small, high-speed storage array located within the CPU. It holds operands, intermediate results, and final outputs. Its key characteristic is its low-latency access, which is orders of magnitude faster than main memory. This high speed justifies its small size and critical role in providing the ALU with immediate access to data, thereby significantly accelerating computation.
* **Memory Unit (MU):** The Memory Unit serves as the primary storage for both program instructions and data. It offers a large capacity at the cost of higher latency compared to the Register File. While it functions as the main system memory, some simplified architectural diagrams depict it as tightly coupled within the overall CPU block for visualization purposes. It interacts closely with the CPU, particularly when operands must be fetched from memory, as dictated by certain addressing modes.

Having established the high-level system components, the following section will define the specific functions this ALU is designed to perform.

## 3.0 ALU Functional Specification

A clear functional specification is paramount in digital design, as it precisely defines what a component does before considering how it is implemented. This section outlines the capabilities of the 8-bit ALU by detailing its supported operations and the methods it employs to access data, known as addressing modes.

### 3.1 Supported Operations

The ALU is designed to execute four fundamental arithmetic and logical operations. Each operation takes two 8-bit operands as input and produces a single 8-bit result.

| Operation | Description |
| :--- | :--- |
| **ADD** | Performs binary addition on two 8-bit operands. |
| **SUB** | Performs subtraction using 2's complement methodology. |
| **AND** | Performs a bitwise logical AND operation on two 8-bit operands. |
| **OR** | Performs a bitwise logical OR operation on two 8-bit operands. |

### 3.2 Addressing Modes

Addressing modes define how the location of an operand is specified within an instruction. This ALU supports three essential modes, each with distinct performance characteristics.

#### 3.2.1 Register Addressing Mode

In register addressing mode, both operands are located in the CPU's internal Register File.

* **Example:** `ADD R1, R2`
* **Operational Flow:** The operands are fetched directly from the specified registers (R1 and R2) and passed to the ALU. The main memory is not involved in the operation.
* **Characteristics:** This is the fastest execution mode due to the high speed of register access and the absence of memory latency.

#### 3.2.2 Direct Addressing Mode

In direct addressing mode, the instruction itself contains the memory address where one of the operands is stored.

* **Example:** `ADD R1, [100H]`
* **Operational Flow:** The Control Unit extracts the address (100H) from the instruction and sends it to the Memory Unit. A single memory read cycle is performed to fetch the operand, which is then sent to the ALU.
* **Characteristics:** This mode is slower than register addressing because it requires one access to main memory.

#### 3.2.3 Indirect Addressing Mode

In indirect addressing mode, the instruction provides the address of a memory location that, in turn, contains the effective address of the actual operand.

* **Example:** `ADD R1, [[200H]]`
* **Operational Flow:** This mode requires a two-step memory access. First, the Control Unit reads the content at the initial address (200H) to retrieve the effective address. Second, it uses this effective address to perform another memory read to fetch the final operand.
* **Characteristics:** This is the slowest of the three modes as it necessitates two separate memory access cycles. Its utility lies in its ability to implement pointers, which allows for more dynamic and flexible data structures in software.

This functional specification defines what the ALU is capable of; the next section will detail the internal hardware design that makes these capabilities possible.

## 4.0 Internal Hardware Design and Implementation

The external functions of the ALU are realized through an internal structure composed of fundamental logic gates and combinational circuits. Each supported operation corresponds to a dedicated hardware block, and a selection mechanism is used to route the correct result to the output.

### 4.1 Arithmetic Unit

The Arithmetic Unit is responsible for addition and subtraction. Its implementation is based on the principles of binary arithmetic:

* **Addition:** The core of the addition circuit is constructed from eight Full Adders cascaded together to form an 8-bit ripple-carry adder, which processes the two 8-bit input operands.
* **Subtraction:** Subtraction is efficiently implemented using the 2's complement method ($A - B \text{ becomes } A + (\text{NOT } B) + 1$). This is realized elegantly through control logic. A single SUBTRACT signal from the Control Unit simultaneously instructs a bank of eight XOR gates to invert the second operand (B) and sets the initial carry-in (Cin) of the first Full Adder to 1. This performs the inversion and the "add one" step in a single, coordinated action using the same adder hardware.

### 4.2 Logic Unit

The Logic Unit handles the bitwise operations. The implementation is straightforward, with dedicated parallel logic for each function:

* **AND Operation:** This is implemented using eight parallel AND gates. Each gate takes one bit from each of the two 8-bit operands, performing the AND operation on a bit-by-bit basis.
* **OR Operation:** Similarly, this is implemented using eight parallel OR gates, which perform a bitwise OR across the two input operands.

### 4.3 Output Selection

With four separate hardware blocks producing four potential results (from ADD, SUB, AND, and OR), a mechanism is needed to select the final output. This is accomplished using an 8-bit wide 4-to-1 multiplexer. The multiplexer receives the four 8-bit results as its inputs. Based on two selection lines (control signals) from the Control Unit, it routes only the desired result to the ALU's final output bus.

The coordination of these internal units is managed by a precise set of control signals generated by the Control Unit, which are detailed next.

## 5.0 Control Unit Signals and Logic

The Control Unit is the director of all CPU activity, and its primary tools are control signals. These binary signals are sent to every component in the data path to dictate which operation the ALU should perform, whether registers or memory should be read from or written to, and how data should be routed through multiplexers.

### 5.1 ALU Operation Control

Two control bits, ALU_OP1 and ALU_OP0, are used to select which of the four ALU operations is performed. These bits serve as the selection lines for the 4-to-1 output multiplexer inside the ALU. The relationship between these signals and the selected operation is defined in the following truth table.

| ALU_OP1 | ALU_OP0 | Operation |
| :---: | :---: | :--- |
| 0 | 0 | ADD |
| 0 | 1 | SUB |
| 1 | 0 | AND |
| 1 | 1 | OR |

### 5.2 Memory and Register Control

Control signals are also required to manage data access from the Register File and the Memory Unit. The specific signals activated depend on the addressing mode of the current instruction.

| Addressing Mode | Reg_Read | Reg_Write | Mem_Read | Mem_Write |
| :--- | :---: | :---: | :---: | :---: |
| Register | 1 | 1 | 0 | 0 |
| Direct | 1 | 1 | 1 | 0 |
| Indirect | 1 | 1 | 1 (two cycles) | 0 |

**Analysis:** The logic of this control scheme reveals key design assumptions. The Reg_Read and Reg_Write signals are active (1) in all modes because the instructions considered (e.g., ADD Rx, ...) always source one operand from a register and always write the result back to that same register. Conversely, Mem_Write is always inactive (0) because none of these operations are designed to store their result in memory. The Mem_Read signal is activated only for Direct and Indirect modes, as these are the only modes that require fetching an operand from the Memory Unit, with the latter requiring two distinct cycles to resolve the pointer.

The following case study will demonstrate how these control signals work in concert to execute a complete instruction.

## 6.0 Instruction Execution Case Study

To synthesize the concepts of architecture, operations, and control signals, this section will trace the step-by-step execution of a single instruction from fetch to completion. This practical example illustrates the synchronized interaction between all system components.

**Example Instruction:** `ADD R1, [300H]`

This instruction directs the processor to add the contents of register R1 to the value stored at memory location 300H (Direct Addressing Mode) and store the result back into register R1.

1.  **Instruction Decode:** The Control Unit fetches the instruction from memory and decodes it, separating it into its constituent parts: the opcode (ADD) and the address field (300H). The opcode determines the required ALU operation and addressing mode.
2.  **Address Dispatch:** The Control Unit places the address 300H onto the system's address bus. Simultaneously, it activates the Mem_Read control signal by setting it to 1.
3.  **Operand Fetch:** The Memory Unit detects the address on the bus and the active Mem_Read signal. It retrieves the 8-bit data value stored at location 300H and places it onto the data bus.
4.  **Data Routing:** Under the direction of the Control Unit, the datapath is configured. The output of the Register File corresponding to R1 is routed to the ALU's A-input. A multiplexer at the ALU's B-input selects the data from the memory bus as the second operand.
5.  **ALU Execution:** The Control Unit sets the ALU control signals ALU_OP1 and ALU_OP0 to 00 to select the ADD operation. The ALU takes the values from inputs A and B and computes their sum.
6.  **Result Write-back:** The 8-bit result from the ALU's output bus is written to the Register File. The Control Unit activates the Reg_Write signal for register R1, causing the computed sum to be stored, overwriting its previous value.
7.  **Flag Update:** Based on the result of the addition, the ALU updates its status flags. The Zero (Z) flag is set if the result is zero, the Carry (C) flag is set if the addition produced a carry-out, and the Negative (N) flag is set if the most significant bit of the result is 1.

This step-by-step flow demonstrates the precise, clockwork-like coordination managed by the Control Unit to execute a single instruction.

## 7.0 Conclusion

This report has successfully detailed the design of a functional 8-bit ALU system, encompassing its architecture, operational specifications, and internal logic. The design is consistent with the core principles of computer organization, demonstrating how high-level instructions are translated into controlled electronic signals that manipulate data within a processor.

The key features of this ALU include its support for four fundamental arithmetic and logical operations and three essential addressing modes. The entire system is governed by a well-defined set of control signals from a central Control Unit, ensuring precise instruction execution. The modular nature of the design, which separates the arithmetic unit, logic unit, and control logic, makes it an excellent model for educational purposes and is highly suitable for practical simulation using tools like Logisim, Proteus, or Multisim. While this design provides a robust foundation, further extensions could include incorporating a barrel shifter for more complex logical operations or implementing a pipelined architecture to improve instruction throughput, representing logical next steps in processor design.

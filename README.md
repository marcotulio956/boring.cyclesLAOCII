# boring.cyclesLAOCII
### 3-cycles, 9bits instructions, 16bits data microprocessor with data inputs(DINs) for a course assignment and fpga testing.
## ISA and flag, IIIXXXYYY 9 bits
	000 - mv  regx, regy       rb[XXX] ← [rb[YYY]]  
		cc0:
			bus_sel <= 0;
			rb_read_sel <= 1;

			rb_write <= 1;
			ctrlu_instDone <= 1; 
	001 - mvi regx, #Immediate rb[XXX] ← Immediate 
		cc0:    
			bus_sel <= 1;
			
			rb_write <= 1;
			ctrlu_instDone <= 1;
	010 - add regx, regy       rb[XXX] ← [rb[XXX]]+[rb[YYY]]                     
	011 - sub regx, regy       rb[XXX] ← [rb[XXX]] − [rb[YYY]]
	100 - or  regx, regy       rb[XXX] ← [rb[XXX]] | [rb[YYY]]
	101 - slt regx, regy       If (rb[XXX] < rb[YYY]) [rb[XXX]] = 1 else [rb[XXX]] = 0
	110 - sll regx, regy       rb[XXX] = [rb[XXX]] << [rb[YYY]]
	111 - slr regx, regy       rb[XXX] = [rb[XXX]] >>[rb[YYY]] 
		cc0:
			bus_sel <= 0;
			rb_read_sel <= 0;
			
			a_reg_write <= 1;
			ctrlu_instDone <= 0; 
		cc1:
			bus_sel <= 0;
			rb_read_sel <= 1;

			alu_opcode <= 010(add) ou 011(sub) ou 110(or) ou 101(slt);
			g_reg_write <= 1;
		cc2:
			bus_sel <= 10;

			rb_write <= 1;
			ctrlu_instDone <= 1; 
## Outputs
![diagram](https://github.com/MnoZombie956/boring.cyclesLAOCII/blob/main/sim_waves.bmp?raw=true)
see also ```transcript_console.txt```
## Diagram
![diagram](https://github.com/MnoZombie956/boring.cyclesLAOCII/blob/main/diagram.png?raw=true)
## Program example 1 
| Binary    | DIN | CCs | op         | r0 r1 r2 r3 |
| --------- | --- | --- | ---------- | ----------- |
| 001000000 | 10  | 0   | MVI R0, #2 | 2 0 0 0 |
| 001001000 | 11  | 1   | MVI R1, #3 | 2 3 0 0 |
| 010001000 |     | 2   | ADD R1, R0 | 2 5 0 0 |
| 001010000 | 110 | 3   | MVI R2, #6 | 2 5 6 0 |
| 011010001 |     | 4   | SUB R2, R1 | 2 5 1 0 |
| 000011010 |     | 5   | MV  R3, R2 | 2 5 1 1 |
| 010000011 |     | 6   | ADD R0, R3 | 3 5 1 1 |
| 100001000 |     | 7   | OR  R1, R0 | 3 7 1 1 |
| 011001000 |     | 8   | SUB R1, R0 | 3 4 1 1 |
| 010001011 |     | 9   | ADD R1, R3 | 3 5 1 1 |
| 110001011 |     | 10  | SLL R1, R3 | 1 A 1 1 |
| 111001011 |     | 11  | SRL R1, R3 | 1 5 1 1 |
| 001000000 |   0 | 12  | MVI R0, #0 | 0 5 1 1 |
| 101000001 |     | 13  | SLT R0, R1 | 1 5 1 1 |
| 101001001 |     | 14  | SLT R1, R1 | 1 0 1 1 |
| 010000011 |     | 15  | ADD R0, R3 | 2 0 1 1 | 
| 010001010 |     | 16  | ADD R1, R2 | 2 1 1 1 |

## Program example 2 - Risc Cycles: LOOP
| op |
| -- |
| MVI R2, #1 | 
| MVI R4, #10 |
| MV R5,R7 |
| SUB R4, R2 | 
| MVNZ  R7,R5 | 

## Program example 3 - Risc Cycles: LOGIC

| op | R0 R1 R2 R3 |
| -- | --- |
| MVI R0, #2 | 2 0 0 0 |
| MVI R1, #3 | 2 3 0 0 |
| ADD R1, R0 | 2 5 0 0 |
| MVI R2, #6 | 2 5 6 0 |
| SUB R2, R1 | 2 5 1 0 |
| MV R3, R2 | 2 5 1 1 |
| OR R0, R3 | 3 5 1 1 |
| SLL R0, R3 | 6 5 1 1 |
| SRL R0, R3 | 3 5 1 1 |
| SLT R0, R1 | 1 5 1 1 |
| SLT R0, R3 | 0 5 1 1 |
| MVI R3, #3 | 0 5 1 3 |
| ADD R0, R3 | 3 5 1 3 |
| LD R2, R3 | 3 5 4 3 |
| ADD R2, R3 | 3 5 7 3 |
| ST R2, R0 | 3 5 7 3 |
| LD R0, R0 | 7 5 7 3 |
| SUB R0, R3 |4 5 7 3 |
| MVI R0, #0 |0 5 7 3 |
| ADD R0, R0 |0 5 7 3 |
| MVNZ R0, R2 | 0 5 7 3 |
| SUB R3, R0 | 0 5 7 3 |
| MVNZ R2, R3 | 0 5 3 3 |



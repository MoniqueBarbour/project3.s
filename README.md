# project3.s
.data
	data: .space 1001
	output: .asciiz "\n"
	notvalid: .asciiz "NaN"
	comma: .asciiz ","
.text
# reads user input
main :
	li $v0,8	
	la $a0,data	
	li $a1, 1001	
	syscall
# jumps to label 
jal SubprogramA 
continue1:
	j print 
SubprogramA:
# creates spaces in the stack
	sub $sp, $sp,4 #creates spaces in the stack
	sw $a0, 0($sp) #stores input into the stack
	lw $t0, 0($sp) # stores the input into $t0
	addi $sp,$sp,4 # moves the stack pointer up
	move $t6, $t0 # stores the begining of the input into $t6

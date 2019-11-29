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
start:
# used to check for space or tabs within the input 
	li $t2,0 
	li $t7, -1 #used for invaild input
	lb $s0, ($t0) # loads the bit that $t0 is pointing to
	beq $s0, 0, insubstring# check if the bit is null
	beq $s0, 10, insubstring #checks if the bit is a new line 
# check if bit is a comma 
	beq $s0, 44, invalidloop #check if bit is a comma
	beq $s0, 9, skip # checks if the bit is a tabs character 
	beq $s0, 32, skip #checks if the bit is a space character
	move $t6, $t0 #store the first non-space/tab character
	j loop # jumps to the beginning of the loop function
# move the $t0 to the next element of the array
skip:
	addi $t0,$t0,1 #move the $t0 to the next element of the array
	j start 
loop:
	
	
	lb $s0, ($t0) # loads the bit that $t0 is pointing to
	beq $s0, 0,next# check if the bit is null
# checks if the bit is a new line 
	beq $s0, 10, next #checks if the bit is a new line 	
	addi $t0,$t0,1 #move the $t0 to the next element of the array	
	beq $s0, 44, substring #check if bit is a comma
# checks to see if there were any spaces or tabs in between valid chars
check:
	bgt $t2,0,invalidloop 
	beq $s0, 9,  gap #checks to see if there is a tab characters
	beq $s0, 32, gap #checks to see if there is a space character
	ble $s0, 47, invalidloop # checks to see if the ascii less than 48
	ble $s0, 57, vaild # checks to see if the ascii less than 57(integers)
# checks to see if ascii less than 64
	ble $s0, 64, invalidloop # checks to see if the ascii less than 64
	ble $s0, 82, vaild	# checks to see if the ascii less than 84(capital letters)
	ble $s0, 96, invalidloop # checks to see if the ascii less than 96
	ble $s0, 114, vaild 	# checks to see if the ascii less than 116(lowercase letters)
	bge $s0, 114, invalidloop # checks to see if the ascii greater than 114

# keeps track of spaces/tabs

gap:
	addi $t2,$t2,-1 #keeps track of spaces/tabs
	j loop

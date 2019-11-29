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
	sub $sp, $sp,4 
	sw $a0, 0($sp) 
	lw $t0, 0($sp) 
	addi $sp,$sp,4 
	move $t6, $t0 
start:
# used to check for space or tabs within the input 
	li $t2,0 
	li $t7, -1 
	lb $s0, ($t0) 
	beq $s0, 0, insubstring
	beq $s0, 10, insubstring 
# check if bit is a comma 
	beq $s0, 44, invalidloop 
	beq $s0, 9, skip 
	beq $s0, 32, skip 
	move $t6, $t0 
	j loop 
# move the $t0 to the next element of the array
skip:
	addi $t0,$t0,1
	j start 
loop:
	
	
	lb $s0, ($t0) 
	beq $s0, 0,next
# checks if the bit is a new line 
	beq $s0, 10, next 	
	addi $t0,$t0,1 	
	beq $s0, 44, substring 
# checks to see if there were any spaces or tabs in between valid chars
check:
	bgt $t2,0,invalidloop 
	beq $s0, 9,  gap 
	beq $s0, 32, gap 
	ble $s0, 47, invalidloop 
	ble $s0, 57, vaild 
# checks to see if ascii less than 64
	ble $s0, 64, invalidloop 
	ble $s0, 82, vaild	
	ble $s0, 96, invalidloop 
	ble $s0, 114, vaild 	
	bge $s0, 114, invalidloop 

# keeps track of spaces/tabs

gap:
	addi $t2,$t2,-1 
	j loop

vaild:
	addi $t3, $t3,1 
	mul $t2,$t2,$t7 
# jumps to the beginning of loop
	j loop 

invalidloop:
	
	lb $s0, ($t0) 
	beq $s0, 0, insubstring
	beq $s0, 10, insubstring  
# move the $t0 to next element 
	addi $t0,$t0,1 	
	beq $s0, 44, insubstring 
	
	
	j invalidloop 
insubstring:
# keeps track of the amount substring 
	addi $t1,$t1,1 	
	sub $sp, $sp,4
	
	sw $t7, 0($sp) 
	
	move $t6,$t0  
	lb $s0, ($t0) 
# check if the bit is null
	beq $s0, 0, continue1
	beq $s0, 10, continue1  
	beq $s0,44, invalidloop 
	li $t3,0 
	li $t2,0 
	j start


# checks if there is a space 

substring:
	mul $t2,$t2,$t7 
next:
	bgt $t2,0,insubstring 
# checks to see if there are more than 4 chars 
	bge $t3,5,insubstring 
	addi $t1,$t1,1  	
	sub $sp, $sp,4 
	sw $t6, 0($sp) 
	move $t6,$t0  
# loads what was in the stack 
	lw $t4,0($sp) 
	li $s1,0 
	jal SubprogramB
	lb $s0, ($t0) 
	beq $s0, 0, continue1 
# checks if the bit is a new line 
	beq $s0, 10, continue1  
	beq $s0,44, invalidloop 
	li $t2,0 
	j start



# check how many chars left 
SubprogramB:
	beq $t3,0,finish  
	addi $t3,$t3,-1 
	lb $s0, ($t4) 
	
	addi $t4,$t4,1	
	j SubprogramC 
# stores the converted number 
continue:
	
	sw $s1,0($sp)	
	j SubprogramB

SubprogramC:
	li $t9, 1	
# sorts the bit to the appropraite function
	ble $s0, 57, num 
	ble $s0, 82, upper
	ble $s0, 114, lower
num:
	
	sub $s0, $s0, 48	
# if no chars left exponent is zero
	beq $t3, 0, combine	
	li $t9, 30		
	j exp

upper:
	
	sub $s0, $s0, 55 
	beq $t3, 0, combine 
	li $t9, 30
	j exp
# converts lowercase bits 
lower:
	
	sub $s0, $s0, 87 
	beq $t3, 0, combine 
	li $t9, 30
	j exp
exp:
	
	ble $t8, 1, combine	
	mul $t9, $t9, 30 	
# decreasing the exponent 
	addi $t8, $t8, -1	
	j exp
combine:
	mul $s2, $t9, $s0	
	
	add $s1,$s1,$s2		 
	j continue

# jumps back to substring 

	
finish : jr $ra	



print:
	mul $t1,$t1,4 
	add $sp, $sp $t1 
		
done:	
	
	
	sub $t1, $t1,4	
	sub $sp,$sp,4 

# storing that element into $s7
	lw $s7, 0($sp)	
	
	beq $s7,-1,invalidprint 
	
	
	li $v0, 1
	lw $a0, 0($sp) 
	syscall
# if there are elements left terminate program 
com:
	beq $t1, 0,Exit 
	li $v0, 4
	la $a0, comma 
	syscall
	j done
invalidprint:
# prints a nonvalid input
	li $v0, 4
	
	la $a0, notvalid 
	syscall	
	j com 
	
	
Exit:
	li $v0, 10
	syscall

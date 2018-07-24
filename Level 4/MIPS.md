# MIPS

### Analyzing the code
Here is the code somehow cleaned up and commented:

```asm
.data
fail:
  .asciiz  "Not quite! Keep trying.\n"
success:
  .asciiz  "Good job! Submit your input value as the flag.\n"

.text
main:
  li $v0, 5                 ;mov read-int sys call
  syscall                   ;SPIM read_int system call (Note: decimal, not hex)
  move    $4,$v0            ;mov whatever number was entered in $a0
  addiu   $sp,$sp,-32 	    ;push back stack by 32 bytes
  sw      $31,28($sp)       ;store whatever is in [sp+4] in $ra
  sw      $16,24($sp)       ;store we is in [sp+8] in $s0
  
  li      $6,-16777216      ;store 0xffffffffff000000 in $a2
  and     $6,$4,$6          ;compare $a0 (input number) and $a2 (constant), put result in $a2
  
  li      $16,16711680      ;store 0xff0000 in $s0
  and     $16,$4,$16        ;compare $a0 (input number) and $s0 (constant) and put the result in $s0
  
  andi    $7,$4,0xff00      ;bitwise and between $a0 (input number) and fixed val, put result in $a3
  andi    $4,$4,0x00ff      ;bitwise and between $a0 (input number) and fixed val, put result in $a0
  sra     $3,$6,24          ;shift $a2 right 24 bits and put result in $v1
  move    $2,$0             ;mov zero in $v0 (so zero out the value)
 

$L3: #this is a loop
  slt     $5,$2,13          ;if $v0 is less than 13, set $a1 is 1
  beq     $5,$0,$L2         ;if $a1 = 0: goto L2
  addiu   $2,$2,1           ;increase $v0 by 1
  b       $L3               ;restart L3
  addiu   $3,$3,-13         ;decrease $v1 by 13  --> branch delay?  => 13*13 = 169 so $v1 = $v1-169
  
$L2:
  addiu   $3,$3,-6          ;decrease $v1 by 6 --> $v1 = $v1 - 169 - 6
  sll     $5,$3,24          ;$a1 = $v1*(2^24)
  sra     $16,$16,16        ;shift $s0 by 0x10
  addiu   $2,$16,-81        ;decrease $s0 by 81 and put result in $v0 --> $s0 is now 
  sll     $8,$2,6           ;$t0 = $v0*(2^6)
  sll     $3,$2,8           ;$v1 = $v0*(2^8)
  subu    $3,$3,$8          ;substract $t0 from $v1 and put result in $v1
  subu    $3,$2,$3          ;substract $v1 from $v0 and put result in $v1
  sra     $7,$7,8           ;shift right $a3 by 8
  sll     $2,$4,1           ;$v0 = $a1*2
  addiu   $2,$2,3           ;increase $v0 by 3
  bne     $7,$2,$loader_fct ;if $a3 != $v0 jump to $loader_fct
  sll     $3,$3,16          ;shift $v1 by 16
  b       $final_eval       ;jump to final_eval
  li      $2,94
  
$loader_fct:
  li      $2,165            ;load 0xa5 into $v0
  
$final_eval:
  addiu   $2,$2,-94         ;$v0 = $v0 - 94 --> either v0 is now 0 (a) or v0 = 71 (b)
  sll     $2,$2,8           ;$v0 = $v0 * 2*8 --> 0 (a) or 0x4700 (b)
  srl     $6,$6,24          ;shift right $a2 by 0x18 // $a2 = $a2/(2^24)
  subu    $16,$6,$16        ;$s0 = $a2 - $s0
  subu    $4,$4,$16         ;$a0 = $a0 - s0
  addu    $3,$5,$3          ;$v1 = $a1 + $v1
  addu    $3,$2,$3          ;$v1 = $v0 + $v1 --> (a) v1 = v1
  addu    $16,$4,$3         ;$s0 = $a0 + $v1
  bne     $16,$0,$L5        ;if $s0 != 0 jump to $fail_end
  li $v0, 4                 ;set $v0 to 4
  la $a0, success           ;BIG SHAQ WIN
  syscall                   ;SPIM print_string system call
  lw      $28,16($sp)
  b       $return_end
  move    $2,$16
  
$fail:_end
  li $v0, 4
  la $a0, fail
  syscall                  # SPIM print_string system call
  lw      $28,16($sp)
  move    $2,$16
 
$return_end:
  lw      $31,28($sp)
  lw      $16,24($sp)
  j       $31
;  addiu   $sp,$sp,32
```

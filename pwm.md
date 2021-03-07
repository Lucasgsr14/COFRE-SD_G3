.NOLIST
.LIST
;
; -------- Register -------------------------
.def rmp = R16 ; multi purpose register
.def rmp1 = R17
;
; -------- Program --------------------------
	; Init output pin OC0A
	sbi DDRD,DDD6 ; OC0A as output
	sbi DDRD,DDD5 ; OC0B as output
	; PWM compare value to timer 0
	ldi rmp, 30*256/100 ; 20% intensity
	out OCR0A,rmp ; to compare match port A
	; Timer 0 in Fast PWM mode, output A low at cycle start
	ldi rmp1,(1<<COM0A1)|(1<<COM0B1)|(1<<WGM01)|(1<<WGM00)
	out TCCR0A,rmp ; to timer control port A
	; Start Timer 0 with prescaler = 1
	ldi rmp1,1<<CS00 ; Prescaler = 1
	out TCCR0B,rmp1 ; to timer control port B
	; Enable sleep mode
	ldi rmp1,1<<SE ; Sleep enable, mode idle
	out MCUCR,rmp ; to Universal control port
Loop:
	sleep ; go to sleep
	nop ; in case of wakeup
	rjmp Loop ; again to sleep
;
; End of source code
;
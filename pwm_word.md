//.NOLIST
//.LIST
;
; -------- Register -------------------------
.def rmp = R16 ; multi purpose register
.def rmp1 = R17
;
; -------- Program --------------------------
pwm_mode:
	; Init output pin OC0A
	sbi DDRD,DDD6 ; OC0A as output
	sbi DDRD,DDD5 ; OC0B as output
	; PWM compare value to timer 0
	; Replace with your application code
sbi DDRD,DDD6 ; red
sbi DDRB,DDB3 ; blue
sbi DDRD,DDD5 ; green
ldi r17, 0b00000000;

pwm_red: 
ldi r16, 0b01000000;
out PORTB, r17;
out PORTD, r16;
// rjmp pwm_red;

pwm_blue:
ldi r16, 0b00001000;
out PORTD, r17;
out PORTB, r16;
// rjmp pwm_blue;

pwm_green:
ldi r16, 0b00100000;
out PORTB, r17;
out PORTD, r16;
// rjmp pwm_green;

pwm_orange: 
ldi r16, 0b01000000;
out PORTB, r17;
out PORTD, r16;
ldi rmp, 10*256/100 ; 20% intensity
out OCR0B,rmp ; to compare match port A
	; Timer 0 in Fast PWM mode, output A low at cycle start
ldi rmp1,(1<<COM0B1)|(1<<WGM01)|(1<<WGM00)
out TCCR0A,rmp1 ; to timer control port A
; Start Timer 0 with prescaler = 1
ldi rmp1,1<<CS00 ; Prescaler = 1
out TCCR0B,rmp1 ; to timer control port B
; Enable sleep mode
rjmp pwm_orange;

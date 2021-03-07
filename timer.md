# CÓDIGO ASSEMBLY COMENTADO SOBRE O TIMER 16 BITS DO uC AVR ATMEGA328P

```
;
; timer2.asm
;
; Created: 07/03/2021 10:07:57
; Author : lucas
;


;
; timer.asm
;
; Created: 06/03/2021 14:42:28
; Author : lucas
;	

;modo timer normal
; Replace with your application code
.org 0x00    ; seta o início da memoria de instrução  
	rjmp main 

main:
	ldi r17, 10				; carrega o registrador r17 com o número a ser comparado
	ldi r20, 255			; setando o registrador 20 para ser usado como habilitador do loop infinito
	ldi r19, (1 << CS10)	; carrega o registrador r19 setando os bits CS10 (modo normal)
	sts TCCR1B, r19         ; carrega o registrador TIMSK1 com os bits CS10 setado (sem divisão por prescaler)(só para simulação)
	rjmp infinito           ; vai para a função de loop infinito até que seja dado clear no bit do r20
  
infinito:
	sbrs r20, 0b00000001    ; checa se o habilitador do loop esta setado
	rjmp main               ; salto relativo para a função main caso não esteja
	lds r15,TCNT1L			; carrega o low do contador
	lds r16,TCNT1H			; carrega o high do contador
	cp r15, r17				; nesse caso compara o high com 10
	brge TIM1_COMPA			; vai para a branch TIM1_COMPA caso a comparação seja maior igual
	rjmp infinito           ; salto relativo para a função infinito se a comparação for menor

TIM1_COMPA:
	ldi r19, (0 << CS10)	; carrega a parada da contagem
	sts TCCR1B, r19			; para a contagem
	ldi r22, 0x00			; carrega o zero
	sts TCNT1L, r22			; zera registrador low do contador
	sts TCNT1H, r22			; zera registrador high do contador
	ldi r20, 0				; clear no bit de loop infinito
	rjmp infinito           ; retorno da interrupção

```  

# Observações

* Caso se deseje aplicar um novo clock e nova divisão do mesmo pelo prescaler vide equação abaixo:

![image](https://user-images.githubusercontent.com/32770973/109967701-8026cb80-7cd0-11eb-9204-34362a3c68c2.png)

* Explicação dos mnemônicos utilizados

![image](https://user-images.githubusercontent.com/32770973/109995530-22ed4300-7ced-11eb-8ce2-70956380313d.png)

* Mnemônicos de complementares

![image](https://user-images.githubusercontent.com/32770973/109996915-6f854e00-7cee-11eb-91bb-3d17986849f3.png)

General purpose I/O registers within the address range 0x00 - 0x1F are directly bit-accessible using 
the SBI, CBI, SBIS, and SBIC instructions

For I/O registers located in extended I/O map, “IN”, “OUT”, “SBIS”, “SBIC”, “CBI”, and “SBI” instructions must
be replaced with instructions that allow access to extended I/O. Typically “LDS” and “STS” combined with
“SBRS”, “SBRC”, “SBR”, and “CBR”.

https://www.youtube.com/watch?v=7sgyyA92Pqs&ab_channel=JoelCastillo

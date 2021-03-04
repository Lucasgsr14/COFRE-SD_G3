# CÓDIGO ASSEMBLY COMENTADO SOBRE O TIMER 16 BITS DO uC AVR ATMEGA328P

```
.org 0x00    ; seta o início da memoria de instrução  
  rjmp main 
```

![image](https://user-images.githubusercontent.com/32770973/109974639-88830480-7cd8-11eb-827a-2021ad9d82b3.png)

```
.org 0x16    ; definição de onde pular na interrupção
  rjmp interrupcao

main:
  ldi r16, (1 << WGM12)   ; carrega o registrador r16 setando o bit WGM12
  out TCCR1B, r16         ; carrega o registrador TCCR1B com o bit WGM12 setado (modo CTC)
  ldi r17, 2              ; carrega o registrador r17 com 2
  out OCR1A, r17          ; carrega o registrador OCR1A com 2 (comparado com o timer)
  ldi r18, (1 << OCIE1A)  ; carrega o registrador r18 setando o bit OCIE1A
  out TIMSK1, r18         ; carrega o registrador TIMSK1 com o bit OCIE1A setado (sinaliza comparação do timer com o registrador OCR1A)
  sei                     ; habilita os eventos de interrupção
  ldi r20, 0x01           ; setando um bit para ser usado de habilitador do loop infinito
  ldi r19, (1 << CS12) | (1 << CS10) ; carrega o registrador r19 setando os bits CS12 e CS10
  out TCCR1B, r19         ; carrega o registrador TIMSK1 com os bits CS12 e CS10 setados
  rjmp infinito           ; vai para a função de loop infinito até que seja dado clear no bit do r20
  
infinito:
  sbrs r20, 0x01          ; checa se o bit esta setado
  rjmp main               ; salto relativo para a função main
  nop                     ; gasta um ciclo de máquina
  rjmp infinito           ; salto relativo para a função infinito

interrupcao:
  cbr TCCR1B, CS12        ; clear no bit CS12
  cbr TCCR1B, CS10        ; clear no bit CS10
  cbr r20, 0x01           ; clear no bit de loop infinito
  reti                    ; retorno da interrupção
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

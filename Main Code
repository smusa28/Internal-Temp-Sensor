/* 
 * File:   main_MajorProject.c
 * Author: sharonmusa
 *
 * Created on December 6, 2020, 10:12 PM
 */

#include <xc.h>
#include <avr/interrupt.h>
#include <stdint.h>


#define LED PORTB        
#define Button PINB


unsigned int temp_value;
volatile unsigned long delay;


void LED_flash(void)
{
    volatile unsigned long delay;

    delay = 300000;        //delay of about a second
    
    while (delay > 0) {       //loop to decrement out delay value
        delay = delay - 1;
    }
}
    

int main(void) 
{ 
      
    asm("SBI %0, 5  \n\t"   //make LED as output
    "CBI %1, 7"             //setting the push button as input 
    : :"I" (_SFR_IO_ADDR(DDRB)),"I" (_SFR_IO_ADDR(DDRB)): ); 
    
    
    //LED &= ~(1<<5);        //turn our LED off
    asm("CBI %0, 5"
    : :"I"(_SFR_IO_ADDR(PORTB)): );
    
    PCMSK0 |= (1<<PCINT7);      //enable pin change interrupt for PORTB7
    PCICR |= (1<<PCIE0);        //pin change interrupt 0 is enabled
    asm("SEI");                //enable global interrupts
    
    ADMUX = 0b11001000;     // ADC Setup: enable temp sensor, right justified and 1.1V volatage reference.
    ADCSRA = 0b10000111;    //Enable ADC and prescaler 128
   
    ADCSRA |= (1<<ADSC);

    while ((ADCSRA & (1<<ADSC)) != 0);          //waiting for conversion

   
    while (1){
        ADCSRA |= (1<<ADSC);       //start a new conversion on channel 8

        while ((ADCSRA & (1<<ADSC)) != 0);   //waiting for conversion

        temp_value = (ADC - 247)/1.22;         //compensation (in degree celsius)
        
        if (temp_value > 30) {             //check if out temp is high

    //toggle the LED using in-line assembly
   asm("LDI R17, 0x20 \n\t"
       "IN R24, %0 \n\t"
       "EOR R24, R17 \n\t"
       "OUT %1, R24"
       : :"I" (_SFR_IO_ADDR(PORTB)),"I" (_SFR_IO_ADDR(PORTB)):"r16","r24");
   
        LED_flash();  //delay
        temp_value = (ADC - 247)/1.22;
      
       }
        else {
            //led stays off 
           asm("CBI %0, 5"
           : :"I"(_SFR_IO_ADDR(PORTB)): );
        }
  }
}

ISR(PCINT0_vect) {
    if (!(Button & (1<<PINB7)))    //if the button is pushed
    {
    //turn our LED off
    asm("CBI %0, 5"
    : :"I"(_SFR_IO_ADDR(PORTB)): );
         
        unsigned int variable = 15;
        while (variable > 0){        //loop to increase delay time. 
            LED_flash();
            variable = variable - 1;
            
}
    }
    
}


# Self-regulating-battery-pack-for-ev
PIC CCS COMPILER PROGRAM

#include <16F877A.h>
#device ADC=10

#FUSES NOWDT                    //No Watch Dog Timer
#FUSES PUT                      //Power Up Timer
#FUSES NOBROWNOUT               //No brownout reset
#FUSES NOLVP                    //No low voltage prgming, B3(PIC16) or B5(PIC18) used for I/O

#use delay(crystal=4MHz)
#define LCD_ENABLE_PIN PIN_D2
#define LCD_RS_PIN PIN_D0
#define LCD_RW_PIN PIN_D1
#define LCD_DATA4 PIN_D4
#define LCD_DATA5 PIN_D5
#define LCD_DATA6 PIN_D6
#define LCD_DATA7 PIN_D7

#include <lcd.c>

void main()
{
   setup_adc_ports(ALL_ANALOG);
   setup_adc(ADC_CLOCK_INTERNAL);
   float temp1,temp2;

   lcd_init();

   while(TRUE)
   {
      /*set_adc_channel(0);
      v=read_adc();
      lcd_gotoxy(1,1);
      printf(lcd_putc,"%f v",v);
      //voltage meter
      if(v>=512)
      {
      output_high(pin_b0);
      }
      else if(v<512)
      {
      output_low(pin_b0);
      }*/
      //Temperature sensor 1 for monitoring battery with the cladding
      set_adc_channel(1);
      temp1=read_adc();
      lcd_gotoxy(1,2);
      int data=(temp1*500)/1023;
      printf(lcd_putc,"%d v",data);
      if(data>=50)
      {
      output_high(pin_b1);//RAD FAN ON
      output_low(pin_b2);
      }
      else if(data<50)
      {
      output_low(pin_b1);//RAD FAN OFF
      output_low(pin_b2);
      }
      //Temperature sensor 2 for peltier over heating
      set_adc_channel(2);
      temp2=read_adc();
      lcd_gotoxy(17,1);
      int d2=(temp2/1023)*500;
      printf(lcd_putc,"%d v",d2);
      if((data<=20)&&(d2<=20))//PELTIER & FAN ON STATE
      {
      output_high(pin_b3);
      output_low(pin_b4);
      output_high(pin_b5);
      output_low(pin_b6);
      }
      else if((data>=35)&&(d2>=30))//FOR REACHING OPTIMUM TEMP
      {
      output_low(pin_b3);
      output_low(pin_b4);
      delay_ms(1000);
      output_low(pin_b5);
      output_low(pin_b6);
      }
      else if(d2>=55)
      {
      output_low(pin_b3);
      output_low(pin_b4);
      output_low(pin_b5);
      output_low(pin_b6);
      }
   }

}

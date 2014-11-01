Laboratorio2-SPI
================
Codigo1
================
// CABECERA 

#include<18f4550.h>
#FUSES INTHS,MCLR,PUT,BROWNOUT
#use delay(INTERNAL=8MHZ)
#include<LCD.c>

// VARIABLES GLOBALES //////////////////////////////////////////////////////////////

int dato=0;  // hemos definido la variable dato como global


// PRINCIPAL 

void main()
{
 SETUP_SPI(SPI_MASTER|SPI_L_TO_H|SPI_CLK_DIV_16); // configuracion del Pic1 como maestro
 lcd_init();
 printf(lcd_putc,"Pulse Boton 1\nPara Enviar");
 while(true)
 {
  if(input(PIN_A0)==1)
  {
   while(input(PIN_A0)==1){}
   output_bit(PIN_A4,1);  /* activamos el pin RA4 en True, ya que está puenteado con RA5 */
   printf(lcd_putc,"\fEnviando");
   SPI_WRITE(dato);
   delay_ms(50);
   SETUP_SPI(SPI_SLAVE|SPI_L_TO_H|SPI_CLK_DIV_16); /* configuramos el Pic1 como esclavo */
   delay_ms(50);
   lcd_gotoxy(1,2);
   printf(lcd_putc,"Dato par: %d",dato); // muestra el dato enviado
   output_bit(PIN_A4,0);/* cambiamos el pin RA4 a False, ya que está puenteado con RA5 */
  }
  if(SPI_DATA_IS_IN()) // si recibe un dato a través de SPI
  {
   printf(lcd_putc,"\fRecibido");
   dato=SPI_READ(); // almacenamos en la variable dato la lectura
   delay_ms(50);
   SETUP_SPI(SPI_MASTER|SPI_L_TO_H|SPI_CLK_DIV_16);// configuracion del Pic1 como maestro de nuevo
   delay_ms(50);
   lcd_gotoxy(1,2);
   printf(lcd_putc,"Dato impar: %d",dato); // muestra el dato recibido
   dato++;
   output_bit(PIN_A4,1); /* activamos el pin RA4 de nuevo a True, ya que está puenteado con RA5 */  }
  delay_ms(500);
 }
}

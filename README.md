# printf via UART
Sometimes you just want good old fashioned serial monitor prints like "Serial.write()" on arduino or printf in C.
In order to use printf() on a STM32 uController we need to change how printf() works.
We do it by redefining the function _fputc() (printf() calls _fputc() in order to print every character) to sending the characters via UART. This is done by the following code.

``` c
#ifdef __GNUC__
	#define PUTCHAR_PROTOTYPE int __io_putchar(int ch)
#else
	#define PUTCHAR_PROTOTYPE int fputc(int ch, FILE *f)
#endif
PUTCHAR_PROTOTYPE{
	HAL_UART_Transmit(&huart1, (uint8_t *) &ch, 1, 0xFFFF);
	return ch;
}
```
Rember to user the correct UART handler (huartX).
Now we can use printf() to send messages to a serial monitor like so:
``` c
int age = 22;
printf("My age is: %d\r\n", age);
```

### Hardware Setup
In order to recieve the data beeing sent out by the uController via UART we need a UART to USB serial adapter.
These can by found all over the internet by searching on "USB to UART". THe CP210X chip from Silicon labs is common chip, it is found on cheap board from aliexpres and from adaftruit.
![alt text](https://cdn-shop.adafruit.com/1200x900/3309-00.jpg "Logo Title Text 1")
Connect a USB cable from your computer to the "USB to UART board".
Connect the uController and "USB to UART board" like shown below:
| uController | USB to UART board |
| ------------- |:-------------:|
| TX            | RX            |
| RX            | TX            |
| GND           | GND           |
To find the RX, TX pins on the uController you have check how you have setup the uController in STM32CubeMX or the code.

### Example
A basic example can be found in the main.c file.
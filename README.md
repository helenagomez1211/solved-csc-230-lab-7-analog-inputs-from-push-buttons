Download Link: https://assignmentchef.com/product/solved-csc-230-lab-7-analog-inputs-from-push-buttons
<br>
The five pushbuttons (excluding the RST reset button) on the LCD shield are connected to one pin on the AVR board, which is designed to read analog input. Analog is a continuously variable signal, but our system is digital (discrete). Thus, we need to use the built-in Analog to Digital Converter (ADC) to transform the continuous analog value into a discrete one expressed as a binary number. ADC samples the signal (in this case voltage) and converts the analog value to a 10-bit digital value, as shown below:

Several special-purpose registers must be correctly configured for ADC to digitize an analog signal. These are the Control and Status Registers A and B (ADCSRA, ADCSRB) and the Multiplexer Selection Register (ADMUX). ADC can handle up to 8 channels, it uses the multiplexer to select the appropriate channel (pin on the AVR board). When the conversion is complete, ADC stores the 10-bit binary result in two 8-bit data registers ADCL and ADCH.

For the purposes of this lab (and the course), we will not use the ADCRB register, so only the ADCSRA and ADMUX need to be configured. Following is a brief description of these registers; more details are available in the ATmega2560 datasheet (in resources on conneX).

<strong>ADCSRA</strong>:

<ul>

 <li>When set, the <strong>ADEN</strong> bit enables the ADC, which means it’s ready to convert.</li>

 <li>When set, the <strong>ADSC</strong> bit, tells ADC to start conversion “now!”.</li>

 <li><strong>ADPS0</strong>, <strong>ADPS1</strong>, and <strong>ADPS2</strong> are pre-scalar bits. They reference the ATMEGA2560 clock to set the ADC clock.</li>

</ul>

<strong>ADMUX</strong>:

<ul>

 <li><strong>REFS0</strong> and <strong>REFS1</strong> select the voltage reference for the ADC.</li>

 <li><strong>ADLAR</strong> bit affects the presentation of the ADC conversion result in the ADC Data Register. When this bit is 1, the result is left adjusted. Otherwise, it is right adjusted.</li>

 <li><strong>MUX0</strong>, <strong>MUX1</strong>, <strong>MUX2</strong>, <strong>MUX3</strong>, and <strong>MUX4</strong> select the input channel.</li>

</ul>

The ADC registers are available in the I/O view of Atmel Studio 7.0 debugger:

Before using the ADC, it must be initialized. This is done once, in the beginning of the program, as follows:

ADCSRA: 0x87 = 0b 1000 0111 (means enable the ADC and slow down the ADC clock from 16mHz to ~125kHz, 16mHz/128).

ADMUX: 0x40 = 0b 0100 0000 (means use the AVCC with external capacitor at AREF pin and use the right adjustment since ADLAR is 0, ADC0 channel is used). The reason for the right adjustment is that the analog signal is digitized to a 10 bits value. ADCH:ADCL is 2 bytes (16bits). The digitized value is either left-adjusted or right-adjusted.

After the ADC is initialized (once), it can be used multiple times. When a conversion is needed, set (turn on) the ADSC bit in the ADCSRA register. When this bit is set to 1, the ADC will immediately start converting the analog signal into digital format. This bit will continue to remain 1 as long as the conversion is in progress. When the conversion completes, the ADC will clear this bit to 0. At this point, the 10-bit digital value is available in the ADCH:ADCL register-pair. The code below does exactly that: initiates the conversion, waits for it to complete, and reads the 10-bit result into the registers r16 and r17.

<ol>

 <li><strong>Additional Resources on conneX </strong></li>

</ol>

The file LCDKeypad_Shield_Schematic.pdf contains the wiring diagram of the buttons. <strong>Caution</strong>: different AVR boards have different hardware configuration, refer to the LCD_Shield_Buttons.pdf for more information.

<ul>

 <li><strong>Exercises:</strong></li>

</ul>

<ol>

 <li>Create a new project and replace the starter-program in main.asm with the contents of button.asm file available on conneX. Build and upload the program to the AVR board. Notice that some buttons will toggle the LED, but some don’t. Modify the code so that it detects ANY button press (except the RST reset button).</li>

 <li>Modify the check_button function to detect each button and return a unique value for each. Then, modify the main program so that each button turns on a different LED.

  <ol start="24">

   <li>Notice that the function returns a value corresponding to which button was pressed via register r24. Your task is to write the necessary if-else control flow with the constraints described in the LCD_Shield_Buttons.pdf file so that r24 contains a value 0 when no button was pressed, 1 for btnRIGHT, 2 for btnUP, 3 for btnDOWN, 4 for btnLEFT, 5 for btnSELECT.</li>

   <li>Replace the code in the main program loop (not in the check_button function), with the code to turn on the corresponding LED when each button is pressed and to turn off all LEDs when no button is pressed. It’s up to you to determine which button corresponds to which LED.</li>

  </ol></li>

</ol>

<ol start="3">

 <li>Modify the check_button function so that it protects all the necessary registers and returns the value corresponding to which button was pressed via the stack. Then modify the main program to reserve the space on the stack and retrieve the returned value from the stack.</li>

 <li>Extend the main program from exercise 1 to count the number of button presses and output the 6 least significant bits of the binary representation of this count on the LEDs.</li>

</ol>

<strong> </strong>
# Blockdiagram:-
![image](https://user-images.githubusercontent.com/101261412/164521482-c3cfc686-f291-4390-b3ca-96622d34c095.png)
# Explanation Of The Blockdiagram :-
In circuit PORTA of ATMEGA32 is connected to data port of LCD. Here one should remember to disable the JTAG communication in PORTC to ATMEGA by changing the fuse bytes, if one wants to use the PORTC as a normal communication port. In 16x2 LCD there are 16 pins overall if there is a black light, if there is no back light there will be 14 pins. One can power or leave the back light pins. Now in the 14 pins there are 8 data pins (7-14 or D0-D7), 2 power supply pins (1&2 or VSS&VDD or gnd&+5v), 3rd pin for contrast control (VEE-controls how thick the characters should be shown), 3 control pins (RS&RW&E)

In the circuit, you can observe that I have only took two control pins, this give the flexibility of better understanding, the contrast bit and READ/WRITE are not often used so they can be shorted to ground. This puts LCD in highest contrast and read mode. We just need to control ENABLE and RS pins to send characters and data accordingly.

The connections which are done for LCD are given below:

PIN1 or VSS to ground

PIN2 or VDD or VCC to +5v power

PIN3 or VEE to ground (gives maximum contrast best for a beginner)

PIN4 or RS (Register Selection) to PD6 of uC

PIN5 or RW (Read/Write) to ground (puts LCD in read mode eases the communication for user)

PIN6 or E (Enable) to PD5 of uC

PIN7 or D0 to PA0 of uC

PIN8 or D1 to PA1 of uC

PIN9 or D2 to PA2 of uC

PIN10 or D3 to PA3 of uC

PIN11 or D4 to PA4 of uC

PIN14 or D7 to PA7 of uC

PIN12 or D5 to PA5 of uC

PIN13 or D6 to PA6 of uC

In the circuit, you can see we have used 8bit communication (D0-D7). However this is not a compulsory and we can use 4bit communication (D4-D7) but with 4 bit communication program becomes a bit complex, so I preferred 8 bit communication.

So from mere observation of above table we are connecting 10 pins of LCD to controller in which 8 pins are data pins and 2 pins for control.

Before moving ahead, we need to understand about the serial communication. The RFID module here sends data to the controller in serial. It has other mode of communication but for easy communication we are choosing RS232. The RS232 pin of module is connected to RXD pin of ATMEGA.

The data sent by the RFID module goes as:
![image](https://user-images.githubusercontent.com/101261412/164522317-e791ae07-dfca-436d-b8f2-e379b564eaed.png)
Now for the RFID module interface, the following features are required:

1. The RXD pin (data receiving feature) of controller must be enabled.

2. Since the communication is serial we need to know whenever the data bye is received, so that we can stop the program until complete byte is received. This is done by enabling a data receive complete interrupt.

3. RFID sends data to controller in 8bit mode. So two characters will be sent to the controller at a time. This is shown in block of figure3

4. From figure3, there are no parity bits, one stop bit in the data sent by the module.

The above features are set in the controller registers; we are going to discuss them briefly,

![image](https://user-images.githubusercontent.com/101261412/164522629-2eef256f-9373-4548-9027-5395eaf3ec56.png)
![image](https://user-images.githubusercontent.com/101261412/164522860-78054ce9-9b67-421b-af4d-5f5b8a78503b.png)

RED (RXEN): This bit represents receive data feature, this bit must be set for the data from the module to be received by the controller, it also enables RXD pin of controller.

BROWN (RXCIE): This bit must be set for getting an interrupt after successful data reception. By enabling this bit we get to know, right after 8 bit data receive.

PINK (URSEL): This bit must be set before enable other bits in UCSRC, after setting other needed bits in UCSRC; URSEL must be disabled or put to zero.

YELLOW(UCSZ0,UCSZ1,UCSZ2):These three bits are used for selecting the number of data bits we are receiving or sending in a single go.

![image](https://user-images.githubusercontent.com/101261412/164523075-31568985-e9de-46e5-87be-b755271d03a6.png)
 Since the data sent by RFID module is 8bit data type (FIGURE3), we have to set UCSZ0, UCSZ1 to one and UCSZ2 to zero.

![image](https://user-images.githubusercontent.com/101261412/164523329-22369d66-0ce6-4453-8e38-262fb2ae5124.png)

ORANGE (UMSEL): This bit is set based on whether the system is communicating asynchronously (both use different clock) or synchronously (both use same clock),

![image](https://user-images.githubusercontent.com/101261412/164523812-48380b98-1d32-4489-9d90-acb7198a6eff.png)

Since module and controller use different clock, this bit must be set to zero or left alone as they are all set to zero by default.

GREEN (UPM1, UPM0): These two bits are adjusted based on bit parity we are using in communication.
![image](https://user-images.githubusercontent.com/101261412/164524062-09427d2c-6981-4eb2-8b27-f55626d7e1ea.png)
 
 Since RFID module sends data with no parity (FIGURE3), we have set both UPM1, UPM0 to zero or they can be left alone as all bits in any registers are set to zero by default.

BLUE (USBS): This bit is used for choosing the number of stop bits we are using during communication.
![image](https://user-images.githubusercontent.com/101261412/164524327-75cf8ea4-f80f-4255-be45-b86dadaa1529.png)

Since RFID module sends data with one stop bit (figure3), we just have to leave USBS bit alone.

Now at last we need to set the baud rate, from figure3 it is clear that the RFID module sends data to controller with a baud rate of 9600bps (bits per second).

The baud rate is set in controller by choosing the appropriate UBRRH,

![image](https://user-images.githubusercontent.com/101261412/164524478-d4f6ceec-0307-415e-8022-6293feb57856.png)

The UBRRH value is chosen by cross referring baud rate and CPU crystal frequency,

  So by cross reference UBRR value is seen as ‘6’, and so the baud rate is set.
  
  ![image](https://user-images.githubusercontent.com/101261412/164524635-8e130a07-8e5a-4a57-be02-41ed5e2ddea7.png)
  
  There are five buttons present here, four for incrementing the votes of candidates and fifth is for resetting the candidates’ votes to zero. The capacitors present here is for nullifying the bouncing effect of buttons. If they are removed the controller might count more than one each time the button is pressed.

The resistors connected for pins are for limiting the current, when the button is pressed to pull down the pin to the ground. Whenever a button is pressed, The corresponding pin of controller gets pulled down to ground and thus the controller recognizes that certain button is pressed and corresponding action to be taken, it may be incrementing the candidate votes or resetting of votes depending on button pressed.

When the button representing a corresponding person is pressed, the controller picks it and increments the corresponding person number inside its memory after increment it shows the corresponding persons score on the 16x2 LCD display.







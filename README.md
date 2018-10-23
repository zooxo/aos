# AOS - Arduboy Operating System
Welcome to AOS (Arduboy Operating System)  
Version 1.0 ... (c) 2018 by deetee/zooxo  
This software is covered by the 3-clause BSD license.  

![aos](https://user-images.githubusercontent.com/16148023/47264994-5adefb80-d521-11e8-996a-7acc3bd4717e.jpg)  

See a short video of AOS:
* Version 1.0: https://youtu.be/dVvj3j-pYlQ

## PREAMBLE
It is a little bit overacting to name this software an OS - at least because of the missing multiuser and multitasking features. But in form of a command shell this software controls the hardware of the Arduboy, the memory and all in/outputs.

As I like Linux and ancient HP-calculators I tried to combine both on a standardized Arduino hardware (Arduboy). AOS is based on a command shell with nearly 90 commands. So it is possible to generate and edit shell scripts (programs) to run a batch of commands automatically. With a simple disk operating system programs can be permanently saved to the EEPROM - and of course loaded from disk. It is even possible to export or import programs to or from a (Linux) PC.

In addition to this basic OS functions AOS offers a lot of useful applications:
* A calculator interface to offer a scientific RPN calculator. There is even a SOLVE or an INTEGRATE function.
* A simple text editor which enables the treatment of text files. Like programs text files can be stored to disk or exported to a PC.
* A keyboard emulator to simulate a (HID) keyboard via USB.
* A terminal mode which allows to control AOS from a PC keyboard.
* A clock (WATCH command).
* A torch (toggle white Arduboy LED).

Enjoy!
deetee


## OVERVIEW

![all](https://user-images.githubusercontent.com/16148023/46715416-2edd9380-cc60-11e8-8aa4-b2d6a0e266b3.png)

Central part of AOS is the memory which consists of three different kinds of stacks:
* **Bytestack**:
This stack of 16 bytes is the central memory for inputs of whole numbers between 0 and 255 which can be interpreted as figure or ascii character. With [Ibyte], [Ichar] and [Inum] values can be entered respectively with [PpB] the value of the next program step will be interpreted as a number (and not as a command).
Values of the bytestack can further be used as parameters for settings, as characters for printing messages, as bytes to send to interfaces or as ascii characters to create float numbers.
* **Floatstack**:
Similar to most RPN-calculators the floatstack consists of four registers (X, Y, Z and T) which are able to hold float numbers (-1E38 to 1E38) with a precision of mostly 7 significant digits.
Once on the floatstack numbers can be processed (i.e. added), treated (i.e. mathematical function) or compared (conditions in programs).
To save the floatstack permanently to the EEPROM use [Fsave].
* **Program**:
Two stacks of 256 bytes (0...255) can hold programs (like shell scripts) or ascii characters (text). Programs can be executed with [Prun] and edited with [Pedit] respectively text files can be edited with [TXT]. To switch between active program and inactive twin use [Pswap]. Even a simple subprogram feature is possible (see [Psub]).
Note that there are some commands that are only useful in executable programs (like [PpB], [Pgoto] or [Psub]).


## COMMANDS

### **COMMANDS - Sections** (capital letters in commands):
``````
    B     bytestack operations
    F     floatstack operations
    C     operations with constants
    CALC  RPN-calculator
    D     disk operations (EEPROM)
    I     input operations to select bytes
    KBD   sends selected bytes as HID (keyboard) to USB
    OFF   deep sleep mode (wake up with A) - auto poweroff after 3 min
    P     programing related commands (ie goto)
    S     settings (ie contrast, screensaver time)
    TERM  control aos with PC-keyboard
    TORCH toggle torch on/off
    TXT   simple text editor
    WATCH simple digital clock
``````

### **COMMANDS - Overview** (commands and corresponding ascii characters):
  ```
      DEC  |  0       1       2       3       4       5       6       7
      -----|--+-------+-------+-------+-------+-------+-------+-------+------
      032  |    about ! B2F   " B>>F  # Bclr  $ Bprnt % Bshow & CALC  ' Cload
      040  |  ( Csave ) D     * Dfmt  + F!    , F*    - F+    . F-    / F/
      048  |  0 F2h   1 F2hms 2 F2p   3 F2r   4 F<    5 F<=   6 F<>   7 F=
      056  |  8 F>    9 F>=   : Facos ; Facsh < Fannu = Fasin > Fasnh ? Fatan
      064  |  @ Fatnh A Fbatt B Fce   C Fchs  D Fclx  E Fcos  F Fcosh G FEE
      072  |  H Fexp  I Fgaus J Fint  K Finv  L Fln   M FLR   N Fpow  O Fpush
      080  |  P Frcl  Q Frot  R Fsave S Fshow T Fsin  U Fsinh V Fsolv W Fsqrt
      088  |  X Fstat Y Fsto  Z Fsum  [ Fswap \ Ftan  ] Ftanh ^ Ibyte _ Ichar
      096  |  ` Inum  a KBD   b OFF   c Pedit d Pexp  e Pgoto f Pimp  g Pname
      104  |  h Pnop  i PpB   j Ppse  k Prun  l Pstop m Psub  n Pswap o Py/n
      112  |  p Sfn   q Slit  r Sss   s TERM  t TORCH u TXT   v WATCH w Wset
```

### **COMMANDS** - Detailed description:
* **[about]** Print about message  
Prints the name of this software, the version and the author.
* **[B2F]** Pull bytestack[0] and push/cast to floatstack[0]  
Pulls first bytestack register and pushes it (with casting to double) to the first floatstack register.
* **[B>>F]** Flush bytestack to calculator  
Flushes the inverted bytestack (not equal NULL) to become interpreted by the the calculator (ie digits, decimal point). Useful in programs to enter float numbers to the floatstack.
* **[Bclr]** Clear bytestack  
Writes NULL values to the whole bytestack.
* **[Bprnt]** Print bytestack as string  
Flushes the inverted bytestack (not equal NULL) and print this characters to the first line of the display. Useful in programs to print text to the display.
* **[Bshow]** Show bytestack on display  
Shows the first three bytestack register in the second line of the display.
* **[CALC]** Calculator mode (RPN)  
User interface that allows to enter numbers to the floatstack (digits, decimal point, paragraph symbol to enter the number). Additional is a clear function (C) and the possibility to enter one command (arrow up).
* **[Cload]** Load constant with number floatstack[0] to floatstack  
Loads a float constant from EEPROM and pushes it to the floatstack. The number of the desired constant (0...9) will be pulled from the floatstack (see also [Csave]).
* **[Csave]** Save floatstack[1] to EEPROM at slot floatstack[0]  
Copies the number of the second floatstack register to the EEPROM at slot floatstack[0].
* **[D]** Disk operation system (save, load, delete)  
User interface that allows to treat files (programs) saved to EEPROM. Browse the disk content (cursor keys), load a program to memory (with key B), save a program to disk (with shift right) or delete a saved file from EEPROM (with shift left). Exit this interface with shift up or shift down.
* **[Dfmt]** Format disk system  
Format the disk (EEPROM) - this deletes all saved files!
* **[F!]** Calculate GAMMA/factorial +)  
This function calculates the gamma function due to Nemes.
* **[F\*]** Calculate Y\*X  
This function pulls and multiplies the first two floatstack registers and pushes the result to the floatstack.
* **[F+]** Calculate Y+X  
This function pulls and adds the first two floatstack registers and pushes the result to the floatstack.
* **[F-]** Calculate Y-X  
This function pulls and substracts the first two floatstack registers and pushes the result to the floatstack.
* **[F/]** Calculate Y/X  
This function pulls and divides the first two floatstack registers and pushes the result to the floatstack.
* **[F2h]** Convert H.MS to H  
Converts time or angle data given in h.mmss format to h.hh format.
* **[F2hms]** Convert H to H.MS  
Converts time or angle data given in h.hh format to h.mmss format.
* **[F2p]** Convert rectangular to polar coordinates +)  
Converts rectangular coordinates (x|y) represented in the first two registers of the floatstack (X|Y) to polar coordinates (r|<) represented in the first two registers of the floatstack (X|Y).
* **[F2r]** Convert polar to rectangular coordinates +)  
Converts polar coordinates (r|<) represented in the first two registers of the floatstack (X|Y) to rectangular coordinates (x|y) represented in the first two registers of the floatstack (X|Y).
* **[F<]** True (ptr+2) if X < Y on floatstack  
Increments the program pointer with 2 if the first floatstack register is less than the second.
* **[F<=]** True (ptr+2) if X <= Y on floatstack  
Increments the program pointer with 2 if the first floatstack register is less or equal than the second.
* **[F<>]** True (ptr+2) if X <> Y on floatstack  
Increments the program pointer with 2 if the first floatstack register is not equal than the second.
* **[F=]** True (ptr+2) if X = Y on floatstack  
Increments the program pointer with 2 if the first floatstack register is equal to the second.
* **[F>]** True (ptr+2) if X > Y on floatstack  
Increments the program pointer with 2 if the first floatstack register is greater than the second.
* **[F>=]** True (ptr+2) if X >= Y on floatstack  
Increments the program pointer with 2 if the first floatstack register is greater or equal than the second.
* **[Facos]** Calculate ACOS(X) +)  
This function calculates the inverse cosine function of the first floatstack register.
* **[Facsh]** Calculate ACOSH(X) +)  
This function calculates the inverse hyperbolic cosine function of the first floatstack register.
* **[Fannu]** Calculate annuity/preset value (Y: rate, X:years) +)  
Calculates the annuity respective preset value of a given interest rate (second floatstack register) and duration (first floatstack register).
* **[Fasin]** Calculate ASIN(X)  
This function calculates the inverse sine function of the first floatstack register.
* **[Fasnh]** Calculate ASINH(X) +)  
This function calculates the inverse hyperbolic sine function of the first floatstack register.
* **[Fatan]** Calculate ATAN(X)  
This function calculates the inverse tangent function of the first floatstack register.
* **[Fatnh]** Calculate ATANH(X) +)  
This function calculates the inverse hyperbolic tangent function of the first floatstack register.
* **[Fbatt]** Push the value of battery voltage to floatstack  
This function measures the supply voltage and pushes the value to the floatstack. It seems that a standard Arduboy is full charged if the voltage exceeds 4.3 V if plugged and 4.2 V if unplugged. The battery seems to be empty with a voltage below 3.1 V. These values may differ with different hardware or battery condition. See also the section "Energy Saving".
* **[Fce]** Clear last entry  
Deletes last entry or keypress of entered float number.
* **[Fchs]** Change sign of X  
Changes the sign of the first floatstack register.
* **[Fclx]** Clear X  
Clears the first floatstack register (assign 0.0).
* **[Fcos]** Calculate COS(X) +)  
This function calculates the cosine function of the first floatstack register.
* **[Fcosh]** Calculate COSH(X) +)  
This function calculates the hyperbolic cosine function of the first floatstack register.
* **[FEE]** Calculate EE (Y\*10^X) on floatstack  
This function pulls the first two floatstack registers and multiplies the second with 10  raised to the power of the first. Use this function to enter exponents with base 10.
* **[Fexp]** Calculate EXP(X)  
This function calculates the exponential function of the first floatstack register.
* **[Fgaus]** Calculate CDF/PDF (standardized gaussian distribution function) +)  
This function calculates the  CDF (Cumulative Distribution Function) and PDF (Probability Density Function) of the first floatstack register and pushes the results to the floatstack.
* **[Fint]** INTegrate function represented in prg[] between Y and X  
Calculates the integral of the function represented in the program between the boundaries given in the first two floatstack registers. This uses the Simpson method with 10 stripes.
* **[Finv]** Calculate 1/X  
 This function calculates the reciprocal value of the first floatstack register.
* **[Fln]** Calculate LN(X)  
This function calculates the natural logarithm of the first floatstack register.
* **[FLR]** Calculate L.R. (Xprev, Yprev)  
Calculates the predictive values of the line of best fit to nodes entered with [Fsum].
* **[Fpow]** Calculate POW(Y,X) ... Y^X  
This function pulls the first two floatstack registers and calculates the second raised to the power of the first and pushes the result to the floatstack.
* **[Fpush]** Push floatstack  
This function lifts the whole floatstack one step but holds (reproduces) the first floatstack register.
* **[Frcl]** Recall float memory to X  
This function pushes the number stored with [Fsto] to the floatstack.
* **[Frot]** Rotate floatstack (down)  
 This function rotates the whole floatstack down - which means the first floatstack register was shifted to the last.
* **[Fsave]** Save floatstack and mem to EEPROM  
Saves the whole floatstack and stored number [Fsto] permanently to the EEPROM. This values will be loaded automatically when switching on.
* **[Fshow]** Show floatstack on display  
Shows the first floatstack register in the first line of the display. This will be done in scientific format only.
* **[Fsin]** Calculate SIN(X)  
This function calculates the sine function of the first floatstack register.
* **[Fsinh]** Calculate SINH(X) +)  
This function calculates the hyperbolic sine function of the first floatstack register.
* **[Fsolv]** SOLVE function represented in prg[]  
Calculates the root of the function represented in the program due to Newton's method. The start value is given by the first floatstack register.
* **[Fsqrt]** Calculate SQRT(X)  
This function calculates square root of the first floatstack register.
* **[Fstat]** Calculate statistics STAT ... X: mean Y: standard deviation  
Calculates the mean value and standard deviation of statistical data entered with [Fsum].
* **[Fsto]** Store X to float memory  
This function stores the first floatstack register to the memory. To restore/recall it use [Frcl].
* **[Fsum]** Process statistical data (SUM)  
Enter one- or two-dimensional statistical data. Evaluate this data with [FLR] or [Fstat].
* **[Fswap]** Swap X and Y  
This function swaps the first two floatstack register.
* **[Ftan]** Calculate TAN(X) +)  
This function calculates the tangent function of the first floatstack register.
* **[Ftanh]** Calculate TANH(X) +)  
This function calculates the hyperbolic tangent function of the first floatstack register.
* **[Ibyte]** Input byte  
This user interface allows entering byte values (0...255) to the bytestack.
* **[Ichar]** Input character  
This user interface allows entering ascii characters (byte values between 32 and 127) to the bytestack.
* **[Inum]** Input number  
This user interface allows entering ascii character numbers (byte values between 48 and 57) to the bytestack.
* **[KBD]** Keyboard emulator (HID keyboard via USB)  
This user interface allows to send keyboard like commands to the USB interface. To send CTRL or ALT keys press shift+L or shift+R. To send special commands use this codes:
``````
              128-CTRL    130-ALT     176-RETURN  177-ESC    178-BKSPC
              179-TAB     193-CAPS    194-F1      195-F2      196-F3
              197-F4      198-F5      199-F6      200-F7      201-F8
              202-F9      203-F10     204-F11     205-F12     209-INSERT
              210-HOME    211-PGUP    212-DELETE  213-END     214-PGDOWN
              215-RIGHT   216-LEFT    217-DOWN    218-UP
``````
* **[OFF]** Power down till A is pressed (SLEEP_MODE_PWR_DOWN), shift+B 
Sends the device into a deep sleep mode till button A is pressed. Note that neither a timer nor an interface is active.
* **[Pedit]** Edit active program  
User interface to edit a program. Insert a step with shift+R, delete a step with shift+L and edit a step with A.
* **[Pexp]** Export active program to Linux-PC  
Exports program to a Linux-PC with minicom installed. It opens a shell, transfers steps (corresponding ascii characters) with minicom to the home directory of the PC (file named prg.aos). This file can be edited with any text editor and imported with [Pimp].
* **[Pgoto]** Goto program step  
Reads the next program step and sets the program pointer to this value.
* **[Pimp]** Import active program from Linux-PC  
Imports program from a Linux-PC. The file should be located in the home directory and should be named prg.aos. To export a program to a PC see [Pexp].
* **[Pname]** Set program name (ZYX)  
Sets the name of the program (3 letters) due to the first three bytestack registers (inverted order).
* **[Pnop]** No operation  
This command does nothing, but is useful for skipping commands like [Py/n].
* **[PpB]** Push value of next program step to bytestack  
This is the one and only command for a direct input of numbers in a program. It pushes the byte value of the following program step to the bytestack.
* **[Ppse]** Pause for PAUSE milliseconds  
Pauses the execution of the program for 1 second. This is useful to show messages or results while program execution.
* **[Prun]** Run active program  
This command starts the execution of the program.
* **[Pstop]** Stops program execution  
This command stops the execution of the program (note the hotkey LEFT+B).
* **[Psub]** Execute non-active program and return  
Switches to the non-active program, executes it and switches back to the source program execution. This is a simple way to use program codes as subprogram. To toggle between the active and non-active program see also [Pswap].
* **[Pswap]** Toggle between active and non-active program  
Toggle between active program and non-active program. See also [Psub].
* **[Py/n]** Decision ESC (ptr+1), NO (ptr+3) or YES (ptr+5)  
This command offers the possibility to check for a decision while program execution. While in line 1 of the display a text could be displayed (command [Bprnt]) in line 2 a YES or NO text will be shown. In case of a NO decision (LEFT key) the program pointer will be incremented by 3 and in case of a YES decision (RIGHT key) the program pointer will be incremented by 5. Any other key will execute the next program step.
* **[Sfn]** Set function keys and save to EEPROM (rdlu=TZYX)  
While in command mode 4 function keys can be defined by the user. This command pulls 4 bytes (commands) from the bytestack and assigns them to the 4 function keys (RIGHT, DOWN, LEFT, UP). Additionally these assignments are saved permanantly to the EEPROM. To recall these commands press in command mode shift and the corrsponding cursor key.
* **[Slit]** Set contrast  
Sets the contrast of the display due to the first bytestack register and saves it permanently to the EEPROM.
* **[Sss]** Set screensaver time  
Sets the screensaver time due to the first bytestack register (in seconds) and saves it permanently to the EEPROM.
* **[TERM]** Toggle terminal control  
Enables to control AOS via USB from a PC running a terminal program (ie minicom). So the keys Q/W/E/A/S/D are corresponding to the buttons A/UP/B/LEFT/DOWN/RIGHT.
* **[TORCH]** Toggle torch  
This command toggles the white LED light of the Arduboy.
* **[TXT]** Text editor (treat prg[] as text file)  
User interface to edit a program a text file. To insert a character press shift+RIGHT or B. To delete a character (backspace) press shift+LEFT. Note that a text file can be moved (load, save, import, export) like a program.
* **[WATCH]** Show watch (set with cursor keys, reset minute with B)  
Note that screensaver and auto poweroff will be deactivated.
* **[Wset]** Set watch from bytestack (YY:XX)  
Activates and sets the watch due to the first two bytestack registers (minutes and hours).

+) ...  Some functions affect the whole floatstack as they are calculated with basic operations.


## SPECIALITIES
## First start
Some variables are stored permanently to the EEPROM and will be loaded automatically when started. After flashing AOS the EEPROM may content undefined values. So setting some variables after flashing make sense. Particulary this makes sense for the screensaver time ([Sss]), screen contrast ([Slit]) and floatstack values ([Fsave]).

## Hotkeys, Shortcuts
      - UP while switching on ... Secure wait to flash software
      - A + B                 ... Reset device
      - LEFT + B              ... Stop program execution
      - shift B (in cmd mode) ... Deep sleep mode <OFF> till A is pressed
      - shift RIGHT/DOWN/LEFT/UP (in cmd mode) ... execute function key set with Sfn

## Energy Saving
      - Some not used features (I2C, ADC, USART, all timers but number 0) are permanently disabled.
      - The display deactivates after inactivity and the screensaver time (set with <Sss>).
      - After 3 minutes of inactivity auto poweroff activates the deep sleep mode.
      Note: In deep sleep mode USB and all timers (clock) are disabled.
      Note: While the clock is active ([WATCH]) neither auto poweroff nor the screensaver will be activated.

## DECISION - based on floatstack (X, Y)
       F(<, <=, <>, =, >, >=) --+   Skips next two program steps if TRUE
       -                        |
       -                        |
       - <----------------------+

### YES/NO/ESC-decision [Py/n]
       Py/n --->---+
       - <---ESC---+   ESC (any key except YES or NO) runs the next program step
       -           |
       - <---NO----+   NO (left key) skips next two program steps
       -           |
       - <---YES---+   YES (right key) skips next four program steps


## APPENDIX

### EEPROM:
``````
    | Bright- | Screenoff | Fkeys | Floatstack  |   Disk    |
    | ness    | time      |       | X Y Z T MEM |  (files)  |
    0         1           2       6             26         2559
    |    1    |     1     |   4   |      20     |   2533    |
``````

### ASCII TABLE

      DEC     |  0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5
          HEX |  0 1 2 3 4 5 6 7 8 9 a b c d e f
      ------------------------------------------
      032 20  |    ! " # $ % & ' ( ) * + , - . /
      048 30  |  0 1 2 3 4 5 6 7 8 9 : ; < = > ?
      064 40  |  @ A B C D E F G H I J K L M N O
      080 50  |  P Q R S T U V W X Y Z [ \ ] ^ _
      096 60  |  ` a b c d e f g h i j k l m n o
      112 70  |  p q r s t u v w x y z { | } ~

### MODYFIER KEYS- Leonardo (Key Hex Dec)
KEY_LEFT_CTRL 0x80 128  
KEY_LEFT_SHIFT  0x81  129  
KEY_LEFT_ALT  0x82  130
KEY_LEFT_GUI  0x83  131
KEY_RIGHT_CTRL  0x84  132
KEY_RIGHT_SHIFT  0x85  133
KEY_RIGHT_ALT  0x86  134
KEY_RIGHT_GUI  0x87  135
KEY_UP_ARROW  0xDA  218
KEY_DOWN_ARROW  0xD9  217
KEY_LEFT_ARROW  0xD8  216
KEY_RIGHT_ARROW  0xD7  215
KEY_BACKSPACE  0xB2  178
KEY_TAB  0xB3  179
KEY_RETURN  0xB0  176
KEY_ESC  0xB1  177
KEY_INSERT  0xD1  209
KEY_DELETE  0xD4  212
KEY_PAGE_UP  0xD3  211
KEY_PAGE_DOWN  0xD6  214
KEY_HOME  0xD2  210
KEY_END  0xD5  213
KEY_CAPS_LOCK  0xC1  193
KEY_F1  0xC2  194
KEY_F2  0xC3  195
KEY_F3  0xC4  196
KEY_F4  0xC5  197
KEY_F5  0xC6  198
KEY_F6  0xC7  199
KEY_F7  0xC8  200
KEY_F8  0xC9  201
KEY_F9  0xCA  202
KEY_F10  0xCB  203
KEY_F11  0xCC  204
KEY_F12  0xCD  205

# AOS - Arduboy Operating System
Version 1.0 ... (c) 2018 by deetee/zooxo

## PREAMBLE

## OVERVIEW

![all](https://user-images.githubusercontent.com/16148023/46715416-2edd9380-cc60-11e8-8aa4-b2d6a0e266b3.png)

## COMMANDS


/*

  Todo:
  check FLR
    //INT n wÃ¤hlen
    watch kalibrieren
    memmove statt for-loops
    memset
    // EEPROM editor
    // Screenshot
    // Kalender
    order variables subprgs defines
    nicer code

  12345678901234567890123456789012345678901234567890123456789012345678901234567890


  ____________________

    Welcome to AOS (Arduboy Operating System)
    Version 1.0 ... (c) 2018 by deetee/zooxo
    This software is covered by the 3-clause BSD license.

  ____________________

    PREAMBLE
  ____________________

    It is a little bit overacting to name this software an OS - at least because
    of the missing multiuser and multitasking features. But in form of a command
    shell this software controls the hardware of the Arduboy, the memory and all
    in/outputs.

    As I like Linux and ancient HP-calculators I tried to combine both on a
    standardized Arduino hardware (Arduboy).
    AOS is based on a command shell with nearly 90 commands. So it is possible
    to generate and edit shell scripts (programs) to run a batch of commands
    automatically. With a simple disk operating system programs can be
    permanently saved to the EEPROM - and of course loaded from disk. It is even
    possible to export or import programs to or from a (Linux) PC.

    In addition to this basic OS functions AOS offers a lot of useful
    applications:
      - A simple text editor which enables the treatment of text files. Like
        programs text files can be stored to disk or exported to a PC.
      - A calculator interface to offer a scientific RPN calculator. There is
        even a SOLVE or an INTEGRATE function.
      - A keyboard emulator to simulate a (HID) keyboard via USB.
      - A terminal mode which allows to control AOS from a PC keyboard.
      - A clock (WATCH command).
      - A torch (toggle white Arduboy LED).

    Enjoy!
    deetee

  ____________________

    OVERVIEW
  ____________________

    Central part of AOS is the memory which consists of three different kinds
    of stacks:
      - Bytestack:
        This stack of 16 bytes is the central memory for inputs of whole
        numbers between 0 and 255 which can be interpreted as figure or ascii
        character. With [Ibyte], [Ichar] and [Inum] values can be entered
        respectively with [PpB] the value of the next program step will be
        interpreted as a number (and not as a command).
        Values of the bytestack can further be used as parameters for settings,
        as characters for printing messages, as bytes to send to interfaces
        or as ascii characters to create float numbers.
      - Floatstack:
        Similar to most RPN-calculators the floatstack consists of four
        registers (X, Y, Z and T) which are able to hold float numbers
        (-1E38 to 1E38) with a precision of mostly 7 significant digits.
        Once on the floatstack numbers can be processed (i.e. added), treated
        (i.e. mathematical function) or compared (conditions in programs).
        To save the floatstack permanently to the EEPROM use [Fsave].
      - Program:
        Two stacks of 256 bytes (0...255) can hold programs (like shell
        scripts) or ascii characters (text). Programs can be executed with
        [Prun] and edited with [Pedit] respectively text files can be
        edited with [TXT]. To switch between active program and inactive
        twin use [Pswap]. Even a simple subprogram feature is possible (see
        [Psub]).
        Note that there are some commands that are only useful in executable
        programs (like [PpB], [Pgoto] or [Psub]).

  ____________________

    COMMANDS
  ____________________

  COMMANDS - Sections (capital letters in commands):
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

  COMMANDS - Overview (commands and corresponding ascii characters):
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


  COMMANDS - Detailed description:
    [about] Print about message
            Prints the name of this software, the version and the author.
    [B2F]   Pull bytestack[0] and push/cast to floatstack[0]
            Pulls first bytestack register and pushes it (with casting to double)
            to the first floatstack register.
    [B>>F]  Flush bytestack to calculator
            Flushes the inverted bytestack (not equal NULL) to become interpreted
            by the the calculator (ie digits, decimal point). Useful in programs
            to enter float numbers to the floatstack.
    [Bclr]  Clear bytestack
            Writes NULL values to the whole bytestack.
    [Bprnt] Print bytestack as string
            Flushes the inverted bytestack (not equal NULL) and print this
            characters to the first line of the display. Useful in programs to
            print text to the display.
    [Bshow] Show bytestack on display
            Shows the first three bytestack register in the second line of the
            display.
    [CALC]  Calculator mode (RPN)
            User interface that allows to enter numbers to the floatstack
            (digits, decimal point, paragraph symbol to enter the number).
            Additional is a clear function (C) and the possibility to enter one
            command (arrow up).
    [Cload] Load constant with number floatstack[0] to floatstack
            Loads a float constant from EEPROM and pushes it to the floatstack.
            The number of the desired constant (0...9) will be pulled from the
            floatstack (see also [Csave]).
    [Csave] Save floatstack[1] to EEPROM at slot floatstack[0]
            Copies the number of the second floatstack register to the EEPROM
            at slot floatstack[0].
    [D]     Disk operation system (save, load, delete)
            User interface that allows to treat files (programs) saved to
            EEPROM. Browse the disk content (cursor keys), load a program to
            memory (with key B), save a program to disk (with shift right) or
            delete a saved file from EEPROM (with (shift left). Exit this
            interface with shift up or shift down.
    [Dfmt]  Format disk system
            Format the disk (EEPROM) - this deletes all saved files!
    [F!]    Calculate GAMMA/factorial +)
            This function calculates the gamma function due to Nemes.
    [F*]    Calculate Y*X
            This function pulls and multiplies the first two floatstack
            registers and pushes the result to the floatstack.
    [F+]    Calculate Y+X
            This function pulls and adds the first two floatstack registers
            and pushes the result to the floatstack.
    [F-]    Calculate Y-X
            This function pulls and substracts the first two floatstack
            registers and pushes the result to the floatstack.
    [F/]    Calculate Y/X
            This function pulls and divides the first two floatstack
            registers and pushes the result to the floatstack.
    [F2h]   Convert H.MS to H
            Converts time or angle data given in h.mmss format to h.hh format.
    [F2hms] Convert H to H.MS
            Converts time or angle data given in h.hh format to h.mmss format.
    [F2p]   Convert rectangular to polar coordinates +)
            Converts rectangular coordinates (x|y) represented in the first
            two registers of the floatstack (X|Y) to polar coordinates (r|<)
            represented in the first two registers of the floatstack (X|Y).
    [F2r]   Convert polar to rectangular coordinates +)
            Converts polar coordinates (r|<) represented in the first two
            registers of the floatstack (X|Y) to rectangular coordinates (x|y)
            represented in the first two registers of the floatstack (X|Y).
    [F<]    True (ptr+2) if X < Y on floatstack
            Increments the program pointer with 2 if the first floatstack
            register is less than the second.
    [F<=]   True (ptr+2) if X <= Y on floatstack
            Increments the program pointer with 2 if the first floatstack
            register is less or equal than the second.
    [F<>]   True (ptr+2) if X <> Y on floatstack
            Increments the program pointer with 2 if the first floatstack
            register is not equal than the second.
    [F=]    True (ptr+2) if X = Y on floatstack
            Increments the program pointer with 2 if the first floatstack
            register is equal to the second.
    [F>]    True (ptr+2) if X > Y on floatstack
            Increments the program pointer with 2 if the first floatstack
            register is greater than the second.
    [F>=]   True (ptr+2) if X >= Y on floatstack
            Increments the program pointer with 2 if the first floatstack
            register is greater or equal than the second.
    [Facos] Calculate ACOS(X) +)
            This function calculates the inverse cosine function of the
            first floatstack register.
    [Facsh] Calculate ACOSH(X) +)
            This function calculates the inverse hyperbolic cosine function
            of the first floatstack register.
    [Fannu] Calculate annuity/preset value (Y: rate, X:years) +)
            Calculates the annuity respective preset value of a given interest
            rate (second floatstack register) and duration (first floatstack
            register).
    [Fasin] Calculate ASIN(X)
            This function calculates the inverse sine function of the first
            floatstack register.
    [Fasnh] Calculate ASINH(X) +)
            This function calculates the inverse hyperbolic sine function
            of the first floatstack register.
    [Fatan] Calculate ATAN(X)
            This function calculates the inverse tangent function of the first
            floatstack register.
    [Fatnh] Calculate ATANH(X) +)
            This function calculates the inverse hyperbolic tangent function
            of the first floatstack register.
    [Fbatt] Push the value of battery voltage to floatstack
            Measures the supply voltage and pushes the value to the floatstack.
            Values between 2.4V (0%) and 4.3V (100%) correspond to the
            battery level.
    [Fce]   Clear last entry
            Deletes last entry or keypress of entered float number.
    [Fchs]  Change sign of X
            Changes the sign of the first floatstack register.
    [Fclx]  Clear X
            Clears the first floatstack register (assign 0.0).
    [Fcos]  Calculate COS(X) +)
            This function calculates the cosine function of the first
            floatstack register.
    [Fcosh] Calculate COSH(X) +)
            This function calculates the hyperbolic cosine function of the
            first floatstack register.
    [FEE]   Calculate EE (Y*10^X) on floatstack
            This function pulls the first two floatstack registers and
            multiplies the second with 10  raised to the power of the first.
            Use this function to enter exponents with base 10.
    [Fexp]  Calculate EXP(X)
            This function calculates the exponential function of the first
            floatstack register.
    [Fgaus] Calculate CDF/PDF (standardized gaussian distribution function) +)
            This function calculates the  CDF (Cumulative Distribution Function)
            and PDF (Probability Density Function) of the first floatstack
            register and pushes the results to the floatstack.
    [Fint]  INTegrate function represented in prg[] between Y and X
            Calculates the integral of the function represented in the program
            between the boundaries given in the first two floatstack
            registers. This uses the Simpson method with 10 stripes.
    [Finv]  Calculate 1/X
            This function calculates the reciprocal value of the first
            floatstack register.
    [Fln]   Calculate LN( X)
            This function calculates the natural logarithm of the first
            floatstack register.
    [FLR]   Calculate L.R. (Xprev, Yprev)
            Calculates the predictive values of the line of best fit to nodes
            entered with [Fsum].
    [Fpow]  Calculate POW(Y,X) ... Y^X
            This function pulls the first two floatstack registers and
            calculates the second raised to the power of the first and
            pushes the result to the floatstack.
    [Fpush] Push floatstack
            This function lifts the whole floatstack one step but holds
            (reproduces) the first floatstack register.
    [Frcl]  Recall float memory to X
            This function pushes the number stored with [Fsto] to the
            floatstack.
    [Frot]  Rotate floatstack (down)
            This function rotates the whole floatstack down - which means
            the first floatstack register was shifted to the last.
    [Fsave] Save floatstack and mem to EEPROM
            Saves the whole floatstack and stored number [Fsto] permanently
            to the EEPROM. This values will be loaded automatically
            when switching on.
    [Fshow] Show floatstack on display
            Shows the first floatstack register in the first line of the
            display. This will be done in scientific format only.
    [Fsin]  Calculate SIN(X)
            This function calculates the sine function of the first
            floatstack register.
    [Fsinh] Calculate SINH(X) +)
            This function calculates the hyperbolic sine function of the
            first floatstack register.
    [Fsolv] SOLVE function represented in prg[]
            Calculates the root of the function represented in the program due
            to Newton's method. The start value is given by the first floatstack
            register.
    [Fsqrt] Calculate SQRT(X)
            This function calculates square root of the first floatstack
            register.
    [Fstat] Calculate statistics STAT ... X: mean Y: standard deviation
            Calculates the mean value and standard deviation of statistical
            data entered with [Fsum].
    [Fsto]  Store X to float memory
            This function stores the first floatstack register to the memory.
            To restore/recall it use [Frcl].
    [Fsum]  Process statistical data (SUM)
            Enter one- or two-dimensional statistical data. Evaluate this
            data with [FLR] or [Fstat].
    [Fswap] Swap X and Y
            This function swaps the first two floatstack register.
    [Ftan]  Calculate TAN(X) +)
            This function calculates the tangent function of the first
            floatstack register.
    [Ftanh] Calculate TANH(X) +)
            This function calculates the hyperbolic tangent function of the
            first floatstack register.
    [Ibyte] Input byte
            This user interface allows entering byte values (0...255) to
            the bytestack.
    [Ichar] Input character
            This user interface allows entering ascii characters (byte
            values between 32 and 127) to the bytestack.
    [Inum]  Input number
            This user interface allows entering ascii character numbers
            (byte values between 48 and 57) to the bytestack.
    [KBD]   Keyboard emulator (HID keyboard via USB)
            This user interface allows to send keyboard like commands to
            the USB interface. To send CTRL or ALT keys press shift+L or
            shift+R. To send special commands use this codes:
              128-CTRL    130-ALT     176-RETURN  177-ESC    178-BKSPC
              179-TAB     193-CAPS    194-F1      195-F2      196-F3
              197-F4      198-F5      199-F6      200-F7      201-F8
              202-F9      203-F10     204-F11     205-F12     209-INSERT
              210-HOME    211-PGUP    212-DELETE  213-END     214-PGDOWN
              215-RIGHT   216-LEFT    217-DOWN    218-UP
    [OFF]   Power down till A is pressed (SLEEP_MODE_PWR_DOWN), shift+B
            Sends the device into a deep sleep mode till button A is
            pressed. Note that neither a timer nor an interface is active.
    [Pedit] Edit active program
            User interface to edit a program. Insert a step with shift+R,
            delete a step with shift+L and edit a step with A.
    [Pexp]  Export active program to Linux-PC
            Exports program to a Linux-PC with minicom installed. It opens
            a shell, transfers steps (corresponding ascii characters) with
            minicom to the home directory of the PC (file named prg.aos).
            This file can be edited with any text editor and imported
            with [Pimp].
    [Pgoto] Goto program step
            Reads the next program step and sets the program pointer to
            this value.
    [Pimp]  Import active program from Linux-PC
            Imports program from a Linux-PC. The file should be located
            in the home directory and should be named prg.aos. To export
            a program to a PC see [Pexp].
    [Pname] Set program name (ZYX)
            Sets the name of the program (3 letters) due to the first three
            bytestack registers (inverted order).
    [Pnop]  No operation
            This command does nothing, but is useful for skipping commands
            like [Py/n].
    [PpB]   Push value of next program step to bytestack
            This is the one and only command for a direct input of numbers
            in a program. It pushes the byte value of the following program
            step to the bytestack.
    [Ppse]  Pause for PAUSE milliseconds
            Pauses the execution of the program for 1 second. This is useful
            to show messages or results while program execution.
    [Prun]  Run active program
            This command starts the execution of the program.
    [Pstop] Stops program execution
            This command stops the execution of the program (note the
            hotkey LEFT+B).
    [Psub]  Execute non-active program and return
            Switches to the non-active program, executes it and switches back
            to the source program execution. This is a simple way to use
            program codes as subprogram. To toggle between the active and
            non-active program see also [Pswap].
    [Pswap] Toggle between active and non-active program
            Toggle between active program and non-active program. See also
            [Psub].
    [Py/n]  Decision ESC (ptr+1), NO (ptr+3) or YES (ptr+5)
            This command offers the possibility to check for a decision
            while program execution. While in line 1 of the display a text
            could be displayed (command [Bprnt]) in line 2 a YES or NO
            text will be shown. In case of a NO decision (LEFT key) the
            program pointer will be incremented by 3 and in case of a YES
            decision (RIGHT key) the program pointer will be incremented
            by 5. Any other key will execute the next program step.
    [Sfn]   Set function keys and save to EEPROM (rdlu=TZYX)
            While in command mode 4 function keys can be defined by the
            user. This command pulls 4 bytes (commands) from the bytestack
            and assigns them to the 4 function keys (RIGHT, DOWN, LEFT, UP).
            Additionally these assignments are saved permanantly to the
            EEPROM. To recall these commands press in command mode shift and
            the corrsponding cursor key.
    [Slit]  Set contrast
            Sets the contrast of the display due to the first bytestack
            register and saves it permanently to the EEPROM.
    [Sss]   Set screensaver time
            Sets the screensaver time due to the first bytestack register
            (in seconds) and saves it permanently to the EEPROM.
    [TERM]  Toggle terminal control
            Enables to control AOS via USB from a PC running a terminal
            program (ie minicom). So the keys Q/W/E/A/S/D are corresponding
            to the buttons A/UP/B/LEFT/DOWN/RIGHT.
    [TORCH] Toggle torch
            This command toggles the white LED light of the Arduboy.
    [TXT]   Text editor (treat prg[] as text file)
            User interface to edit a program a text file. To insert a
            character press shift+RIGHT or B. To delete a character
            (backspace) press shift+LEFT. Note that a text file can be moved
            (load, save, import, export) like a program.
    [WATCH] Show watch (set with cursor keys, reset minute with B)
            Note that screensaver and auto poweroff will be deactivated.
    [Wset]  Set watch from bytestack (YY:XX)
            Activates and sets the watch due to the first two bytestack
            registers (minutes and hours).

    +) ...  Some functions affect the whole floatstack as they are
            calculated with basic operations.



  Battery level: PLUGGED/FULL 4.33 V, UNPLUGGED/MAX 4.22 V, MIN 3.11 V
     discharge: (0h|4.15V) (22h|3.6V)

  Variables, Memory:
          :         T
      2   Z         Z                   P1 = 0, 1, 2, ... 255  <----/  Swap
      1   Y         Y                                              /   with
      0   X         X        MEM      P0 = 0, 1, 2, ... 255  <----/    Pswap

          Byte-     Float-   Float-   Program-
          stack     stack    memory   memory
          16 bytes  4 float  1 float  2 x 256 bytes
                    <-------------->  <------------------->
                    Save to EEPROM    Save/load to/from EEPROM with D (Disk Tool).
                    with Fsave        Export/import to/from PC with Pexp/Pimp.
                                      Edit with Pedit (program) or TXT (text file).


  EEPROM:

    | Bright- | Screenoff | Fkeys | Floatstack  |   Disk    |
    | ness    | time      |       | X Y Z T MEM |  (files)  |
    0         1           2       6             26         2559
    |    1    |     1     |   4   |      20     |   2533    |




  CALC:
    : ENTER
    / Choose one command
    - CLx/CE

  Disk

  Screen - select a byte/character/command

                          |       _        |
    selected byte (dec)   |  069 |_  Fexp  |  selected command
    previous characters   |  BCD |_ FGHIJ  |  next characters
                          |________________|
                                 ^
                                 selected character (double size)

  Keyboard:
    ^
   < >  B
    v  A


            up/esc
      left/del right/ins        OK
          down/          ESC/shift

    Shortcuts:
      A-B reset

    Input, calculator:
           pageUp          select
      left pageDown right  esc

    Program edit:
                      up/esc                    editStep
      pgup/deleteStep down/esc pgdn/insertStep  shift

    Text edit:
                      up/esc                    insertChar
      pgup/deleteChar down/esc pgdn/insertChar  shift

    Disk:
      Dfmt
               up/esc                  loadFile
      pgup/del down/esc pgdn/saveFile  shift

    Watch:
                  incr_hour              reset_seconds
      decr_minute decr_hour incr_minute  esc


    Terminal Mode:
         w     m
       a s d   n

    KBD:
                up/esc             send
      pgup/CTRL down/esc pgdn/ALT  shift


    Decision based on floatstack (X, Y)
       F(<, <=, <>, =, >, >=) --+   Skips next two program steps if TRUE
       -                        |
       -                        |
       - <----------------------+



    YES/NO/ESC-decision (Py/n)
       Py/n --->---+
       - <---ESC---+   ESC (any key except YES or NO) runs the next program step
       -           |
       - <---NO----+   NO (left key) skips next two program steps
       -           |
       - <---YES---+   YES (right key) skips next four program steps



    Hotkeys, Shortcuts
      - UP while switching on ... Secure wait to flash software
      - A + B                 ... Reset device
      - LEFT + B              ... Stop program execution
      - shift B (in cmd mode) ... Deep sleep mode <OFF> till A is pressed
      - shift RIGHT/DOWN/LEFT/UP (in cmd mode) ... execute function key set with Sfn

    Energy Saving:
      - Some not used features (I2C, ADC, USART, all timers but number 0)
        are permanently disabled.
      - The display deactivates after the screensaver time (set with <Sss>).
      - After 3 minutes auto power off activates the deep sleep mode.
      Note: In deep sleep mode USB and all timers (clock) are disabled.
      Note: While the clock is active <WATCH> neither auto poweroff nor the
            screensaver is active.

    First start
      Sss, Slit, Fsave


    ASCII TABLE:

      DEC     |  0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5
          HEX |  0 1 2 3 4 5 6 7 8 9 a b c d e f
      ------------------------------------------
      032 20  |    ! " # $ % & ' ( ) * + , - . /
      048 30  |  0 1 2 3 4 5 6 7 8 9 : ; < = > ?
      064 40  |  @ A B C D E F G H I J K L M N O
      080 50  |  P Q R S T U V W X Y Z [ \ ] ^ _
      096 60  |  ` a b c d e f g h i j k l m n o
      112 70  |  p q r s t u v w x y z { | } ~



  Leonardo's definitions for modifier keys (Key Hex Dec):
  KEY_LEFT_CTRL  0x80  128
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
*/

/************************************************************
  SYSTEM, KERNEL
*************************************************************/

// INCLUDES
//#include <SPI.h>       // Needed to control screen
#include <avr/power.h> // Needed for power management
#include <avr/sleep.h>
#include <EEPROM.h>    // For saving data to EEPROM

// DEFINES
#define EECONTRAST 0 // EEPROM address to save screencontrast
#define EESCREENOFF 1 // EEPROM address to save screenofftime
#define FRAMERATE 10 // Maximal number of screen refreshes per second (>3)
#define SCREENWIDTH 64 // Virtual screen width (quarter of real screen)
#define SCREENHEIGHT 32 // Virtual screen height (quarter of real screen)
#define SCREENBYTES 256 // Number of bytes to address SCREEN (SCREENWIDTH x SCREENHEIGHT)/8
#define KEYREPEATTIME 250 // Time in ms when pressed key repeats
#define MAXCHAR 10 // Maximum of characters in a line (due to font size)
#define MAXCHAR2 5
#define MAXCHAR4 3
#define MAXLIN 4 // Maximum of lines
#define MAXLIN2 2
#define MAXLIN4
#define PRINTMAXX 59 // Boundaries to restrict coordinates
#define PRINTMAXY 3
#define PRINT2MAXX 54
#define PRINT2MAXY 2
#define PRINT4MAXX 44
#define PRINT4MAXY 0

// Pins, ports
// Display
#define PIN_CS 12       // CS pin number
#define CS_PORT PORTD   // CS port
#define CS_BIT PORTD6   // CS physical bit number
#define PIN_DC 4        // DC pin number
#define DC_PORT PORTD   // DC port
#define DC_BIT PORTD4   // DC physical bit number
#define PIN_RST 6       // RST pin number
#define RST_PORT PORTD  // RST port
#define RST_BIT PORTD7  // RST physical bit number
// RGB LED
#define RED_LED 10           // Red LED pin
#define RED_LED_PORT PORTB   // Red LED port
#define RED_LED_BIT PORTB6   // Red LED physical bit number
#define GREEN_LED 11         // Green LED pin
#define GREEN_LED_PORT PORTB // Red LED port
#define GREEN_LED_BIT PORTB7 // Green LED physical bit number
#define BLUE_LED 9           // Blue LED pin
#define BLUE_LED_PORT PORTB  // Blue LED port
#define BLUE_LED_BIT PORTB5  // Blue LED physical bit number
// Buttons
#define LEFT_BUTTON _BV(5)  // Left button value for functions requiring a bitmask
#define RIGHT_BUTTON _BV(6) // Right button value for functions requiring a bitmask
#define UP_BUTTON _BV(7)    // The Up button value for functions requiring a bitmask
#define DOWN_BUTTON _BV(4)  // The Down button value for functions requiring a bitmask
#define A_BUTTON _BV(3)     // The A button value for functions requiring a bitmask
#define B_BUTTON _BV(2)     // The B button value for functions requiring a bitmask
#define PIN_LEFT_BUTTON A2 // Left Button
#define LEFT_BUTTON_PORT PORTF
#define LEFT_BUTTON_PORTIN PINF
#define LEFT_BUTTON_DDR DDRF
#define LEFT_BUTTON_BIT PORTF5
#define PIN_RIGHT_BUTTON A1 // Right button
#define RIGHT_BUTTON_PORT PORTF
#define RIGHT_BUTTON_PORTIN PINF
#define RIGHT_BUTTON_DDR DDRF
#define RIGHT_BUTTON_BIT PORTF6
#define PIN_UP_BUTTON A0
#define UP_BUTTON_PORT PORTF // Up button
#define UP_BUTTON_PORTIN PINF
#define UP_BUTTON_DDR DDRF
#define UP_BUTTON_BIT PORTF7
#define PIN_DOWN_BUTTON A3 // Down button
#define DOWN_BUTTON_PORT PORTF
#define DOWN_BUTTON_PORTIN PINF
#define DOWN_BUTTON_DDR DDRF
#define DOWN_BUTTON_BIT PORTF4
#define PIN_A_BUTTON 7 // A button
#define A_BUTTON_PORT PORTE
#define A_BUTTON_PORTIN PINE
#define A_BUTTON_DDR DDRE
#define A_BUTTON_BIT PORTE6
#define PIN_B_BUTTON 8 // B button
#define B_BUTTON_PORT PORTB
#define B_BUTTON_PORTIN PINB
#define B_BUTTON_DDR DDRB
#define B_BUTTON_BIT PORTB4
// Speaker
#define PIN_SPEAKER_1 5  // Pin number of the first lead of the speaker
#define PIN_SPEAKER_2 13 // Pin number of the second lead of the speaker
#define SPEAKER_1_PORT PORTC
#define SPEAKER_1_DDR DDRC
#define SPEAKER_1_BIT PORTC6
#define SPEAKER_2_PORT PORTC
#define SPEAKER_2_DDR DDRC
#define SPEAKER_2_BIT PORTC7
#define RAND_SEED_IN A4
#define RAND_SEED_IN_PORT PORTF
#define RAND_SEED_IN_BIT PORTF1
#define RAND_SEED_IN_ADMUX (_BV(REFS0) | _BV(REFS1) | _BV(MUX0))
// SPI interface
#define SPI_MISO_PORT PORTB
#define SPI_MISO_BIT PORTB3
#define SPI_MOSI_PORT PORTB
#define SPI_MOSI_BIT PORTB2
#define SPI_SCK_PORT PORTB
#define SPI_SCK_BIT PORTB1
#define SPI_SS_PORT PORTB
#define SPI_SS_BIT PORTB0


// FONTS
const byte font5x8 [] PROGMEM = {
  0x00, 0x00, 0x00, 0x00, 0x00, // sp
  0x00, 0x00, 0x2f, 0x00, 0x00, // !
  0x00, 0x07, 0x00, 0x07, 0x00, // "
  0x14, 0x7f, 0x14, 0x7f, 0x14, // #
  0x24, 0x2a, 0x7f, 0x2a, 0x12, // $
  0x62, 0x64, 0x08, 0x13, 0x23, // %
  0x36, 0x49, 0x55, 0x22, 0x50, // &
  0x00, 0x05, 0x03, 0x00, 0x00, // '
  0x00, 0x1c, 0x22, 0x41, 0x00, // (
  0x00, 0x41, 0x22, 0x1c, 0x00, // )
  0x14, 0x08, 0x3E, 0x08, 0x14, // *
  0x08, 0x08, 0x3E, 0x08, 0x08, // +
  0x00, 0x00, 0xA0, 0x60, 0x00, // ,
  0x08, 0x08, 0x08, 0x08, 0x08, // -
  0x00, 0x60, 0x60, 0x00, 0x00, // .
  0x20, 0x10, 0x08, 0x04, 0x02, // /
  //0x3E, 0x51, 0x49, 0x45, 0x3E, // 0
  0x3E, 0x41, 0x49, 0x41, 0x3E, // 0 new
  0x00, 0x42, 0x7F, 0x40, 0x00, // 1
  0x42, 0x61, 0x51, 0x49, 0x46, // 2
  //0x21, 0x41, 0x45, 0x4B, 0x31, // 3
  0x22, 0x41, 0x49, 0x49, 0x36, // 3 new
  //0x18, 0x14, 0x12, 0x7F, 0x10, // 4
  0x08, 0x0c, 0x0a, 0x7F, 0x08, // 4 new
  0x27, 0x45, 0x45, 0x45, 0x39, // 5
  0x3C, 0x4A, 0x49, 0x49, 0x30, // 6
  0x01, 0x71, 0x09, 0x05, 0x03, // 7
  0x36, 0x49, 0x49, 0x49, 0x36, // 8
  0x06, 0x49, 0x49, 0x29, 0x1E, // 9
  0x00, 0x36, 0x36, 0x00, 0x00, // :
  //0x00, 0x00, 0x14, 0x00, 0x00, // : tiny
  0x00, 0x56, 0x36, 0x00, 0x00, // ;
  0x08, 0x14, 0x22, 0x41, 0x00, // <
  0x14, 0x14, 0x14, 0x14, 0x14, // =
  0x00, 0x41, 0x22, 0x14, 0x08, // >
  0x02, 0x01, 0x51, 0x09, 0x06, // ?
  0x32, 0x49, 0x59, 0x51, 0x3E, // @
  0x7C, 0x12, 0x11, 0x12, 0x7C, // A
  0x7F, 0x49, 0x49, 0x49, 0x36, // B
  0x3E, 0x41, 0x41, 0x41, 0x22, // C
  0x7F, 0x41, 0x41, 0x22, 0x1C, // D
  0x7F, 0x49, 0x49, 0x49, 0x41, // E
  0x7F, 0x09, 0x09, 0x09, 0x01, // F
  0x3E, 0x41, 0x49, 0x49, 0x7A, // G
  0x7F, 0x08, 0x08, 0x08, 0x7F, // H
  0x00, 0x41, 0x7F, 0x41, 0x00, // I
  0x20, 0x40, 0x41, 0x3F, 0x01, // J
  0x7F, 0x08, 0x14, 0x22, 0x41, // K
  0x7F, 0x40, 0x40, 0x40, 0x40, // L
  0x7F, 0x02, 0x0C, 0x02, 0x7F, // M
  0x7F, 0x04, 0x08, 0x10, 0x7F, // N
  0x3E, 0x41, 0x41, 0x41, 0x3E, // O
  0x7F, 0x09, 0x09, 0x09, 0x06, // P
  0x3E, 0x41, 0x51, 0x21, 0x5E, // Q
  0x7F, 0x09, 0x19, 0x29, 0x46, // R
  0x46, 0x49, 0x49, 0x49, 0x31, // S
  0x01, 0x01, 0x7F, 0x01, 0x01, // T
  0x3F, 0x40, 0x40, 0x40, 0x3F, // U
  0x1F, 0x20, 0x40, 0x20, 0x1F, // V
  0x3F, 0x40, 0x38, 0x40, 0x3F, // W
  0x63, 0x14, 0x08, 0x14, 0x63, // X
  0x07, 0x08, 0x70, 0x08, 0x07, // Y
  0x61, 0x51, 0x49, 0x45, 0x43, // Z
  0x00, 0x7F, 0x41, 0x41, 0x00, // [
  0x02, 0x04, 0x08, 0x10, 0x20, // b
  0x00, 0x41, 0x41, 0x7F, 0x00, // ]
  0x04, 0x02, 0x01, 0x02, 0x04, // ^
  0x40, 0x40, 0x40, 0x40, 0x40, // _
  //0x00, 0x01, 0x02, 0x04, 0x00, // '
  //0x14, 0x36, 0x77, 0x36, 0x14, // ' updown
  //0x04, 0x7e, 0x7f, 0x7e, 0x04, // shift1
  //0x04, 0xfe, 0xff, 0xfe, 0x04, // shift2
  0x04, 0xbe, 0xbf, 0xbe, 0x04, // shift3
  0x20, 0x54, 0x54, 0x54, 0x78, // a
  0x7F, 0x48, 0x44, 0x44, 0x38, // b
  0x38, 0x44, 0x44, 0x44, 0x20, // c
  0x38, 0x44, 0x44, 0x48, 0x7F, // d
  0x38, 0x54, 0x54, 0x54, 0x18, // e
  0x08, 0x7E, 0x09, 0x01, 0x02, // f
  0x18, 0xA4, 0xA4, 0xA4, 0x7C, // g
  0x7F, 0x08, 0x04, 0x04, 0x78, // h
  0x00, 0x44, 0x7D, 0x40, 0x00, // i
  0x40, 0x80, 0x84, 0x7D, 0x00, // j
  0x7F, 0x10, 0x28, 0x44, 0x00, // k
  0x00, 0x41, 0x7F, 0x40, 0x00, // l
  0x7C, 0x04, 0x18, 0x04, 0x78, // m
  0x7C, 0x08, 0x04, 0x04, 0x78, // n
  0x38, 0x44, 0x44, 0x44, 0x38, // o
  0xFC, 0x24, 0x24, 0x24, 0x18, // p
  0x18, 0x24, 0x24, 0x18, 0xFC, // q
  0x7C, 0x08, 0x04, 0x04, 0x08, // r
  0x48, 0x54, 0x54, 0x54, 0x20, // s
  0x04, 0x3F, 0x44, 0x40, 0x20, // t
  0x3C, 0x40, 0x40, 0x20, 0x7C, // u
  0x1C, 0x20, 0x40, 0x20, 0x1C, // v
  0x3C, 0x40, 0x30, 0x40, 0x3C, // w
  0x44, 0x28, 0x10, 0x28, 0x44, // x
  0x1C, 0xA0, 0xA0, 0xA0, 0x7C, // y
  0x44, 0x64, 0x54, 0x4C, 0x44, // z
  // 0x00, 0x08, 0x36, 0x41, 0x00, // {
  0x08, 0x1c, 0x3e, 0x7f, 0x00, // { left
  0x00, 0x00, 0xff, 0x00, 0x00, // |
  //0x41, 0x36, 0x08, 0x00, 0x00, // }
  0x00, 0x7f, 0x3e, 0x1c, 0x08, // } right
  //0x10, 0x08, 0x18, 0x10, 0x08  // ~
  0x06, 0x7f, 0x01, 0x7f, 0x01  // paragraph
};

// Macros
#define _abs(x) ((x<0)?(-x):(x))      // abs()-substitute macro
#define _min(a,b) (((a)<(b))?(a):(b)) // mix()-substitute macro
#define _max(a,b) (((a)>(b))?(a):(b)) // max()-substitute macro

// GLOBAL VARIABLES
static byte sbuf[SCREENBYTES]; // Buffer for virtual screen (costs 256 bytes of dynamic memory)
static byte eachframemillis, thisframestart, lastframedurationms; // Framing times
static boolean justrendered; // True if frame was just rendered
static boolean isscreensave = false; // True if screensaver is active
static boolean isshift = false; // True if button A was holded (= shift)
static boolean istorch; // True if torch is on


// BOOT, BEGIN
void SPItransfer(byte data) { // Write to the SPI bus (MOSI pin)
  SPDR = data;
  asm volatile("nop"); // Tiny delay before wait
  while (!(SPSR & _BV(SPIF))) {} // Wait for byte to be sent
}

static void ScreenCommandMode(void) { // Set screen to command mode
  bitClear(DC_PORT, DC_BIT);
}

static void ScreenDataMode(void) { // Set screen to data mode
  bitSet(DC_PORT, DC_BIT);
}

static void bootpins(void) { // Declare and boot port pins
  PORTB |= _BV(RED_LED_BIT) | _BV(GREEN_LED_BIT) | _BV(BLUE_LED_BIT) | // Port B
           _BV(B_BUTTON_BIT);
  DDRB  &= ~(_BV(B_BUTTON_BIT) | _BV(SPI_MISO_BIT));
  DDRB  |= _BV(RED_LED_BIT) | _BV(GREEN_LED_BIT) | _BV(BLUE_LED_BIT) |
           _BV(SPI_MOSI_BIT) | _BV(SPI_SCK_BIT) | _BV(SPI_SS_BIT);
  PORTD |= _BV(CS_BIT); // Port D
  PORTD &= ~(_BV(RST_BIT));
  DDRD  |= _BV(RST_BIT) | _BV(CS_BIT) | _BV(DC_BIT);
  PORTE |= _BV(A_BUTTON_BIT); // Port E
  DDRE  &= ~(_BV(A_BUTTON_BIT));
  PORTF |= _BV(LEFT_BUTTON_BIT) | _BV(RIGHT_BUTTON_BIT) | // Port F
           _BV(UP_BUTTON_BIT) | _BV(DOWN_BUTTON_BIT);
  PORTF &= ~(_BV(RAND_SEED_IN_BIT));
  DDRF  &= ~(_BV(LEFT_BUTTON_BIT) | _BV(RIGHT_BUTTON_BIT) |
             _BV(UP_BUTTON_BIT) | _BV(DOWN_BUTTON_BIT) |
             _BV(RAND_SEED_IN_BIT));
}

static void bootSPI(void) { // Initialize the SPI interface for the display
  SPCR = _BV(SPE) | _BV(MSTR); // master, mode 0, MSB first, CPU clock / 2 (8MHz)
  SPSR = _BV(SPI2X);
}

const byte PROGMEM ScreenBootProgram[] = { // SSD1306 boot sequence
  // 0xAE, // Display Off
  0xD5, 0xF0, // Set Display Clock Divisor v = 0xF0 - default is 0x80
  // 0xA8, 0x3F, // Set Multiplex Ratio v = 0x3F
  // 0xD3, 0x00, // Set Display Offset v = 0
  // 0x40, // Set Start Line (0)
  0x8D, 0x14, // Charge Pump Setting v = enable (0x14) - default is disabled
  0xA1, // Set Segment Re-map (A0) | (b0001) - default is (b0000)
  0xC8, // Set COM Output Scan Direction
  // 0xDA, 0x12, // Set COM Pins v
  0x81, 0xCF, // Set Contrast v = 0xCF
  0xD9, 0xF1, // Set Precharge = 0xF1
  // 0xDB, 0x40, // Set VCom Detect
  // 0xA4, // Entire Display ON
  // 0xA6, // Set normal/inverse display
  0xAF, // Display On
  0x20, 0x00, // Set display mode = horizontal addressing mode (0x00)
  // 0x21, 0x00, COLUMN_ADDRESS_END, // Set col address range
  // 0x22, 0x00, PAGE_ADDRESS_END // Set page address range
};

static void bootscreen(void) { // Boot screen - reset the display
  delayshort(5); // Reset pin should be low here - let it stay low a while
  bitSet(RST_PORT, RST_BIT); // Set reset pin high to come out of reset
  delayshort(5); // Wait
  bitClear(CS_PORT, CS_BIT); // Select the display (permanently, since nothing else is using SPI)

  ScreenCommandMode(); // Run customized boot-up command sequence
  for (uint8_t i = 0; i < sizeof(ScreenBootProgram); i++)
    SPItransfer(pgm_read_byte(ScreenBootProgram + i));
  ScreenDataMode();
}

static void setframerate(byte rate) { // Calculate frameduration
  eachframemillis = 1000 / rate;
}


// SYSTEM
static void setscreencontrast(byte c) { // Set screen contrast
  ScreenCommandMode();
  SPItransfer(0x81);
  SPItransfer(c);
  ScreenDataMode();
  EEPROM.write(EECONTRAST, c);
}

static void sbufclr(void) { // Clear display buffer
  for (int i = 0; i < SCREENBYTES; i++) sbuf[i] = NULL;
  //memset(sbuf,NULL,SCREENBYTES);
}

static byte expand4bit(byte b) { // 0000abcd  Expand 4 bits (lower nibble)
  b = (b | (b << 2)) & 0x33;     // 00ab00cd
  b = (b | (b << 1)) & 0x55;     // 0a0b0c0d
  return (b | (b << 1));         // aabbccdd
}

static byte expand2bit(byte b) { // Expand 2 bits 000000ab
  b = (b | (b << 3)) & 0x11;                   // 000a000b
  for (byte i = 0; i < 3; i++) b |= (b << 1);  // aaaabbbb
  return (b);
}

static void display(void) { // Print display buffer (64x32) to real screen (128x64)
  for (byte l = 0; l < MAXLIN; l++) { // Four lines
    for (byte k = 0; k < 2; k++) { // Two nibbles (double height)
      for (byte j = 0; j < SCREENWIDTH; j++) {
        byte tmp = expand4bit((sbuf[j + l * SCREENWIDTH] >> (k * 4)) & 0x0f); // Expand 0000abcd
        for (byte i = 0; i < 2; i++) SPItransfer(tmp); // Double width
      }
    }
  }
}

static void print(int x, int y, byte ch) { // Print character to sbuf[]
  x = _min(x, PRINTMAXX); y = _min(y, PRINTMAXY); // Restrict x and y
  if (ch >= ' ' && ch <= '~')
    for (int i = 0; i < 5; i++)
      sbuf[x + y * SCREENWIDTH + i] = pgm_read_byte(&font5x8[5 * (ch - ' ') + i]);
}

static void print2(int x, int y, byte ch, boolean slim) { // Print double sized (slim) character
  x = slim ? _min(x, PRINTMAXX) : _min(x, PRINT2MAXX); // Restrict x and y
  y = _min(y, PRINT2MAXY);
  for (byte k = 0; k < 2; k++) { // Two nibbles
    for (byte j = 0; j < 5; j++) {
      byte tmp = expand4bit((pgm_read_byte(&font5x8[5 * (ch - ' ') + j]) >> (k * 4)) & 0x0f); // Expand 0000abcd
      if (slim) sbuf[x + (y + k) * SCREENWIDTH + 1 * j] = tmp;
      else for (byte i = 0; i < 2; i++) sbuf[x + (y + k) * SCREENWIDTH + 2 * j + i] = tmp;
    }
  }
}

static void print4(byte x, byte y, byte ch, boolean slim) { // Print quad-sized character
  x = slim ? _min(x, PRINT2MAXX) : _min(x, PRINT4MAXX); // Restrict x and y
  y = _min(y, PRINT4MAXY);
  for (byte k = 0; k < 4; k++) { // 4 pairs of bits (ab cd ef gh)
    for (byte j = 0; j < 5; j++) {
      byte tmp = expand2bit((pgm_read_byte(&font5x8[5 * (ch - ' ') + j]) >> (2 * k)) & 0x03); // Expand 000000ab
      if (slim) for (byte i = 0; i < 2; i++) sbuf[x + (y + k) * SCREENWIDTH + 2 * j + i] = tmp;
      else for (byte i = 0; i < 4; i++) sbuf[x + (y + k) * SCREENWIDTH + 4 * j + i] = tmp;
    }
  }
}

static void delayshort(byte ms) { // Delay in ms with 8 bit duration
  delay((unsigned long) ms);
}

static void bootpowersaving(void) {
  PRR0 = _BV(PRTWI) | _BV(PRADC); // Disable I2C-Interface and ADC
  PRR1 |= _BV(PRUSART1); // Disable USART1
  //power_adc_disable();
  power_usart0_disable();
  power_twi_disable();
  power_timer1_disable(); // Disable timer 1...3 (0 is used for millis())
  power_timer2_disable();
  power_timer3_disable();
  power_usart1_disable();
  //power_usb_disable(); // Disable USB (needs reset to reprogram)
}

static void screenoff(void) { // Shut down the display
  ScreenCommandMode();
  SPItransfer(0xAE); // Display off
  SPItransfer(0x8D); // Disable charge pump
  SPItransfer(0x10);
  delayshort(100);
  bitClear(RST_PORT, RST_BIT); // Set RST to low (reset state)
}

static void screenon(void) { // Restart the display after a displayOff()
  bootscreen();
  setscreencontrast(EEPROM.read(EECONTRAST));
}

static void idle(void) { // Idle, while waiting for next frame
  SMCR = _BV(SE); // Select idle mode and enable sleeping
  sleep_cpu();
  SMCR = 0; // Disable sleeping
}

static bool nextFrame(void) { // Wait (idle) for next frame
  byte now = (byte) millis(), framedurationms = now - thisframestart;
  if (justrendered) {
    lastframedurationms = framedurationms;
    justrendered = false;
    return false;
  }
  else if (framedurationms < eachframemillis) {
    if (++framedurationms < eachframemillis) idle();
    return false;
  }
  justrendered = true;
  thisframestart = now;
  return true;
}

static void clrline(byte from, byte to) { // Clears line(s) (0...3)
  for (byte j = from; j <= _min(to, MAXLIN - 1); j++)
    for (byte i = 0; i < SCREENWIDTH; i++) sbuf[i + j * SCREENWIDTH] = NULL;
}

static void cleartextarea(byte x1, byte y1, byte x2, byte y2) { // Clear area
  x1 = _min(x1, PRINTMAXX); y1 = _min(y1, PRINTMAXY); // Restrict x and y
  x2 = _min(x2, PRINTMAXX); y2 = _min(y2, PRINTMAXY);
  for (byte j = y1; j <= y2; j++)
    for (byte i = x1; i < x1 + x2; i++) sbuf[i + j * SCREENWIDTH] = NULL;
}

static void printstring(byte x, byte y, char* s) { // Print string at x, y (normal size)
  byte tmp = strlen(s);
  cleartextarea(x, y, tmp * 6, y);
  for (byte i = 0; i < tmp && i < MAXCHAR; i++) print(x + i * 6, y, s[i]);
}
static void printstring2(byte x, byte y, char* s) { // Print string at x, y (double size)
  byte tmp = strlen(s);
  cleartextarea(x, y, tmp * 12, y + 1);
  for (byte i = 0; i < tmp && i < MAXCHAR2; i++) print2(x + i * 12, y, s[i], false);
}
static void printstring4(byte x, byte y, char* s) { // Print string at x, y (quad size)
  byte tmp = strlen(s);
  cleartextarea(x, y, tmp * 22, y + 3);
  for (byte i = 0; i < tmp && i < MAXCHAR4; i++) print4(x + i * 22, y, s[i], false);
}

static byte button() { // Get keycode for pressed button
  if (!digitalRead(PIN_B_BUTTON)) return ('B');
  if (!digitalRead(PIN_A_BUTTON)) return ('A');
  if (!digitalRead(PIN_RIGHT_BUTTON)) return ('R');
  if (!digitalRead(PIN_DOWN_BUTTON)) return ('D');
  if (!digitalRead(PIN_LEFT_BUTTON)) return ('L');
  if (!digitalRead(PIN_UP_BUTTON)) return ('U');
  return (NULL);
}

static byte getkey() { // Get one debounced key
  static byte oldkey = 0; // Needed for debouncing
  static long keytimestamp = 0; // Needed for key repeating
  byte k = button();
  if (k != oldkey || millis() - keytimestamp > KEYREPEATTIME) {
    oldkey = k;
    keytimestamp = millis();
    return (k);
  }
  return (NULL);
}

static void wakeupnow() {} // Dummy wakeup code

static void sleepnow(void) { // Power down - wake up by pressing A (interrupt 4)
  ledsoff();
  if (!isscreensave) screenoff(); // Display off only if screensaver didn't
  pinMode(A_BUTTON, INPUT_PULLUP);
  attachInterrupt(4, wakeupnow, LOW);
  set_sleep_mode(SLEEP_MODE_PWR_DOWN);
  sleep_enable();
  attachInterrupt(4, wakeupnow, LOW);
  sleep_mode();
  // SLEEP ... till A (interrupt 4 on Leonardo) is pressed //
  sleep_disable();
  detachInterrupt(4);
  screenon(); // Display on
  delayshort(200);
  ledsoff();
  //power_all_enable();
  //bootpowersaving();
}

static int rawadc() { // Measure Vcc
  power_adc_enable();
  ADMUX = (_BV(REFS0) | _BV(MUX4) | _BV(MUX3) | _BV(MUX2) | _BV(MUX1)); // Set voltage bits
  delayshort(2); // Wait for ADMUX setting to settle
  ADCSRA |= _BV(ADSC); // Start conversion
  while (bit_is_set(ADCSRA, ADSC)); // Measuring
  power_adc_disable();
  return (ADC);
}

static void flashlightmode(void) { //  Secure wait to flash software if  UP is pressed when switching on
  if (!digitalRead(PIN_UP_BUTTON)) {
    sbufclr();
    display();
    digitalWrite(BLUE_LED, LOW);
    while (true) {}; // Wait
  }
}

static void ledsoff(void) { // Disable LEDs
  digitalWrite(RED_LED, HIGH); digitalWrite(GREEN_LED, HIGH); digitalWrite(BLUE_LED, HIGH);
  TXLED1; RXLED1;
  istorch = false;
}



/************************************************************
  AOS, APPLICATION
*************************************************************/


// INCLUDES
#include <Keyboard.h>  // For sending keyboard presses via USB


// DEFINES
#define MAXBYTE 255 // Maximal value of byte
#define MAXSTRBUF 12 // Maximal length of strbuf[]
#define CMDLINESTEP 8 // Page up/down step for commands
#define BYTESTACKSIZE 16 // Size of bytestack
#define FLOATSTACKSIZE 4 // Size of floatstack
#define CMDOFFSET 32 // Offset to align commands to ascii characters
#define NUMBERFKEYS 4 // Number of Fn-keys
#define NUMBERCONSTANTS 10 // Number of constants
#define PRGPTRSTART 4 // Start of commands in prg[] (after header)
#define TINYNUMBER 1e-7 // Number for rounding to 0
#define MAXITERATE 100 // Maximal number of Taylor series loops to iterate
#define RAD 57.29578 // 180/PI
#define MAXEE 38 // Maximal EE for Y10X
#define CURSORSIGN '{' // Cursor character
#define LFSIGN '~' // Line feed character
#define PRINTINTERVAL 10 // Prints time every 10 s
#define TIMECORRECTION -58 // Additional time in ms per PRINTINTERVAL  slower -100...-60...-55...0 faster
#define DAILYTIMECORRECTION 0 // Additional time in ms every 24/12 hours
#define PAUSE 1000 // Pause time in ms
#define DELTAX 1E-4 // Delta for solving
#define INTSTRIPES 10 // Number of Simpson stripes when integrating
#define MINSCREENOFFTIME 5 // Lowest time for screensaver in s
#define POWEROFFTIME 180 // Time for auto poweroff in s
#define PGOTO "Pgoto" // Define for goto command
#define PPB "PpB" // Define for push to bytestack command
#define PRUN "Prun" // Define for execute program
#define PSUB "Psub" // Define for gosub
#define EEFKEYS 2 // EEPROM address for 4 function keys
#define EESTACK 6 // EEPROM address for floatstack and mem (5 doubles)
#define EECONSTANT 26 // Begin of EEPROM memory to store constants
#define EEDISKSTART 66 // Begin of EEPROM memory to store files
#define EEDISKEND 2559 // End of EEPROM memory to store files

// Keychars
#define KEY_B 'B'
#define KEY_A 'A'
#define KEY_R 'R'
#define KEY_D 'D'
#define KEY_L 'L'
#define KEY_U 'U'
#define KEY_SHIFT_R 'r'
#define KEY_SHIFT_D 'd'
#define KEY_SHIFT_L 'l'
#define KEY_SHIFT_U 'u'

// Modes
#define CMDMODE 1     // Entering command
#define INMODE 2      // Input byte
#define KBDMODE 3     // Keyboard mode
#define DISKMODE 4    // File system (df, rm, load, save)
#define MSGMODE 5     // View/print message
#define PRGEDITMODE 6 // Edit prg[]
#define TXTEDITMODE 7 // Treat prg[] as text file
#define CLKMODE 8     // Show clock
#define YESNOMODE 9   // Decision mode
#define RUNMODE 10    // Run/execute prg
#define SOLVEMODE 11  // SOLVE mode
#define INTMODE 12    // INT mode
//#define CONSTMODE 13  // Select constant (float)

// Macros
#define _ones(x) ((x)%10)             // Calculates ones unit
#define _tens(x) (((x)/10)%10)        // Calculates tens unit
#define _huns(x) (((x)/100)%10)       // Calculates hundreds unit
#define _tsds(x) (((x)/1000)%10)      // Calculates thousands unit

// GLOBAL VARIABLES
static byte mode, oldmode, oldoldmode; // Mode (see mode table) and previous mode
static byte screencontrast; // Contrast of screen
static byte cmdselect, byteselect; // Selected command/byte
static byte linestep, minbyte, maxbyte; // Limits for selecting
static char strbuf[MAXSTRBUF]; // Holds string to print
static byte bytestack[BYTESTACKSIZE]; // Main data stack
static double floatstack[FLOATSTACKSIZE], mem; // Float stack (XYZT) and memory
//const char hex[] = "0123456789ABCDEF"; // For converting nibble to hex
static byte f[NUMBERFKEYS]; // Function keys holding their commands
static byte key; // Assigned key
static boolean isterm; // True, if terminal controls
static byte prgptr, oldprgptr; // Pointer to actual step or cursor in prg[]
static boolean issubprg; // True if subprogram will be executed
static int fsptr; // Pointer to file system of EEPROM
static byte prg[2][MAXBYTE], prgnr; // Program
static byte msg; // Number of message to print
static boolean isinsertbyte; // True if byte should be insertet in prg[]
static boolean isshowbytestack; // True if bytestack should be displayed (XYZ)
static boolean isshowfloatstack; // True if floatstack should be displayed (X)
static boolean iscalcmode; // True if input goes to floatstack
static boolean iscalcmenu; // True if calculator demands one command
static boolean iscalcrun; // True if Prun was called while in CALC
static boolean isnewnumber; // True if stack has to be lifted before entering a new number
static boolean ispushed; // True if stack was already pushed by ENTER
static byte decimals; // Number of decimals entered - used for input after decimal dot
static boolean isdot; // True if dot was pressed and decimals will be entered
static double sx, sxx, sy, sxy; // Statistical/LR variables
static byte sn; // Statistics - number of data points
static boolean issolve, isint; // True while in SOLVE- or INT-mode
static byte cycles; // Cycles in SOLVE or INT precedures
static double x0, x1, f0, f1; // Old and new solve-value
static double inta, intb, intdelta, intx, intf; // INT variables
static boolean iscursor; // True if prg[] contents a cursor
static unsigned long clockmillis = 0; // Time
static unsigned long starttime = 0; // Marker for time
static long screenofftimestamp; // Screen off timestamp
static byte screenofftime = MINSCREENOFFTIME; // Screen off time in s
static long powerofftimestamp; // Poweroff timestamp
static byte pruncmdnumber = 0; // Number of Prun command (needed if CALC calls Prun)

// Messages
#define MSGABOUT 0
#define MSGOK 3
#define MSGNOMEM 4
#define MSGSTOP 5
#define MSGYESNO 6
#define MSGRUN 7
const char m0[] PROGMEM = "A O S"; // Program name
const char m1[] PROGMEM = "v1.0  2018"; // Version
const char m2[] PROGMEM = "(c) deetee"; // Author
const char m3[] PROGMEM = "OK";    // OK
const char m4[] PROGMEM = "NOMEM"; // No EEPROM
const char m5[] PROGMEM = "STOP";  // Program execution stopped
const char m6[] PROGMEM = "{NO   YES}"; // Program Yes/No decision text
const char m7[] PROGMEM = "RUNNING"; // Program running message
const char* const msg_table[] PROGMEM = {m0, m1, m2, m3, m4, m5, m6, m7};

// COMMANDS (command offset = 32)
const char c0[] PROGMEM = "about";  // Print about message
const char c1[] PROGMEM = "B2F";    // Pull bytestack[0] and push (cast to double) to floatstack[0]
const char c2[] PROGMEM = "B}F";    // Flush bytestack to calculator
const char c3[] PROGMEM = "Bclr";   // Clear bytestack
const char c4[] PROGMEM = "Bprnt";  // Print bytestack as string
const char c5[] PROGMEM = "Bshow";  // Show bytestack on display
const char c6[] PROGMEM = "CALC";   // Calculator mode (RPN)
const char c7[] PROGMEM = "Cload";  // Load constant with number floatstack[0] to floatstack
const char c8[] PROGMEM = "Csave";  // Save floatstack[1] to EEPROM at slot floatstack[0]
const char c9[] PROGMEM = "D";      // Disk operation system (save, load, delete)
const char c10[] PROGMEM = "Dfmt";  // Format disk system
const char c11[] PROGMEM = "F!";    // Calculate GAMMA/factorial
const char c12[] PROGMEM = "F*";    // Calculate Y*X
const char c13[] PROGMEM = "F+";    // Calculate Y+X
const char c14[] PROGMEM = "F-";    // Calculate Y-X
const char c15[] PROGMEM = "F/";    // Calculate Y/X
const char c16[] PROGMEM = "F2h";   // Convert H.MS to H
const char c17[] PROGMEM = "F2hms"; // Convert H to H.MS
const char c18[] PROGMEM = "F2p";   // Convert rectangular to polar coordinates
const char c19[] PROGMEM = "F2r";   // Convert polar to rectangular coordinates
const char c20[] PROGMEM = "F<";    // True (ptr+2) if X < Y on floatstack
const char c21[] PROGMEM = "F<=";   // True (ptr+2) if X <= Y on floatstack
const char c22[] PROGMEM = "F<>";   // True (ptr+2) if X <> Y on floatstack
const char c23[] PROGMEM = "F=";    // True (ptr+2) if X = Y on floatstack
const char c24[] PROGMEM = "F>";    // True (ptr+2) if X > Y on floatstack
const char c25[] PROGMEM = "F>=";   // True (ptr+2) if X >= Y on floatstack
const char c26[] PROGMEM = "Facos"; // Calculate ACOS(X)
const char c27[] PROGMEM = "Facsh"; // Calculate ACOSH(X)
const char c28[] PROGMEM = "Fannu"; // Calculate annuity/preset value (Y: rate, X:years)
const char c29[] PROGMEM = "Fasin"; // Calculate ASIN(X)
const char c30[] PROGMEM = "Fasnh"; // Calculate ASINH(X)
const char c31[] PROGMEM = "Fatan"; // Calculate ATAN(X)
const char c32[] PROGMEM = "Fatnh"; // Calculate ATANH(X)
const char c33[] PROGMEM = "Fbatt"; // Push the value of battery voltage to floatstack
const char c34[] PROGMEM = "Fce";   // Clear last entry
const char c35[] PROGMEM = "Fchs";  // Change sign of X
const char c36[] PROGMEM = "Fclx";  // Clear X
const char c37[] PROGMEM = "Fcos";  // Calculate COS(X)
const char c38[] PROGMEM = "Fcosh"; // Calculate COSH(X)
const char c39[] PROGMEM = "FEE";   // Calculate EE (Y*10^X) on floatstack
const char c40[] PROGMEM = "Fexp";  // Calculate EXP(X)
const char c41[] PROGMEM = "Fgaus"; // Calculate CDF (Cumulative Distribution Function) and PDF (Probability Density Function)
const char c42[] PROGMEM = "Fint";  // INTegrate function represented in prg[] between Y and X
const char c43[] PROGMEM = "Finv";  // Calculate 1/X
const char c44[] PROGMEM = "Fln";   // Calculate LN( X)
const char c45[] PROGMEM = "FLR";   // Calculate L.R. (Xprev, Yprev)
const char c46[] PROGMEM = "Fpow";  // Calculate POW(Y,X) ... Y^X
const char c47[] PROGMEM = "Fpush"; // Push floatstack
const char c48[] PROGMEM = "Frcl";  // Recall float memory to X
const char c49[] PROGMEM = "Frot";  // Rotate floatstack (down)
const char c50[] PROGMEM = "Fsave"; // Save floatstack and mem to EEPROM
const char c51[] PROGMEM = "Fshow"; // Show floatstack on display
const char c52[] PROGMEM = "Fsin";  // Calculate SIN(X)
const char c53[] PROGMEM = "Fsinh"; // Calculate SINH(X)
const char c54[] PROGMEM = "Fsolv"; // SOLVE function represented in prg[]
const char c55[] PROGMEM = "Fsqrt"; // Calculate SQRT(X)
const char c56[] PROGMEM = "Fstat"; // Calculate statistics STAT ... X: mean Y: standard deviation
const char c57[] PROGMEM = "Fsto";  // Store X to float memory
const char c58[] PROGMEM = "Fsum";  // Process statistical data (SUM)
const char c59[] PROGMEM = "Fswap"; // Swap X and Y
const char c60[] PROGMEM = "Ftan";  // Calculate TAN(X)
const char c61[] PROGMEM = "Ftanh"; // Calculate TANH(X)
const char c62[] PROGMEM = "Ibyte"; // Input byte
const char c63[] PROGMEM = "Ichar"; // Input character
const char c64[] PROGMEM = "Inum";  // Input number
const char c65[] PROGMEM = "KBD";   // Keyboard emulator (HID keyboard via USB)
const char c66[] PROGMEM = "OFF";   // Power down till A is pressed (SLEEP_MODE_PWR_DOWN), shift+B
const char c67[] PROGMEM = "Pedit"; // Edit active program
const char c68[] PROGMEM = "Pexp";  // Export active program to Linux-PC
const char c69[] PROGMEM = PGOTO;   // Goto program step
const char c70[] PROGMEM = "Pimp";  // Import active program from Linux-PC
const char c71[] PROGMEM = "Pname"; // Set program name (ZYX)
const char c72[] PROGMEM = "Pnop";  // No operation
const char c73[] PROGMEM = PPB;     // Push value of next program step to bytestack
const char c74[] PROGMEM = "Ppse";  // Pause for PAUSE milliseconds
const char c75[] PROGMEM = PRUN;    // Run active program
const char c76[] PROGMEM = "Pstop"; // Stops program execution
const char c77[] PROGMEM = PSUB  ;  // Execute non-active program and return
const char c78[] PROGMEM = "Pswap"; // Toggle between  active and non-active program
const char c79[] PROGMEM = "Py/n";  // Decision ESC (ptr+1), NO (ptr+3) or YES (ptr+5)
const char c80[] PROGMEM = "Sfn";   // Set function keys and save to EEPROM (rdlu=TZYX)
const char c81[] PROGMEM = "Slit";  // Set contrast
const char c82[] PROGMEM = "Sss";   // Set screensaver time
const char c83[] PROGMEM = "TERM";  // Toggle terminal control
const char c84[] PROGMEM = "TORCH"; // Toggle torch
const char c85[] PROGMEM = "TXT";   // Text editor (treat prg[] as text file)
const char c86[] PROGMEM = "WATCH"; // Show watch (set with cursor keys, reset minute with B)
const char c87[] PROGMEM = "Wset";  // Set watch from bytestack (YY:XX)
const char* const cmd[] PROGMEM = {
  c0, c1, c2, c3, c4, c5, c6, c7, c8, c9, c10, c11, c12, c13, c14, c15, c16, c17, c18, c19, c20,
  c21, c22, c23, c24, c25, c26, c27, c28, c29, c30, c31, c32, c33, c34, c35, c36, c37, c38, c39, c40,
  c41, c42, c43, c44, c45, c46, c47, c48, c49, c50, c51, c52, c53, c54, c55, c56, c57, c58, c59, c60,
  c61, c62, c63, c64, c65, c66, c67, c68, c69, c70, c71, c72, c73, c74, c75, c76, c77, c78, c79, c80,
  c81, c82, c83, c84, c85, c86, c87
};
#define numberofcommands (sizeof(cmd)/sizeof(const char *))

// Function pointer array
static void about(void) {
  clrline(2, 3);
  strcpy_P(strbuf, (char*)pgm_read_word(&(msg_table[1])));
  printstring(0, 2, strbuf);
  strcpy_P(strbuf, (char*)pgm_read_word(&(msg_table[2])));
  printstring(0, 3, strbuf);
  oldmode = CMDMODE;
  mode = MSGMODE;
  msg = MSGABOUT;
}
static void B2F(void) {
  pushfloatstack();
  floatstack[0] = bytestack[0];
  pullbytestack();
}
static void BflushF(void) {
  byte tmp = 0;
  while (bytestack[tmp] && tmp < BYTESTACKSIZE) tmp++;
  for (byte i = tmp + 1; i > 0; i--) calculator(bytestack[i - 1]);
  Bclr();
}
static void Bclr(void) {
  for (byte i = 0; i < BYTESTACKSIZE; i++) bytestack[i] = NULL;
  //memset(bytestack, NULL, BYTESTACKSIZE);
}
static void Bprint(void) {
  byte tmp = 0;
  while (bytestack[tmp] && tmp < MAXCHAR) tmp++; // Find length of bytestack
  //for (byte i = 0; bytestack[i] && i < MAXCHAR; i++) tmp = i;
  fillstrbuf(MAXCHAR);
  for (byte i = tmp; i > 0; i--) strbuf[tmp - i] = bytestack[i - 1];
  clrline(0, 0);
  printstring(0, 0, strbuf);
  Bclr();
}
static void Bshow(void) {
  if (isshowbytestack) {
    isshowbytestack = false;
    clrline(1, 1);
  }
  else isshowbytestack = true;
}
static void calcmode(void) {
  if (iscalcmode) {
    iscalcmode = false;
    isshowfloatstack = false;
    clrline(0, 0);
  }
  else {
    iscalcmode = true;
    isshowfloatstack = true;
    if (mode == RUNMODE) oldmode = RUNMODE;
    else oldmode = CMDMODE;
    mode = INMODE;
    minbyte = '+'; maxbyte = ':'; linestep = 4; // Limits
    if (byteselect < minbyte || byteselect > maxbyte) byteselect = minbyte;
  }
}
static void Cload(void) {
  byte tmp = (int)floatstack[0]; // Number of constant
  if (tmp <= 9) {
    EEPROM.get(EECONSTANT + tmp * sizeof(double), floatstack[0]);
    isnewnumber = true;
  }
}
static void Csave(void) {
  byte tmp = (int)floatstack[0]; // Number of constant
  if (tmp <= 9) {
    pullfloatstack();
    EEPROM.put(EECONSTANT + tmp * sizeof(double), floatstack[0]);
    isnewnumber = true;
  }
}
static void disk(void) {
  oldoldmode = oldmode = CMDMODE;
  mode = DISKMODE;
  fsptr = EEDISKSTART;
}
static void diskformat(void) {
  EEPROM.write(EEDISKSTART, NULL);
  fsptr = EEDISKSTART;
}
static void Fgamma(void) {
  // 1 + ENTER ENTER INV EXP ENTER INV CHS + 2 / * SWAP 6 PWR 810 * INV + LN * 2 /
  floatstack[0] += 1;
  Fpush();
  Fpush();
  Finv();
  Fexp();
  Fpush();
  Finv();
  Fchs();
  Fadd();
  floatstack[0] /= 2;
  Fmult();
  Fswap();
  Fpush();
  floatstack[0] = 6;
  Fpow();
  floatstack[0] *= 810;
  Finv();
  Fadd();
  Fln();
  Fmult();
  floatstack[0] /= 2;
  // SWAP CHS + ROT ROT LN SWAP .5 - * + .9189385 + EXP
  Fswap();
  Fchs();
  Fadd();
  Frot();
  Frot();
  Fln();
  Fswap();
  floatstack[0] -= 0.5;
  Fmult();
  Fadd();
  floatstack[0] += 0.9189385;
  Fexp();
}
static void Fmult(void) {
  floatstack[0] *= floatstack[1];
  pullupperfloatstack();
}
static void Fadd(void) {
  floatstack[0] += floatstack[1];
  pullupperfloatstack();
}
static void Fsub(void) {
  floatstack[0] = floatstack[1] - floatstack[0];
  pullupperfloatstack();
}
static void Fdiv(void) {
  floatstack[0] = floatstack[1] / floatstack[0];
  pullupperfloatstack();
}
static void F2h(void) {
  floatstack[0] = (int)floatstack[0] + ((int)(100 * (floatstack[0] - (int)floatstack[0]))) / 60.0 + (100 * (100 * floatstack[0] - (int)(100 * floatstack[0]))) / 3600.0;
}
static void F2hms(void) {
  floatstack[0] = (int)floatstack[0] + ((int)(60 * (floatstack[0] - (int)floatstack[0]))) / 100.0 + (60 * ((60 * (floatstack[0] - (int)floatstack[0])) - (int)(60 * (floatstack[0] - (int)floatstack[0])))) / 10000.0;
}
static void F2p(void) { // R2P: ENTER ROT ROT SWAP ROT ENTER ROT SWAP / ATAN ROT ROT * ROT ROT * + SQRT
  Fpush();
  Frot();
  Frot();
  Fswap();
  Frot();
  Fpush();
  Frot();
  Fswap();
  Fdiv();
  Fatan();
  Frot();
  Frot();
  Fmult();
  Frot();
  Frot();
  Fmult();
  Fadd();
  Fsqrt();
}
static void F2r(void) { // P2R: SWAP SIN ENTER ENTER * CHS 1 + SQRT ROT * ROT * SWAP ROT
  Fswap();
  Fsin();
  Fpush();
  Fpush();
  Fmult(); Fchs();
  floatstack[0] += 1.0;
  Fsqrt();
  Frot();
  Fmult();
  Frot();
  Fmult();
  Fswap();
  Frot();
}
static void Flt(void) {
  if (prgptr < prg[prgnr][0] - 2 && floatstack[0] < floatstack[1]) {
    prgptr += 2;
    pullfloatstack;
    pullfloatstack;
  }
}
static void Fle(void) {
  if (prgptr < prg[prgnr][0] - 2 && floatstack[0] <= floatstack[1]) {
    prgptr += 2;
    pullfloatstack;
    pullfloatstack;
  }
}
static void Fne(void) {
  if (prgptr < prg[prgnr][0] - 2 && floatstack[0] != floatstack[1]) {
    prgptr += 2;
    pullfloatstack;
    pullfloatstack;
  }
}
static void Feq(void) {
  if (prgptr < prg[prgnr][0] - 2 && floatstack[0] == floatstack[1]) {
    prgptr += 2;
    pullfloatstack;
    pullfloatstack;
  }
}
static void Fgt(void) {
  if (prgptr < prg[prgnr][0] - 2 && floatstack[0] > floatstack[1]) {
    prgptr += 2;
    pullfloatstack;
    pullfloatstack;
  }
}
static void Fge(void) {
  if (prgptr < prg[prgnr][0] - 2 && floatstack[0] >= floatstack[1]) {
    prgptr += 2;
    pullfloatstack;
    pullfloatstack;
  }
}
static void Facos(void) { // ACOS: ASIN CHS 90 +
  Fasin();
  Fchs();
  floatstack[0] += 90;
}
static void Facosh(void) { // ACOSH: ENTER ENTER * 1 - SQRT + LN
  Fpush();
  Fpush();
  Fmult();
  floatstack[0] -= 1;
  Fsqrt();
  Fadd();
  Fln();
}
static void Fannu(void) { // ANNU: SWAP ENTER ENTER ROT ROT 1 + SWAP PWR INV CHS 1 + SWAP /
  Fswap();
  Fpush();
  Fpush();
  Frot();
  Frot();
  floatstack[0] += 1.0;
  Fswap();
  Fpow();
  Finv();
  Fchs();
  floatstack[0] += 1.0;
  Fswap();
  Fdiv();
}
static void Fasin(void) {
  floatstack[0] = atan(floatstack[0] / _sqrt(1 - floatstack[0] * floatstack[0])) * RAD;
}
static void Fasinh(void) { // ASINH: ENTER ENTER * 1 + SQRT + LN
  Fpush();
  Fpush();
  Fmult();
  floatstack[0] += 1;
  Fsqrt();
  Fadd();
  Fln();
}
static void Fatan(void) {
  floatstack[0] = atan(floatstack[0]) * RAD;
}
static void Fatanh(void) { // ATANH: ENTER ENTER 1 + SWAP CHS 1 + / SQRT LN
  Fpush();
  Fpush();
  floatstack[0] += 1;
  Fswap();
  Fchs();
  floatstack[0] += 1;
  Fdiv();
  Fsqrt();
  Fln();
}
static void Fbatt(void) {
  pushfloatstack();
  floatstack[0] = (double)(1126400L / rawadc()) / 1000;
}
static void Fce(void) {
  if (isdot) {
    if (floatstack[0] > TINYNUMBER && decimals > 0)
      floatstack[0] -= ((long)(floatstack[0] * _pow10(decimals)) % 10) / _pow10(decimals--);
    else isdot = false;
  }
  else floatstack[0] = (long)(floatstack[0] / 10.0);
}
static void Fchs(void) {
  floatstack[0] = -floatstack[0];
}
static void Fclx(void) {
  floatstack[0] = 0.0;
}
static void Fcos(void) { // COS: SIN ENTER * CHS 1 + SQRT
  Fsin();
  Fpush();
  Fmult();
  Fchs();
  floatstack[0] += 1;
  Fsqrt();
}
static void Fcosh(void) { // COSH: EXP ENTER INV + 2 /
  Fexp();
  Fpush();
  Finv();
  Fadd();
  floatstack[0] /= 2;
}
static void Fee(void) {
  if (floatstack[0] <= MAXEE) {
    floatstack[0] = floatstack[1] * _pow10(floatstack[0]);
    pullupperfloatstack();
  }
}
static void Fexp(void) {
  floatstack[0] = _exp_sin(floatstack[0], true);
  isnewnumber = true;
}
static void Fgaus(void) {
  // GAUSS-CDF: ENTER ENTER ENTER * * .07 * CHS SWAP 1.6 * CHS + EXP 1 + INV
  //            SWAP
  //       PDF: ENTER * CHS 2 / EXP 0.3989423 *
  Fpush();
  Fpush();
  Fpush();
  Fmult();
  Fmult();
  floatstack[0] *= 0.07;
  Fchs();
  Fswap();
  floatstack[0] *= 1.6;
  Fchs();
  Fadd();
  Fexp();
  floatstack[0] += 1.0;
  Finv();
  Fswap();
  Fpush();
  Fmult();
  Fchs();
  floatstack[0] /= 2.0;
  Fexp();
  floatstack[0] *= 0.3989423;
}
static void Fint(void) {
  if (mode == RUNMODE) oldmode = RUNMODE;
  else oldmode = CMDMODE;
  mode = INTMODE;
  isint = true;
  inta = floatstack[1];
  intb = floatstack[0];
  intdelta = (intb - inta) / 2 / INTSTRIPES;
  intf = 0;
  cycles = 0;
  runmsg();
}
static void Finv(void) {
  floatstack[0] = 1.0 / floatstack[0];
}
static void Fln(void) {
  floatstack[0] = log(floatstack[0]);
}
static void FLR(void) {
  pushfloatstack();
  //double tmp = (sxy / sn - sx * sy / sn / sn) / (sxx / sn - sx * sx / sn / sn);
  double tmp = (sxy - sx * sy / sn) / (sxx - sx * sx / sn);
  floatstack[1] = sy / sn - tmp * sx / sn + tmp * floatstack[0]; // ->y
  floatstack[0] = (floatstack[0] - sy / sn + tmp * sx / sn) / tmp; // ->x
}
static void Fpow(void) {
  floatstack[0] = _exp_sin(floatstack[0] * log(floatstack[1]), true);
  pullupperfloatstack();
}
static void Fpush(void) {
  pushfloatstack();
}
static void Frcl(void) {
  pushfloatstack();
  floatstack[0] = mem;
}
static void Frot(void) {
  double tmp = floatstack[0];
  pullfloatstack();
  floatstack[FLOATSTACKSIZE - 1] = tmp;
}
static void Fsave(void) {
  for (byte i = 0; i < FLOATSTACKSIZE; i++)
    EEPROM.put(EESTACK + i * sizeof(double), floatstack[i]);
  EEPROM.put(EESTACK + FLOATSTACKSIZE * sizeof(double), mem);
}
static void Fshow(void) {
  if (isshowfloatstack) {
    isshowfloatstack = false;
    clrline(0, 1);
  }
  else isshowfloatstack = true;
}
static void Fsin(void) {
  floatstack[0] = _exp_sin(floatstack[0] / RAD, false);
}
static void Fsinh(void) { // SINH: EXP ENTER INV CHS + 2 /
  Fexp();
  Fpush();
  Finv();
  Fchs();
  Fadd();
  floatstack[0] /= 2;
}
static void Fsolv(void) {
  if (mode == RUNMODE) oldmode = RUNMODE;
  else oldmode = CMDMODE;
  mode = SOLVEMODE;
  issolve = true;
  x0 = floatstack[0];
  cycles = 0;
  runmsg();
}
static void Fsqrt(void) {
  if (floatstack[0] > 0.0) floatstack[0] = _sqrt(floatstack[0]);
}
static void Fstat(void) {
  Fpush();
  floatstack[1] = _sqrt((sxx - sx * sx / sn) / (sn - 1)); // stdev
  floatstack[0] = sx / sn; // mean
}
static void Fsto(void) {
  mem = floatstack[0];
}
static void Fsum(void) {
  sx += floatstack[0];
  sy += floatstack[1];
  sxx += floatstack[0] * floatstack[0];
  sxy += floatstack[0] * floatstack[1];
  floatstack[0] = ++sn;
}
static void Fswap(void) {
  double tmp = floatstack[0];
  floatstack[0] = floatstack[1];
  floatstack[1] = tmp;
}
static void Ftan(void) { // TAN: SIN ENTER ENTER * CHS 1 + SQRT INV *
  Fsin();
  Fpush();
  Fpush();
  Fmult();
  Fchs();
  floatstack[0] += 1;
  Fsqrt();
  Finv();
  Fmult();
}
static void Ftanh(void) { // TANH: EXP ENTER ENTER INV CHS + SWAP ENTER INV + /
  Fexp();
  Fpush();
  Fpush();
  Finv();
  Fchs();
  Fadd();
  Fswap();
  Fpush();
  Finv();
  Fadd();
  Fdiv();
}
static void inputbyte(void) {
  if (mode == RUNMODE) oldmode = RUNMODE;
  else oldmode = CMDMODE;
  mode = INMODE;
  minbyte = 0; maxbyte = MAXBYTE; linestep = 16; // Limits
}
static void inputchar(void) {
  if (mode == RUNMODE) oldmode = RUNMODE;
  else oldmode = CMDMODE;
  mode = INMODE;
  minbyte = ' '; maxbyte = '~'; linestep = 10; // Limits
  if (byteselect < minbyte || byteselect > maxbyte) byteselect = minbyte;
}
static void inputnumber(void) {
  if (mode == RUNMODE) oldmode = RUNMODE;
  else oldmode = CMDMODE;
  mode = INMODE;
  minbyte = '0'; maxbyte = '9'; linestep = 3; // Limits
  if (byteselect < minbyte || byteselect > maxbyte) byteselect = minbyte;
}
static void keyboardmode(void) {
  minbyte = 0; maxbyte = MAXBYTE; linestep = 16; // Limits
  oldmode = KBDMODE;
  mode = INMODE;
}
static void poweroff(void) {
  sleepnow();
}
static void Pedit(void) {
  oldmode = oldoldmode = CMDMODE;
  mode = PRGEDITMODE;
  prgptr = PRGPTRSTART;
}
static void Pexp(void) {
  Keyboard.press(KEY_LEFT_ALT); Keyboard.press(KEY_F1); delayshort(200); Keyboard.releaseAll();
  Keyboard.print(F("minicom -b 9600 -D /dev/ttyACM0 -C prg.aos")); (KEY_RETURN);
  delayshort(200);
  for (byte i = 0; i < prg[prgnr][0]; i++) {
    Serial.write(prg[prgnr][i]);
  }
  delayshort(200);
  Keyboard.press(KEY_LEFT_CTRL); Keyboard.press('a'); delayshort(200); Keyboard.releaseAll();
  Keyboard.write('q'); Keyboard.write(KEY_RETURN);
  delayshort(50);
  Keyboard.print(F("exit")); Keyboard.write(KEY_RETURN);
  oldmode = CMDMODE;
  mode = MSGMODE;
  msg = MSGOK;
}
static void Pgoto(void) {
  // Do nothing here - manage in section "RUN PROGRAM"
}
static void Pimp(void) {
  Keyboard.press(KEY_LEFT_ALT); Keyboard.press(KEY_F1); delayshort(200); Keyboard.releaseAll();
  Keyboard.print(F("echo -ne '\\x'`printf '%x' $(wc -c < prg.aos)` | dd conv=notrunc bs=1 count=1 of=prg.aos")); Keyboard.write(KEY_RETURN);
  Keyboard.print(F("cat prg.aos > /dev/ttyACM0")); Keyboard.write(KEY_RETURN);
  while (Serial.available() <= 0) ;
  prg[prgnr][0] = Serial.read();
  for (byte i = 1; i < prg[prgnr][0]; i++) prg[prgnr][i] = Serial.read();
  Keyboard.print(F("exit")); Keyboard.write(KEY_RETURN);
  oldmode = CMDMODE;
  mode = MSGMODE;
  msg = MSGOK;
}
static void Pname(void) {
  for (byte i = 1; i < PRGPTRSTART; i++) {
    byte tmp = bytestack[PRGPTRSTART - 1 - i];
    if (tmp >= ' ' && tmp <= '~') prg[prgnr][i] = tmp;
  }
}
static void Pnop(void) {
}
static void PpB(void) {
  // Do nothing here - manage in section "RUN PROGRAM"
}
static void Ppse(void) {
  delay(PAUSE);
}
static void Prun(void) {
  prgptr = PRGPTRSTART;
  oldmode = CMDMODE;
  mode = RUNMODE;
  isshift = false;
  runmsg();
}
static void Pstop(void) {
  oldmode = CMDMODE;
  mode = MSGMODE;
  msg = MSGSTOP;
}
static void Psub(void) {
  // Do nothing here - manage in section "RUN PROGRAM"
}
static void Pswap(void) {
  prgnr = 1 - prgnr; // Swap 0 and 1
  prgptr = PRGPTRSTART;
}
static void Pyesno(void) {
  if (mode == RUNMODE) oldmode = RUNMODE;
  else oldmode = CMDMODE;
  mode = YESNOMODE;
}
static void Sfn(void) {
  for (byte i = 0; i < NUMBERFKEYS; i++) {
    EEPROM.write(EEFKEYS + i, bytestack[0]);
    f[i] = bytestack[0];
    pullbytestack();
  }
}
static void setlit(void) {
  screencontrast = bytestack[0];
  pullbytestack();
  setscreencontrast(screencontrast);
  EEPROM.write(EECONTRAST, screencontrast);
}
static void setscreenoff(void) {
  if (bytestack[0] >= MINSCREENOFFTIME) {
    screenofftime = bytestack[0];
    pullbytestack();
    EEPROM.write(EESCREENOFF, screenofftime);
  }
}
static void term(void) {
  if (isterm) {
    isterm = false;
    ledsoff();
  }
  else isterm = true;
}
static void torch(void) {
  if (istorch) { // Torch off
    istorch = false;
    ledsoff();
  }
  else { // Torch on
    istorch = true;
    digitalWrite(RED_LED, LOW);
    digitalWrite(GREEN_LED, LOW);
    digitalWrite(BLUE_LED, LOW);
  }
}
static void text(void) {
  iscursor = false; // If there is no cursor - append one
  for (byte i = 0; i < prg[prgnr][0]; i++) if (prg[prgnr][i] == CURSORSIGN) iscursor = true;
  if (!iscursor && strlen(prg[prgnr]) < MAXBYTE) prg[prgnr][prg[prgnr][0]++] = CURSORSIGN;
  oldoldmode = CMDMODE;
  mode = TXTEDITMODE;
}
static void WATCH(void) {
  printclock();
  mode = CLKMODE;
}
static void Wset(void) {
  if (bytestack[1] < 24) clockmillis = bytestack[1] * 3600000;
  //if (bytestack[1] < 12) clockmillis = bytestack[1] * 3600000;
  if (bytestack[0] < 59) clockmillis += bytestack[0] * 60000;
  pullbytestack();
  pullbytestack();
  starttime = millis();
  mode = CLKMODE;
}
static void (*dispatch[])(void) = { // Function pointer array
  &about,
  &B2F,
  &BflushF,
  &Bclr,
  &Bprint,
  &Bshow,
  &calcmode,
  &Cload,
  &Csave,
  &disk,
  &diskformat,
  &Fgamma,
  &Fmult,
  &Fadd,
  &Fsub,
  &Fdiv,
  &F2h,
  &F2hms,
  &F2p,
  &F2r,
  &Flt,
  &Fle,
  &Fne,
  &Feq,
  &Fgt,
  &Fge,
  &Facos,
  &Facosh,
  &Fannu,
  &Fasin,
  &Fasinh,
  &Fatan,
  &Fatanh,
  &Fbatt,
  &Fce,
  &Fchs,
  &Fclx,
  &Fcos,
  &Fcosh,
  &Fee,
  &Fexp,
  &Fgaus,
  &Fint,
  &Finv,
  &Fln,
  &FLR,
  &Fpow,
  &Fpush,
  &Frcl,
  &Frot,
  &Fsave,
  &Fshow,
  &Fsin,
  &Fsinh,
  &Fsolv,
  &Fsqrt,
  &Fstat,
  &Fsto,
  &Fsum,
  &Fswap,
  &Ftan,
  &Ftanh,
  &inputbyte,
  &inputchar,
  &inputnumber,
  &keyboardmode,
  &poweroff,
  &Pedit,
  &Pexp,
  &Pgoto,
  &Pimp,
  &Pname,
  &Pnop,
  &PpB,
  &Ppse,
  &Prun,
  &Pstop,
  &Psub,
  &Pswap,
  &Pyesno,
  &Sfn,
  &setlit,
  &setscreenoff,
  &term,
  &torch,
  &text,
  &WATCH,
  &Wset,
};


// SUBROUTINES
static void runmsg(void) { // Prints running message
  clrline(0, 3);
  strcpy_P(strbuf, (char*)pgm_read_word(&(msg_table[MSGRUN])));
  printstring(0, 0, strbuf);
}

static void fillstrbuf(byte n) { // Fills strbuf with n blanks
  byte tmp = _min(n, MAXSTRBUF - 1);
  for (byte i = 0; i < tmp; i++) strbuf[i] = ' ';
  strbuf[tmp] = NULL;
}

static void pushbytestack(void) { // Push bytestack
  memmove(&bytestack[1], &bytestack[0], (BYTESTACKSIZE - 1) * sizeof(byte));
}

static void pullbytestack(void) { // Pull bytestack
  memmove(&bytestack[0], &bytestack[1], (BYTESTACKSIZE - 1) * sizeof(byte));
}

static void pushfloatstack(void) { // Push floatstack
  memmove(&floatstack[1], &floatstack[0], (FLOATSTACKSIZE - 1) * sizeof(double));
}

static void pullfloatstack(void) { // Pull floatstack
  memcpy(&floatstack[0], &floatstack[1], (FLOATSTACKSIZE - 1) * sizeof(double));
  //memmove(&floatstack[0], &floatstack[1], (FLOATSTACKSIZE - 1) * sizeof(double));
  //for (byte i = 0; i < FLOATSTACKSIZE - 2; i++) floatstack[i] = floatstack[i + 1];
}

static void pullupperfloatstack(void) { // Pull upper part of floatstack
  memmove(&floatstack[1], &floatstack[2], 2 * sizeof(double));
}

static int eofs(void) { // Returns EEPROM-address of end of file system
  int addr = EEDISKSTART;
  while (EEPROM.read(addr) != NULL) addr += EEPROM.read(addr);
  return (addr);
}

static double _pow10(int8_t e) { // Returns 10^e
  boolean ne = (e < 0);
  double f = 1.0;
  if (ne) while (e++)  f *= 10;
  else while (e--)  f *= 10;
  if (ne) f = 1 / f;
  return (f);
}

static double _exp_sin(double f, boolean isexp) { // Calculate exp or sin with Taylor series
  double result = f;
  double frac = f;
  if (isexp) result = frac = 1.0;
  for (byte i = 1; _abs(frac) > TINYNUMBER && i < MAXITERATE; i++) {
    if (isexp) frac *= f / i; // Fraction for exp
    else frac *=  f  / (-2 * i * (2 * i + 1)) * f; // Fraction for sin
    result += frac;
  }
  return (result);
}

static double _sqrt(double f) { // Calculating square root without using sqrt()
  return (_exp_sin(0.5 * log(f), true));
}

static void calculator(byte k) { // Interpret byte for calculator
  if (k == '/') { // Menu (one command)
    iscalcmenu = true;
    mode = CMDMODE;
    isnewnumber = true;
  }
  else if (k == '-') { // CHS
    Fchs();
    isnewnumber = true;
  }
  else if (k == ',') { // EE
    Fee();
    isnewnumber = true;
  }
  else if (k >= '0' && k <= '9') {
    if (isnewnumber) { // New number
      if (ispushed) ispushed = false;
      else pushfloatstack();
      floatstack[0] = 0.0;
      decimals = 0;
      isdot = false;
      isnewnumber = false;
    }
    if (isdot) floatstack[0] += (k - '0') / _pow10(++decimals); // Append decimal to number
    else floatstack[0] = floatstack[0] * 10 + k - '0'; // Append digit to number
  }
  else if (k == '.') { // Dot
    if (isnewnumber) { // Enable starting new number with .
      if (ispushed) ispushed = false;
      else pushfloatstack();
      floatstack[0] = 0.0;
      decimals = 0;
      isnewnumber = false;
    }
    isdot = true;
  }
  else if (k == '+') { // CLX or CE
    if (isnewnumber) floatstack[0] = 0.0; // CLX
    else { // CE
      if (isdot) {
        if (floatstack[0] > TINYNUMBER && decimals > 0)
          floatstack[0] -= ((long)(floatstack[0] * _pow10(decimals)) % 10) / _pow10(decimals--);
        else isdot = false;
      }
      else floatstack[0] = (long)(floatstack[0] / 10.0);
    }
  }
  else if (k = ':') { // Enter
    pushfloatstack();
    ispushed = true;
    isnewnumber = true;
  }
}

static int prevfileptr(int ptr) { // Returns a pointer to the previous file
  int tmpptr = EEDISKSTART;
  while (tmpptr + EEPROM.read(tmpptr) < ptr) tmpptr += EEPROM.read(tmpptr);
  return (tmpptr);
}

static void repairgoto(boolean insert) { // Adapt goto address if step was inserted/deleted
  for (byte i = PRGPTRSTART; i < prg[prgnr][0] - 1; i++) { // Repair gotos
    char tmpstr[] = ".....";
    if (prg[prgnr][i] >= CMDOFFSET && prg[prgnr][i] <= numberofcommands + CMDOFFSET)
      strcpy_P(tmpstr, (char*)pgm_read_word(&(cmd[prg[prgnr][i] - CMDOFFSET])));
    if (strcmp(tmpstr, PGOTO) == 0 && prg[prgnr][i + 1] > prgptr - PRGPTRSTART)
      if (insert) prg[prgnr][i + 1]++; // Byte inserted
      else prg[prgnr][i + 1]--; // Byte deleted
  }
}

static void printmsg(byte nr) { // Print message
  strcpy_P(strbuf, (char*)pgm_read_word(&(msg_table[nr]))); // Copy string to buffer
  clrline(0, 1);
  printstring2(0, 0, strbuf);
}

void printclock() { // Prints digital time
  fillstrbuf(5);
  strbuf[2] = ':';
  unsigned long tmp = clockmillis / 36000000;
  if (tmp) strbuf[0] = tmp + '0';
  strbuf[1] = clockmillis / 3600000 % 10 + '0';
  tmp = clockmillis / 60000;
  strbuf[3] = (tmp - tmp / 60 * 60) / 10 + '0';
  strbuf[4] = (tmp - tmp / 60 * 60) % 10 + '0';
  clrline(0, 3);
  //printstring2(3, 1, strbuf); // Print time (small)
  for (byte i = 0; i < 5; i++) print4(13 * i + 1, 0, strbuf[i], true); // Print time (big)
}

static void printdot(int x, int y) { // Print dot to sbuf[]
  x = _min(x, PRINTMAXX); y = _min(y, PRINTMAXY); // Restrict x and y
  for (byte i = 0; i < 2; i++) sbuf[x + y * SCREENWIDTH + i] = 0x30;
}

static byte specialcmd(byte p) { // Returns 1 if prg[p]=PSUB (2 if GOTO or 3 if PPB) (else 0)
  char tmpstr[] = ".....";
  if (p >= PRGPTRSTART && p <= prg[prgnr][0] &&
      prg[prgnr][p] >= CMDOFFSET && prg[prgnr][p] < CMDOFFSET + numberofcommands)
    strcpy_P(tmpstr, (char*)pgm_read_word(&(cmd[prg[prgnr][p] - CMDOFFSET])));
  if (strcmp(tmpstr, PSUB) == 0) return (1);
  if (strcmp(tmpstr, PGOTO) == 0) return (2);
  if (strcmp(tmpstr, PPB) == 0) return (3);
  return (NULL);
}

static void printstep(byte line, byte ptr) { // Print program step
  fillstrbuf(4);
  strbuf[3] = '-';
  byte tmp = ptr - PRGPTRSTART + 1;
  strbuf[0] = _huns(tmp) + '0';
  strbuf[1] = _tens(tmp) + '0';
  strbuf[2] = _ones(tmp) + '0';

  byte tmpstr[] = "...  ";
  //char tmptmpstr[] = ".....";

  /*if (ptr > PRGPTRSTART) { // Identify command of previous step
    if (prg[prgnr][ptr - 1] >= CMDOFFSET && prg[prgnr][ptr - 1] < CMDOFFSET + numberofcommands)
      strcpy_P(tmptmpstr, (char*)pgm_read_word(&(cmd[prg[prgnr][ptr - 1] - CMDOFFSET])));
    }*/
  //if (strcmp(tmptmpstr, PPB) == 0 || strcmp(tmptmpstr, PGOTO) == 0) { // Print byte
  byte sccount = 0; // Count number of previous special commands (ie PPB, PGOTO)
  for (byte i = ptr; i > PRGPTRSTART && specialcmd(i - 1) > 1; i--) sccount++;
  //Serial.print(" ptr="); Serial.print(ptr);
  if (sccount & 1) { // Print byte and char if sccount is odd
    //  if (specialcmd(ptr - 2) < 2 && specialcmd(ptr - 1) > 1) { // Print byte if PGOTO or PPB
    byte tmp = prg[prgnr][ptr];
    tmpstr[0] = _huns(tmp) + '0';
    tmpstr[1] = _tens(tmp) + '0';
    tmpstr[2] = _ones(tmp) + '0';
    //if (tmp >= ' ' && tmp <= '~' && strcmp(tmptmpstr, PGOTO) != 0) // Print ascii sign too
    //if (tmp >= ' ' && tmp <= '~' && specialcmd(ptr-1)==1) // Print ascii sign too
    if (tmp >= ' ' && tmp <= '~') // Print ascii sign too
      tmpstr[4] = tmp;
  }
  else { // Print command
    if (prg[prgnr][ptr] >= CMDOFFSET && prg[prgnr][ptr] < CMDOFFSET + numberofcommands)
      strcpy_P(tmpstr, (char*)pgm_read_word(&(cmd[prg[prgnr][ptr] - CMDOFFSET])));
  }
  strcat(strbuf, tmpstr);
  clrline(line, line);
  printstring(0, line, strbuf);
}

static void printfree(byte line) { // Print disk free
  int ttmpptr = EEDISKSTART;
  fillstrbuf(10);
  strbuf[0] = 'F';
  strbuf[1] = 'R';
  strbuf[2] = 'E';
  strbuf[3] = 'E';
  strbuf[4] = ':';
  while (EEPROM.read(ttmpptr) != NULL) ttmpptr += EEPROM.read(ttmpptr);
  int tmp = EEDISKEND - ttmpptr;
  strbuf[6] = _tsds(tmp) + '0';
  strbuf[7] = _huns(tmp) + '0';
  strbuf[8] = _tens(tmp) + '0';
  strbuf[9] = _ones(tmp) + '0';
  printstring(0, line, strbuf);
}

static void printeod(byte line) { // Print EOD
  fillstrbuf(3);
  strbuf[0] = 'E';
  strbuf[1] = 'O';
  strbuf[2] = 'D';
  printstring(0, line, strbuf);
}

static void printfile(int addr, byte line) { // Print EEPROM-entry
  fillstrbuf(7);
  for (byte i = 0; i < PRGPTRSTART - 1; i++) strbuf[i] = EEPROM.read(addr + i + 1);
  strbuf[3] = '-';
  byte t = EEPROM.read(addr);
  strbuf[4] = _huns(t) + '0';
  strbuf[5] = _tens(t) + '0';
  strbuf[6] = _ones(t) + '0';
  printstring(0, line, strbuf);
}

static void printshift(byte c) { // Prints shift sign or other valid character
  if (isshift) print(59, 4, '`');
  else if (c >= ' ' && c <= '~') print(59, 4, c);
}

static byte calcdivert(byte c) { // Divert character in CALC mode
  if (iscalcmode) {
    if (c == '+') return ('C'); // CLX
    if (c == '/') return ('`'); // Menu
    if (c == ':') return ('~'); // Enter
    if (c == ',') return ('E'); // EE
  }
  return (c);
}

static void printscreen(void) { // Print screen due to state
  if (isshowbytestack) { // Show bytestack (XYZ)
    clrline(1, 1);
    for (byte i = 0; i < 3; i++) {
      fillstrbuf(3);
      strbuf[0] = _huns(bytestack[i]) + '0';
      strbuf[1] = _tens(bytestack[i]) + '0';
      strbuf[2] = _ones(bytestack[i]) + '0';
      printstring(i * 23, 1, strbuf);
    }
  }

  if (isshowfloatstack) { // Show floatstack (X)
    double f = floatstack[0];
    long m; // Mantissa
    int8_t e; // Exponent
    fillstrbuf(10);
    if (f < 0.0) {
      f = - f;
      strbuf[0] = '-';
    }
    e = log10(f); // Exponent
    m = (f / _pow10(e - 5)) + .5; // Mantissa
    if (m > 0 && m < _pow10(5)) { // Change (n-1)-digit-mantissa to n digits
      e -= 1;
      m = (f / _pow10(e - 5)) + .5; // "New" mantissa
    }
    for (byte i = 6; i > 0; i--) { // Print mantissa
      strbuf[i] = _ones(m) + '0';
      m /= 10;
    }
    if (e < 0) { // Negative exponent
      strbuf[7] = '-';
      e = -e;
    }
    if (e < 10) { // Print exponent
      strbuf[8] = '0';
      strbuf[9] = e + '0';
    }
    else {
      strbuf[8] = _tens(e) + '0';
      strbuf[9] = _ones(e) + '0';
    }
    clrline(0, 1); // Print strbuf in float format
    print2(0, 0, strbuf[0], true);
    print2(6 , 0, strbuf[1], true);
    //for (byte i = 2; i < 10; i++) print2(6 * i + 3 + (i > 6 ? 1 : 0), 0, strbuf[i], true);
    for (byte i = 2; i < 7; i++) print2(6 * i + 3 , 0, strbuf[i], true);
    for (byte i = 7; i < 10; i++) print(6 * i + 4 , 0, strbuf[i]);
    printdot(12, 1);
  }

  if (mode == CMDMODE) {
    strcpy_P(strbuf, (char*)pgm_read_word(&(cmd[cmdselect])));
    clrline(2, 3);
    printstring2(0, 2, strbuf);
    printshift(' ');
  }

  else if (mode == INMODE) { // Select byte
    clrline(2, 3);
    byte b = byteselect;
    fillstrbuf(3);
    strbuf[0] = _huns(b) + '0';
    strbuf[1] = _tens(b) + '0';
    strbuf[2] = _ones(b) + '0';
    printstring(0, 2, strbuf);
    if (b >= CMDOFFSET && b < CMDOFFSET + numberofcommands) { // cmd
      fillstrbuf(5);
      strcpy_P(strbuf, (char*)pgm_read_word(&(cmd[b - CMDOFFSET])));
      printstring(SCREENWIDTH - (strlen(strbuf) * 6 - 1), 2, strbuf); // Align right
    }
    if (b >= ' ' && b <= '~') { // Char
      print2(21, 2, calcdivert(b), false);
    }
    for (byte i = _max(0, b - 3); i < b; i++) { // Previous chars
      if (i >= minbyte && i <= maxbyte) print(0 + (i - b + 3) * 6, 3, calcdivert(i));
    }
    for (byte i = _min(MAXBYTE, b + 1); i < _min(MAXBYTE, b + 6); i++) { // Next chars
      if (i >= minbyte && i <= maxbyte) print(35 + (i - b - 1) * 6, 3, calcdivert(i));
    }
    if (oldmode == KBDMODE) printshift(NULL);
  }

  else if (mode == PRGEDITMODE) {
    for (byte i = 0; i < MAXLIN; i++) {
      if (prgptr - 3 + i < PRGPTRSTART) { // BOF
        fillstrbuf(8);
        strbuf[3] = ':';
        strbuf[0] = prg[prgnr][1];
        strbuf[1] = prg[prgnr][2];
        strbuf[2] = prg[prgnr][3];
        strbuf[5] = _huns(prg[prgnr][0]) + '0';
        strbuf[6] = _tens(prg[prgnr][0]) + '0';
        strbuf[7] = _ones(prg[prgnr][0]) + '0';
        clrline(0, i); // Clear from top
        printstring(0, i, strbuf);
      }
      else {
        if (prgptr >= prg[prgnr][0] && i == 3) { // EOF
          strcpy(strbuf, "EOF");
          clrline(i, i);
          printstring(0, i, strbuf);
        }
        else printstep(i, prgptr - 3 + i); // Regular program step
      }
    }
    printshift('{');
  }

  else if (mode == DISKMODE) {
    clrline(0, MAXLIN - 1);
    boolean freeprinted = false;
    for (byte i = MAXLIN; i > 0; i--) {
      int tmpptr = fsptr;
      boolean notlowered = 0;
      for (byte j = 0; j < 4 - i; j++) { // Find n-th prev file
        int tmp = tmpptr;
        tmpptr = prevfileptr(tmpptr);
        if (tmpptr == tmp) notlowered = true;
      }
      if (EEPROM.read(tmpptr) == NULL) {
        if (i == MAXLIN) printeod(i - 1); // Print EOD
        else if (!freeprinted) { // Print free for empty disk
          printfree(i - 1);
          freeprinted = true;
        }
      }
      else if (notlowered) {
        if (!freeprinted) { // Print free for not empty disk
          printfree(i - 1);
          freeprinted = true;
        }
        else clrline(i - 1, i - 1);
      }
      else printfile(tmpptr, i - 1); // Regular file
    }
    printshift('{');
  }

  else if (mode == TXTEDITMODE) {
    byte linestart = PRGPTRSTART;
    byte lineend = prg[prgnr][0];
    byte tmpprgptr = PRGPTRSTART;
    boolean iseof = false;
    clrline(0, MAXLIN - 1);
    for (byte i = PRGPTRSTART; i < prg[prgnr][0]; i++) // Find cursor
      if (prg[prgnr][i] == CURSORSIGN) tmpprgptr = i;

    for (byte i = 0; i < MAXLIN; i++) {
      for (byte i = PRGPTRSTART; i < tmpprgptr; i++) // Find previous line feed
        if (prg[prgnr][i] == LFSIGN) linestart = i + 1;
      lineend = prg[prgnr][0];
      for (byte i = prg[prgnr][0] - 1; i > tmpprgptr; i--) // Find next line feed
        if (prg[prgnr][i] == LFSIGN) lineend = i;

      fillstrbuf(MAXCHAR);
      byte tmp = _min(MAXCHAR, lineend - linestart);
      for (byte i = 0; i < tmp; i++) // Copy line to strbuf
        strbuf[i] = prg[prgnr][_max(tmpprgptr - MAXCHAR + 1, linestart) + i];
      if (!iseof) printstring(0, i, strbuf); // Print line
      if (lineend == prg[prgnr][0]) iseof = true; // Prevent printing last line multiple
      if (lineend < prg[prgnr][0] - 1) tmpprgptr = lineend + 1; // Next begin of line
    }
    printshift(' ');
  }

  else if (mode == YESNOMODE) {
    clrline(1, MAXLIN - 1);
    strcpy_P(strbuf, (char*)pgm_read_word(&(msg_table[MSGYESNO])));
    printstring(0, 1, strbuf);
  }

  else if (mode == CLKMODE) printclock();

  else if (mode == MSGMODE) printmsg(msg);

}

static void reset(void) {
  mode = oldmode = CMDMODE;
  strbuf[0] = '/0';
  sbufclr();
  Bclr();
  memset(floatstack, 0, FLOATSTACKSIZE * sizeof(double));
  cmdselect = byteselect = prgnr = sn = decimals = 0;
  strcpy(prg[0], ".ABC");
  strcpy(prg[1], ".abc");
  prg[0][0] = prg[1][0] = prgptr = PRGPTRSTART;
  isinsertbyte = isshowbytestack = isshowfloatstack = iscalcmode = iscalcmenu = isterm = istorch = isdot = issolve = ispushed = false;
  isnewnumber = true;
  mem = sx = sxx = sy = sxy = 0.0;
  fsptr = EEDISKSTART;
  ledsoff();
  powerofftimestamp = screenofftimestamp = millis();
}


// SETUP & LOOP
void setup() {
  bootpins(); // System boot procedure
  bootSPI();
  bootscreen();
  bootpowersaving();
  setframerate(FRAMERATE);
  flashlightmode(); // Secure waiting for flahsing new software (when UP+on)
  setscreencontrast(EEPROM.read(EECONTRAST));
  screenofftime = EEPROM.read(EESCREENOFF);
  if (screenofftime < MINSCREENOFFTIME) screenofftime = MINSCREENOFFTIME;
  reset();

  pruncmdnumber = 0; // Evaluate number of command Prun
  for (byte i = 0; i < numberofcommands; i++) {
    char tmpstr[] = ".....";
    strcpy_P(tmpstr, (char*)pgm_read_word(&(cmd[i])));
    if (strcmp(tmpstr, PRUN) == 0) pruncmdnumber = i;
  }

  digitalWrite(GREEN_LED, LOW);
  for (byte i = 0; i < NUMBERFKEYS; i++) f[i] = EEPROM.read(EEFKEYS + i);
  printmsg(0);
  //printstring4(0,0,"AOS");
  display();
  for (byte i = 0; i < FLOATSTACKSIZE; i++)
    EEPROM.get(EESTACK + i * sizeof(double), floatstack[i]);
  EEPROM.get(EESTACK + FLOATSTACKSIZE * sizeof(double), mem);
  sbufclr();
  delay(800); // Needed to stop serial communication to switch LEDs off
  ledsoff();
}

void loop() {
  if (!(nextFrame())) return; // Pause render (idle) until it's time for the next frame

  if (!digitalRead(PIN_B_BUTTON) && !digitalRead(PIN_A_BUTTON)) reset(); // Reset
  if (!digitalRead(PIN_B_BUTTON) && !digitalRead(PIN_LEFT_BUTTON)) Pstop(); // Stop program execution

  if (millis() - starttime >= PRINTINTERVAL * 1000L) { // Show clock and correct time
    clockmillis = clockmillis + millis() - starttime + TIMECORRECTION;
    starttime = millis();
    if (mode == CLKMODE) printclock(); // Show clock
  }
  if (clockmillis >= 86400000) clockmillis = DAILYTIMECORRECTION; // Reset time after 24 hours
  //if (clockmillis >= 43200000) clockmillis = DAILYTIMECORRECTION; // Reset time after 12 hours

  key = getkey(); // Query key

  if (isterm && Serial.available()) { // Read and convert key from terminal
    char from[] = "eqdsaw"; // AB->qe, Cursor: dsaw
    char to[] =   "BARDLU";
    byte tmp = strlen(from);
    byte k = Serial.read();
    for (byte i = 0; i < tmp; i++) if (k == from[i]) key = to[i];
  }

  if (key == 'A') isshift = isshift ? false : true; // Manage shift key
  else if (isshift && key) {
    key = key - 'A' + 'a';
    isshift = false;
  }

  if (mode == RUNMODE) { // *** RUN PROGRAM ***
    //Serial.print("len="); Serial.print(prg[prgnr][0]);
    //Serial.print(" ptr="); Serial.print(prgptr);
    //Serial.print(" step="); Serial.println(prgptr - PRGPTRSTART + 1);
    byte tmp = specialcmd(prgptr);
    if (tmp == 1) { // Manage GOSUB
      issubprg = true;
      oldprgptr = prgptr;
      prgptr = PRGPTRSTART;
      prgnr = 1 - prgnr;
    }
    else if (tmp == 2) { // Manage GOTO
      if (prg[prgnr][prgptr + 1] + PRGPTRSTART - 1 < prg[prgnr][0])
        prgptr = prg[prgnr][prgptr + 1] + PRGPTRSTART - 1;
    }
    else if (tmp == 3) { // Manage PPB
      pushbytestack();
      bytestack[0] = prg[prgnr][prgptr + 1];
      prgptr += 2;
    }
    else { // Normal command
      if (prg[prgnr][prgptr] >= CMDOFFSET && prg[prgnr][prgptr] < CMDOFFSET + numberofcommands)
        (*dispatch[prg[prgnr][prgptr] - CMDOFFSET])(); // Run selected command from function table
      prgptr++;
    }
    if (prgptr >= prg[prgnr][0]) { // End of prg reached - return to root prg or exit
      if (issubprg) { // Return to root prg
        prgnr = 1 - prgnr;
        issubprg = false;
        if (oldprgptr < prg[prgnr][0] - 1) prgptr = oldprgptr + 1;
        else { // Root prg was at end - exit
          oldmode = CMDMODE;
          mode = MSGMODE;
          msg = MSGOK;
        }
      }
      else { // Exit prg execution
        if (issolve) mode = SOLVEMODE;
        else if (isint) mode = INTMODE;
        else {
          if (iscalcmenu) oldmode = INMODE; // Return to INMODE (CALC)
          else oldmode = CMDMODE;
          iscalcrun = false;
          iscalcmenu = false;
          oldoldmode = CMDMODE;
          mode = MSGMODE;
          msg = MSGOK;
        }
      }
    }
  }
  else if (mode == SOLVEMODE) { // *** SOLVE MODE
    cycles++;
    if (cycles == 1) { // First run - f(x0)
      prgptr = PRGPTRSTART;
      mode = RUNMODE;
    }
    else if (cycles == 2) { // Second run - f(x0+dx)
      f0 = floatstack[0];
      floatstack[0] = x0 + DELTAX;
      prgptr = PRGPTRSTART;
      mode = RUNMODE;
    }
    else { // Third run - x1
      f1 = floatstack[0];
      double tmp = x0;
      x0 = x0 - (DELTAX / (f1 / f0 - 1));
      floatstack[0] = x0;
      cycles = 0;
      if (abs(x0 - tmp) < DELTAX) { // Exit
        issolve = false;
        isnewnumber = true;
        oldmode = CMDMODE;
        mode = MSGMODE;
        msg = MSGOK;
      }
    }
  }
  else if (mode == INTMODE) { // *** INT MODE
    cycles++;
    if (cycles == 1) { // First run - f(xb)
      prgptr = PRGPTRSTART;
      mode = RUNMODE;
    }
    else if (cycles == 2) { // Second run - f(xa)
      intf += floatstack[0]; // F=F+f(b)
      floatstack[0] = inta;
      prgptr = PRGPTRSTART;
      mode = RUNMODE;
    }
    else if (cycles == 3) { // Third run - xa
      intf += floatstack[0]; // F=F+f(a)
      floatstack[0] = intx = inta + intdelta;
      prgptr = PRGPTRSTART;
      mode = RUNMODE;
    }
    else if (cycles <= 2 * INTSTRIPES - 1 + 3) { // 2n-1 stripes
      intf += 4 * floatstack[0] * ((cycles - 3) / 2.0 - (int)((cycles - 3) / 2.0) + 0.5);
      floatstack[0] = intx = intx + intdelta;
      prgptr = PRGPTRSTART;
      mode = RUNMODE;
    }
    else { // Last run
      floatstack[0] = intf * intdelta / 3;
      isint = false;
      isnewnumber = true;
      oldmode = CMDMODE;
      mode = MSGMODE;
      msg = MSGOK;
    }
  }

  if (key) { // Interpret key

    powerofftimestamp = screenofftimestamp = millis(); // Deactivate poweroff and screensaver
    if (isscreensave) {
      isscreensave = false;
      screenon();
    }

    if (mode == CMDMODE) { // *** SELECT COMMAND ***
      if (key == KEY_B) {
        if (cmdselect == pruncmdnumber) iscalcrun = true; // Check if CALC called Prun
        (*dispatch[cmdselect])(); // Run selected command from function table
        isshift = false;
        if (iscalcmenu && !iscalcrun) { // Go back to calculator
          iscalcmenu = false;
          mode = INMODE;
        }
      }
      else if (key == KEY_A) {
        if (iscalcmenu) { // Go back to calculator
          iscalcmenu = false;
          isshift = false;
          mode = INMODE;
        }
        else mode = oldmode;
      }
      else if (key == KEY_R)
        if (cmdselect < numberofcommands - 1) cmdselect++;
        else cmdselect = 0;
      else if (key == KEY_D)
        if (cmdselect < numberofcommands - CMDLINESTEP) cmdselect += CMDLINESTEP;
        else cmdselect = numberofcommands - 1;
      else if (key == KEY_L)
        if (cmdselect > 0) cmdselect--;
        else cmdselect = numberofcommands - 1;
      else if (key == KEY_U)
        if (cmdselect > CMDLINESTEP) cmdselect -= CMDLINESTEP;
        else cmdselect = 0;
      else if (key == 'b') poweroff();
      else if (key >= 'd') { // Function keys for cmd (rdlu-keys)
        byte tmp = 0;
        if (key == 'r') tmp = f[3];
        if (key == 'd') tmp = f[2];
        if (key == 'l') tmp = f[1];
        if (key == 'u') tmp = f[0];
        if (tmp >= CMDOFFSET && tmp < CMDOFFSET + numberofcommands)
          (*dispatch[tmp - CMDOFFSET])();
      }
    }

    else if (mode == INMODE) { // *** SELECT BYTE ***
      if (key == KEY_B) {
        if (oldmode == KBDMODE) { // Send selected byte as HID to USB
          Keyboard.write(byteselect);
          delayshort(50);
          Keyboard.releaseAll();
        }
        else if (oldmode == PRGEDITMODE) { // Edit/insert program step
          if (isinsertbyte) { // Insert step
            prg[prgnr][0]++;
            if (prgptr < prg[prgnr][0]) // Insert
              for (byte i = prg[prgnr][0]; i > prgptr; i--) prg[prgnr][i] = prg[prgnr][i - 1];
            prg[prgnr][prgptr] = byteselect;
            repairgoto(true);
            mode = oldmode;
          }
          else { // Edit step
            prg[prgnr][prgptr] = byteselect;
            mode = oldmode;
          }
        }
        else if (oldmode == TXTEDITMODE) { // Insert char
          if (prg[prgnr][0] < MAXBYTE) {
            for (byte i = prg[prgnr][0] + 1; i > prgptr; i--) prg[prgnr][i] = prg[prgnr][i - 1];
            prg[prgnr][prgptr] = byteselect;
            prg[prgnr][0]++;
          }
          clrline(2, 3);
          mode = oldmode;
          isshift = false;
        }
        else if (iscalcmode) { // Interpret selected byte for calculator
          calculator(byteselect);
        }
        else {
          pushbytestack();
          bytestack[0] = byteselect;
          //mode = oldmode; // Comment if you want to stay in INMODE
        }
      }
      else if (key == KEY_A) {
        if (oldmode == KBDMODE) { // Do nothing here - exit with shift+U or shift+L
        }
        else if (oldmode == TXTEDITMODE) {
          clrline(2, 3);
          mode = oldmode;
          isshift = false;
        }
        else if (iscalcmode) {
          isnewnumber = true;
          iscalcmode = false;
          isshowfloatstack = false;
          clrline(0, 1);
          isshift = false;
          if (oldmode == RUNMODE) mode = RUNMODE; // Back to RUNMODE
          else if (oldmode == CMDMODE)mode = oldmode; // Back to CMDMODE
          else mode = oldmode = oldoldmode; // Back to oldoldmode
        }
        else {
          mode = oldmode;
          isshift = false;
        }
      }
      else if (key == KEY_R)
        if (byteselect < maxbyte) byteselect++;
        else byteselect = minbyte;
      else if (key == KEY_D)
        if (byteselect < maxbyte - linestep) byteselect += linestep;
        else byteselect = maxbyte;
      else if (key == KEY_L)
        if (byteselect > minbyte) byteselect--;
        else byteselect = maxbyte;
      else if (key == KEY_U)
        if (byteselect > minbyte + linestep) byteselect -= linestep;
        else byteselect = minbyte;
      else if (oldmode == KBDMODE && key == KEY_SHIFT_R) Keyboard.press(KEY_LEFT_ALT); // Alt
      else if (oldmode == KBDMODE && key == KEY_SHIFT_L) Keyboard.press(KEY_LEFT_CTRL); // Ctrl
      else { // Exit
        mode = oldmode = CMDMODE;
        ledsoff();
      }
    }

    else if (mode == PRGEDITMODE) { // *** EDIT prg[] ***
      if (key == KEY_B && prgptr < prg[prgnr][0]) { // Edit step
        oldmode = PRGEDITMODE;
        mode = INMODE;
        isinsertbyte = false;
        byteselect = prg[prgnr][prgptr];
        minbyte = 0; maxbyte = MAXBYTE; linestep = 16; // Limits
      }
      else if (key == KEY_A) {} // Do nothing - escape with SHIFT-UP
      else if (key == KEY_R) { // Page down
        if (prgptr < prg[prgnr][0] - 4) prgptr += 4;
        else prgptr = prg[prgnr][0];
      }
      else if (key == KEY_D) { // One step down
        if (prgptr < prg[prgnr][0]) prgptr++;
      }
      else if (key == KEY_L) { // Page up
        if (prgptr > PRGPTRSTART + 4) prgptr -= 4;
        else prgptr = PRGPTRSTART;
      }
      else if (key == KEY_U) { // One step up
        if (prgptr > PRGPTRSTART) prgptr--;
      }
      else if (key == KEY_SHIFT_R) { // Insert step
        minbyte = 0; maxbyte = MAXBYTE; linestep = 16; // Limits
        oldmode = PRGEDITMODE;
        mode = INMODE;
        isinsertbyte = true;
      }
      else if (key == KEY_SHIFT_L) { // Delete step
        if (prgptr < prg[prgnr][0]) {
          for (byte i = prgptr; i < prg[prgnr][0] - 1; i++) prg[prgnr][i] = prg[prgnr][i + 1];
          prg[prgnr][0]--;
        }
        repairgoto(false);
      }
      else { // Escape
        clrline(0, 1);
        mode = oldmode = oldoldmode;
        isshift = false;
      }
    }

    else if (mode == TXTEDITMODE) { // *** EDIT prg[] as text file ***
      for (byte i = PRGPTRSTART; i < prg[prgnr][0]; i++) if (prg[prgnr][i] == CURSORSIGN) prgptr = i;
      if (key == KEY_B || key == KEY_SHIFT_R) { // Insert character
        minbyte = ' '; maxbyte = '~'; linestep = 10; // Limits
        if (byteselect < minbyte || byteselect > maxbyte) byteselect = minbyte;
        oldmode = TXTEDITMODE;
        mode = INMODE;
      }
      else if (key == KEY_A) { // Do nothing - escape with SHIFT-UP
      }
      else if (key == KEY_R) { // Cursor right
        if (prgptr < prg[prgnr][0] - 1) {
          byte tmp = prg[prgnr][prgptr];
          prg[prgnr][prgptr] = prg[prgnr][prgptr + 1];
          prg[prgnr][prgptr + 1] = tmp;
        }
      }
      else if (key == KEY_D) { // Cursor down
        byte prgptrold = prgptr,  prgptrnew = prgptr;
        byte tmp = prg[prgnr][prgptr];
        for (byte i = prg[prgnr][0]; i > prgptr; i--) // Find next line feed
          if (prg[prgnr][i] == LFSIGN) prgptrnew = i;
        for (byte i = prgptrold; i < prgptrnew; i++) prg[prgnr][i] = prg[prgnr][i + 1];
        prg[prgnr][prgptrnew] = tmp;
      }
      else if (key == KEY_L ) { // Cursor left
        if (prgptr > PRGPTRSTART) {
          byte tmp = prg[prgnr][prgptr];
          prg[prgnr][prgptr] = prg[prgnr][prgptr - 1];
          prg[prgnr][prgptr - 1] = tmp;
        }
      }
      else if (key == KEY_U) { // Cursor up
        byte prgptrold = prgptr,  prgptrnew = prgptr;
        byte tmp = prg[prgnr][prgptr];
        for (byte i = PRGPTRSTART; i < prgptr; i++) // Find previous line feed
          if (prg[prgnr][i] == LFSIGN) prgptrnew = i;
        for (byte i = prgptrold; i > prgptrnew; i--) prg[prgnr][i] = prg[prgnr][i - 1];
        prg[prgnr][prgptrnew] = tmp;
      }
      else if (key == KEY_SHIFT_L) { // Backspace
        if (prgptr > PRGPTRSTART) {
          for (byte i = prgptr; i < prg[prgnr][0]; i++) prg[prgnr][i - 1] = prg[prgnr][i];
          prg[prgnr][0]--;
        }
      }
      else { // ESC
        if (!iscursor) { // Remove added cursor
          for (byte i = PRGPTRSTART; i < prg[prgnr][0]; i++) { // Search cursor
            if (prg[prgnr][i] == CURSORSIGN) { // Cursor found
              for (byte j = i; j < prg[prgnr][0] - 1; j++) // Remove cursor
                prg[prgnr][j] = prg[prgnr][j + 1];
              prg[prgnr][0]--;
            }
          }
        }
        mode = oldmode = oldoldmode;
        clrline(0, MAXLIN - 1);
        isshift = false;
      }
    }

    else if (mode == DISKMODE) { // *** MANIPULATE FILE SYSTEM ***
      if (key == KEY_B) { // Load file to prg[]
        byte tmp = EEPROM.read(fsptr);
        for (byte i = 0; i < tmp; i++) prg[prgnr][i] = EEPROM.read(fsptr + i);
        prgptr = PRGPTRSTART;
      }
      else if (key == KEY_A) { // Do nothing - escape with SHIFT-UP
      }
      else if (key == KEY_R) { // Page down - 4 files forward
        for (byte i = 0; i < MAXLIN && EEPROM.read(fsptr) != NULL; i++)
          fsptr += EEPROM.read(fsptr);
      }
      else if (key == KEY_D) { // Next prg
        if (EEPROM.read(fsptr) != 0) fsptr += EEPROM.read(fsptr);
      }
      else if (key == KEY_L) { // Page up - 4 files back
        for (byte i = 0; i < MAXLIN; i++) fsptr = prevfileptr(fsptr);
      }
      else if (key == KEY_U) { // Previous prg
        int tmp = EEDISKSTART;
        while (tmp + EEPROM.read(tmp) < fsptr && EEPROM.read(tmp) != NULL)
          tmp += EEPROM.read(tmp);
        fsptr = tmp;
      }
      else if (key == KEY_SHIFT_R) { // Insert - Save prg[] to EEPROM
        int tmp = eofs();
        if (EEDISKEND - tmp > prg[prgnr][0]) { // Enough memory
          for (int i = tmp + prg[prgnr][0]; i >= fsptr + prg[prgnr][0]; i--)
            EEPROM.write(i, EEPROM.read(i - prg[prgnr][0]));
          for (byte i = 0; i < prg[prgnr][0]; i++) EEPROM.write(fsptr + i, prg[prgnr][i]);
        }
        else { // No memory
          oldmode = DISKMODE;
          mode = MSGMODE;
          msg = MSGNOMEM;
        }
      }
      else if (key == KEY_SHIFT_L) { // Delete file
        int tmp = EEPROM.read(fsptr);
        byte tmpeofs = eofs();
        for (int i = fsptr; i <= tmpeofs - tmp; i++)
          EEPROM.write(i, EEPROM.read(i + tmp));
      }
      else {//if (key == KEY_SHIFT_U) { // ESC
        mode = oldoldmode;
        isshift = false;
      }
    }

    else if (mode == CLKMODE) { // *** CLOCK ***
      if (key == KEY_B) { // Reset seconds
        clockmillis = (long)(clockmillis / 60000) * 60000;
        starttime = millis();
      }
      else if (key == KEY_R) clockmillis += 60000; // + 1 min
      else if (key == KEY_D) // - 1 h
        if (clockmillis > 3600000) clockmillis -= 3600000;
        else clockmillis = 0;
      else if (key == KEY_L && clockmillis > 60000) clockmillis -= 60000; // - 1 min
      else if (key == KEY_U) clockmillis += 3600000; // + 1 h
      printclock();
      if (key == KEY_A) { // End clock mode
        clrline(0, 1);
        isshift = false;
        mode = CMDMODE;
      }
    }

    else if (mode == YESNOMODE) { // *** DECISION ***
      if (key == KEY_R) { // YES
        if (prgptr < prg[prgnr][0] - 4) prgptr += 4;
      }
      else if (key == KEY_L) { // NO
        if (prgptr < prg[prgnr][0] - 2) prgptr += 2;
      }
      isshift = false;
      clrline(1, 1);
      mode = oldmode;
    }

    else if (mode == MSGMODE) { // *** END MESSAGE MODE
      clrline(0, 1);
      mode = oldmode;
      //isshift = false;
    }

    /*for (int i = EEDISKSTART; i < EEDISKSTART + 20; i++) {
      Serial.print(" ");
      Serial.print(EEPROM.read(i));
      }
      Serial.println();

      /*Serial.print("prgptr=");
      Serial.print(prgptr);
      Serial.print(" prg=");
      for (byte i = 0; i < min(10, prg[prgnr][0]); i++) {
      Serial.print(prg[prgnr][i]); Serial.print(" ");
      }
      Serial.println();

      /*Serial.print("key="); Serial.print((char)key);
      Serial.print(" mode="); Serial.println(mode);*/

    /*Serial.print("BS=");
      for (byte i; i < BYTESTACKSIZE; i++) {
      Serial.print(bytestack[i]); Serial.print(" ");
      }
      Serial.println();
      /*Serial.print("ptr="); Serial.println(prgptr);
      Serial.print("len="); Serial.println(strlen(prg));
      Serial.print("PRG=");
      for (byte i; i < 10; i++) {
      Serial.print(prg[i]); Serial.print(" ");
      }
      Serial.println();*/
    //Serial.print(floatstack[0]); Serial.print(" "); Serial.print(floatstack[1]); Serial.print(" "); Serial.println(floatstack[2]);

  } // End of if(key)

  if (mode != CLKMODE) {
    if (millis() - screenofftimestamp > screenofftime * 1000L) { // Screensaver
      screenoff();
      isscreensave = true;
    }
    if (millis() - powerofftimestamp > POWEROFFTIME * 1000L) { // Auto poweroff
      poweroff();
      powerofftimestamp = screenofftimestamp = millis(); // Deactivate poweroff and screensaver
      delay(1000);
      ledsoff();
    }
  }

  if (!isscreensave) { // Print display
    printscreen();
    display();
  }
}



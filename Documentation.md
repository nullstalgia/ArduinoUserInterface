# Arduino User Interface:

This library is a simple user interface for building Arduino applications.  Text and menus are shown on an LCD display.  Four push buttons (Up, Down, Select & Back) make the interface very intuitive.

The *Arduino User Interface* library requires a *Nokia 5110 LCD* display and 4 push buttons.  The library works well with the *Arduino UI Shield*, which plugs directly on a Uno or Mega.  

![alt_text](images/ArduinoUIShield.jpg "Arduino UI Shield")

Unfortunately this shield is not commercially available, so you will need to build your own.  PCB files and a schematic can be found at the GitHub link below.   You can also just wire up the display and buttons on a protoboard.  Find the hookup guide by following the GitHub link.

 

Documentation for the *Arduino User Interface* library and *Arduino UI Shield* can be found at:
    https://github.com/Stan-Reifel/ArduinoUserInterface



##### Overview:

The user interface displays text and menus on a *Nokia 5110 LCD*  display.  The display is divided into two sections.  The top section is the *DisplaySpace*.  Here is where menus, message boxes, configuration screens, and the application's main displays are shown.

Displayed on the screen's bottom line is the *ButtonBar*.  Mounted just below the LCD are two push buttons.  The function of these buttons change, so the *ButtonBar* is used to label what the push buttons do.  Typically the left button will be labeled *Select* and the right one *Back*.

Two more buttons are used, one to go *Up* and the other to go *Down*.  These are mounted to the right side of the LCD.



##### Connecting the hardware:

// The library needs to be told which Arduino pins are connected to the display.  
// The default values used here assume you have an "Arduino UI Shield" and 
// it's plugged into a Uno or Mega.  If you have different hardware, be sure the 
// call to connectToPins() is setup properly.









//
// Notes on building the menu table:
//
// The first line in the table always defines what type of menu it is, either a 
// Main Menu, or a Sub Menu.  The table's last line marks the end of the table.  
// In between are menu items, each line represents one menu choice the user will
// see displayed.
//
// There are three types of menu items: Commands, Toggles, and Sub Menus. In this
// sketch we are only going to explore "Commands".
//
// A MENU_ITEM_TYPE_COMMAND entry indicates that a function will be executed when 
// the menu item is selected by the user. In the second field is the text 
// displayed in the menu.  The third field is a pointer to a function that is 
// executed when this menu item is chosen by the user.  The last field should 
// always be NULL.



//
// Notes on building the menu table:
//
// Earlier examples have menus filled with "Commands".  Here we show a menu 
// with "Commands" and "Toggles".  Toggles let the user select one of a fixed
// number of choices (such as On / Off,  or  Red / Green / Blue).  Each time
// the user clicks a toggle menu item, it alternates the selection.
//
// In the second field of a A MENU_ITEM_TYPE_TOGGLE entry is the text displayed
// in the menu.  The third field is a pointer to a callback function that you 
// write to alternates the value. The last field should always be NULL

// Toggles are used somewhat like Radio Buttons in a dialog box.  They allows 
// the user to select one of a fixed number of choices (such as On / Off, or   
// Red / Green / Blue).  Each time the user clicks on a toggle menu item, it 
// alternates the selection (i.e. toggles between On and Off, or rotates 
// between Red, Green and Blue).



// A Slider allows the users to select a numeric value (such as 0 to 255, or
// -1000 to 1000,  or  0.0 to 15.5).  There are two types of slides, one for
// INTs and one for FLOATs.



// Toggles and Slider are often used to configure your project at runtime.  
// These settings can be saved in the Arduino's EEPROM so the project 
// defaults to the configured values when powered up.
//
// The "Arduino User Interface" library has these functions for saving/reading
// configuration values:
//      writeConfigurationByte()   and   readConfigurationByte()
//      writeConfigurationInt()    and   readConfigurationInt()
//      writeConfigurationLong()   and   readConfigurationLong()
//



//
// Notes on building the main menu / sub menu:
//
// A MENU_ITEM_TYPE_SUB_MENU entry is used to select a different menu.  For 
// example, a main menu might reference a "Settings" sub menu. The fourth field  
// in this entry points to the menu table that defines the sub menu.
//

// Often it is useful to group related commands into their own menu, this 
// is what Sub-menus are for.  This example show how to create a Main-menu 
// that has one or more sub-menus. 
//



//
// Notes on building the sub menu:
//
// Sub-menus are menus called from the main-menu, or another sub-menu. The
// Sub-menu table is built just like the Main-menu, except the first entry
// must be MENU_ITEM_TYPE_SUB_MENU_HEADER. In this entry's fourth field is a 
// pointer back to the parent menu table (typically the main menu).  This is 
// used to reselect the parent menu when the user presses the "Back" button, 
// indicating they are done with the sub menu.





/
// Notes on building the Main-menu:
//
// In this app, the first screen the user sees is not a menu, but the stopwatch.
// The user calls up the menu by pressing the "Menu" button.  They will return 
// to the stopwatch from the menu by pressing the "Back" button.
//
// Normally the Main-menu does not show a "Back" button.  To enable the "Back",
// the fourth field in the MENU_ITEM_TYPE_MAIN_MENU_HEADER line is set to NULL.
// 

// The previous examples show sketches that first run by displaying a menu.  
// In some cases you want to start your application with its display, then 
// press a button to pull up its menu.  This sketch runs a Stopwatch.  The 
// stopwatch is displayed immediately when powered up.  At anytime the user  
// can press the "Menu" button to get a list of options.  







##### Blocking vs Non-blocking function calls:

Blocking functions are a limitation if you want to do other things while the motor is running.  Examples of "other things" might be to:  1) Run two motors at once.  2) Check if a button is press, then decelerate to a stop.  3) Turn on a solenoid when the motor moves past position 850.  4) Flash a strobe every 200 steps.  5) Run continuously as long as a temperature is below 125 degrees.  To do these types of things *Non-blocking* functions are used.  Here is an example:

```
  //
  // setup the motor so that it will rotate 2000 steps, note: this 
  // command does not start moving yet
  //
  stepper.setupMoveInSteps(2000);
  
  //
  // now execute the move, looping until the motor has finished
  //
  while(!stepper.motionComplete())
  {
    stepper.processMovement();       // this call moves themotor
    
    //
    // check if motor has moved past position 400, if so turn On the LED
    //
    if (stepper.getCurrentPositionInSteps() == 400)
      digitalWrite(LED_PIN, HIGH);
  }
```



### Usage examples:

```
Near the top of the program, add:
    include "SpeedyStepper.h"

For each stepper, declare a global object outside of all functions as follows:
    SpeedyStepper stepper1;
    SpeedyStepper stepper2;

In Setup(), assign IO pins used for Step and Direction:
    stepper1.connectToPins(10, 11);
    stepper2.connectToPins(12, 14);
```


    Move one motor in units of steps:
        //
        // set the speed in steps/second and acceleration in steps/second/second
        //
        stepper1.setSpeedInStepsPerSecond(100);
        stepper1.setAccelerationInStepsPerSecondPerSecond(100);
    
        //
        // move 200 steps in the backward direction
        //
        stepper1.moveRelativeInSteps(-200);
    
        //
        // move to an absolute position of 200 steps
        //
        stepper1.moveToPositionInSteps(200);


    Move one motor in units of revolutions:
        //
        // set the number of steps per revolutions, 200 with no microstepping, 
        // 800 with 4x microstepping
        //
        stepper1.setStepsPerRevolution(200);
    
        //
        // set the speed in rotations/second and acceleration in 
        // rotations/second/second
        //
        stepper1.setSpeedInRevolutionsPerSecond(1);
        stepper1.setAccelerationInRevolutionsPerSecondPerSecond(1);
    
        //
        // move backward 1.5 revolutions
        //
        stepper1.moveRelativeInRevolutions(-1.5);
    
        //
        // move to an absolute position of 3.75 revolutions
        //
        stepper1.moveToPositionInRevolutions(3.75);


    Move one motor in units of millimeters:
        //
        // set the number of steps per millimeter
        //
        stepper1.setStepsPerMillimeter(25);
    
        //
        // set the speed in millimeters/second and acceleration in 
        // millimeters/second/second
        //
        stepper1.setSpeedInMillimetersPerSecond(20);
        stepper1.setAccelerationInMillimetersPerSecondPerSecond(20);
    
        //
        // move backward 15.5 millimeters
        //
        stepper1.moveRelativeInMillimeters(-15.5);
    
        //
        // move to an absolute position of 125 millimeters
        //
        stepper1.moveToPositionInMillimeters(125);


    Move two motors in units of revolutions:
        //
        // set the number of steps per revolutions, 200 with no microstepping, 
        // 800 with 4x microstepping
        //
        stepper1.setStepsPerRevolution(200);
        stepper2.setStepsPerRevolution(200);
    
        //
        // set the speed in rotations/second and acceleration in 
        // rotations/second/second
        //
        stepper1.setSpeedInRevolutionsPerSecond(1);
        stepper1.setAccelerationInRevolutionsPerSecondPerSecond(1);
        stepper2.setSpeedInRevolutionsPerSecond(1);
        stepper2.setAccelerationInRevolutionsPerSecondPerSecond(1);
    
        //
        // setup motor 1 to move backward 1.5 revolutions, this step does not 
        // actually move the motor
        //
        stepper1.setupRelativeMoveInRevolutions(-1.5);
    
        //
        // setup motor 2 to move forward 3.0 revolutions, this step does not 
        // actually move the motor
        //
        stepper2.setupRelativeMoveInRevolutions(3.0);
    
        //
        // execute the moves
        //
        while((!stepper1.motionComplete()) || (!stepper2.motionComplete()))
        {
          stepper1.processMovement();
          stepper2.processMovement();
        }



# The library of functions  

### Setup functions: 

```
//
// connect the stepper object to the IO pins
//  Enter:  stepPinNumber = IO pin number for the Step
//          directionPinNumber = IO pin number for the direction bit
//          enablePinNumber = IO pin number for the enable bit (LOW is enabled)
//            set to 0 if enable is not supported
//
void connectToPins(byte stepPinNumber, byte directionPinNumber)
```



### Functions with units in millimeters: 

```
//
// set the number of steps the motor has per millimeter
//
void setStepsPerMillimeter(float motorStepPerMillimeter)


//
// get the current position of the motor in millimeter, this functions is updated
// while the motor moves
//  Exit:  a signed motor position in millimeter returned
//
float getCurrentPositionInMillimeters()


//
// set current position of the motor in millimeter, this does not move the motor
//
void setCurrentPositionInMillimeters(float currentPositionInMillimeter)


//
// set the maximum speed, units in millimeters/second, this is the maximum speed  
// reached while accelerating
// Note: this can only be called when the motor is stopped
//  Enter:  speedInMillimetersPerSecond = speed to accelerate up to, units in 
//          millimeters/second
//
void setSpeedInMillimetersPerSecond(float speedInMillimetersPerSecond)


//
// set the rate of acceleration, units in millimeters/second/second
// Note: this can only be called when the motor is stopped
//  Enter:  accelerationInMillimetersPerSecondPerSecond = rate of acceleration,  
//          units in millimeters/second/second
//
void setAccelerationInMillimetersPerSecondPerSecond(
                      float accelerationInMillimetersPerSecondPerSecond)


//
// home the motor by moving until the homing sensor is activated, then set the  
// position to zero, with units in millimeters
//  Enter:  directionTowardHome = 1 to move in a positive direction, -1 to move in 
//             a negative directions 
//          speedInMillimetersPerSecond = speed to accelerate up to while moving 
//             toward home, units in millimeters/second
//          maxDistanceToMoveInMillimeters = unsigned maximum distance to move 
//             toward home before giving up
//          homeSwitchPin = pin number of the home switch, switch should be 
//             configured to go low when at home
//  Exit:   true returned if successful, else false
//
bool moveToHomeInMillimeters(long directionTowardHome,  
  float speedInMillimetersPerSecond, long maxDistanceToMoveInMillimeters, 
  int homeLimitSwitchPin)


//
// move relative to the current position, units are in millimeters, this function  
// does not return until the move is complete
//  Enter:  distanceToMoveInMillimeters = signed distance to move relative to the  
//          current position in millimeters
//
void moveRelativeInMillimeters(float distanceToMoveInMillimeters)


//
// setup a move relative to the current position, units are in millimeters, no   
// motion occurs until processMove() is called
// Note: this can only be called when the motor is stopped
//  Enter:  distanceToMoveInMillimeters = signed distance to move relative to the  
//            currentposition in millimeters
//
void setupRelativeMoveInMillimeters(float distanceToMoveInMillimeters)


//
// move to the given absolute position, units are in millimeters, this function 
// does not return until the move is complete
//  Enter:  absolutePositionToMoveToInMillimeters = signed absolute position to 
//            move toin units of millimeters
//
void moveToPositionInMillimeters(
                      float absolutePositionToMoveToInMillimeters)


//
// setup a move, units are in millimeters, no motion occurs until processMove() is 
// called.  Note: this can only be called when the motor is stopped
//  Enter:  absolutePositionToMoveToInMillimeters = signed absolute position to move  
//          to in units of millimeters
//
void setupMoveInMillimeters(
                      float absolutePositionToMoveToInMillimeters)


//
// Get the current velocity of the motor in millimeters/second.  This functions is 
// updated while it accelerates up and down in speed.  This is not the desired  
// speed, but the speed the motor should be moving at the time the function is   
// called.  This is a signed value and is negative when motor is moving backwards.
// Note: This speed will be incorrect if the desired velocity is set faster than
// this library can generate steps, or if the load on the motor is too great for
// the amount of torque that it can generate.
//  Exit:  velocity speed in millimeters per second returned, signed
//
float getCurrentVelocityInMillimetersPerSecond()

```





Copyright (c) 2018 S. Reifel & Co.  -   Licensed under the MIT license.
# Bindings Lib 
(the name is a work in progress)

## Installation
* install as vendordep https://earthquakers9094.github.io/bindings_lib/bindings/bindings.json
* thats it

## Using
the main class of this library is `bindings.Bindings`
* Constructor Bindings(`driver`, `operator`, `constants`)
  * takes a `driver` and an `operator` to lock the program to (or null not to do this)
     * this is recommended for competition because it enables the driver and operator to be loaded during robot inititaliaztion instead of when you call resetCommands for the first time
     * you can unlock the driver and operator by pressing the unlock button on smartdashboard or calling `unlockDrivers()` on the Bindings object
  * the it takes another argument called `constants` you just pass in an instance of the class (the values of the properties of the class don't matter) containing the constants that you want to manage with the bindings
* getConstants()
  * returns the `constants` structure that you passed in with it's data filled in using the current drivers and global constants configuration (before the drivers are know the drivers constants are just initialized to their default values)
* resetCommands()
  * this is used to refresh the bindings and constants that the library manages it is recommended to call this at the start of teleop (if no changes have been made it will won't reload the constants and bindings)

the other main class to be aware of is `bindings.Constant` this is used in the constants structure to provide a constant that you can lister for changes on
* Constructor Constant(`value`)
  * does't matter what `value` is inititalized to it is only used for it's type
* getValue()
   * gets the current value of the constant
* addListener((v: Value) -> Unit?)
   * registers a listener in the form of a lamba to allow the user of the library to listen for changes in the constant (ie to update pid values on motor controllers)

## Example
* RobotContainers constuctor
```java
NamedCommands.registerCommand("log a", new InstantCommand(() -> {
      int val = bindings.getConstants().val;

      DriverStation.reportWarning("logging a " + val, false);
    }));

    // Named Commands are from pathplanner and it is how this library gets the actual commands from their names
    // that way you don't have add commands to both pathplanner and this library
    NamedCommands.registerCommand("log b", new InstantCommand(() -> {
      int b = bindings.getConstants().driver.b.getValue();

      DriverStation.reportWarning("logging b " + b, false);
    }));
    
    NamedCommands.registerCommand("log c", new InstantCommand(() -> {

      DriverStation.reportWarning("logging c", false);
    }));

    // locks the driver to john_doe and leaves the operator free to be selected
    // (note it doesn't really matter which one is which it only influences
    // priority when it comes to driver constants)
    bindings = new Bindings("john_doe", null, new Constants());

    bindings.getConstants().driver.b.addListener((n) -> {
      DriverStation.reportWarning("b changed to: " + n, false);
      return null;
    });
```
* Constants File
```java
import bindings.Constant;

public class Constants {
  // supported types for constants are currently strings, ints, and doubles,
  // or Constants<T> to be able to listen to changes 
  public int val;

  // other objects are allowed to exist in the constants object
  // they have the same requirements as the Constants class in
  // terms of their members
  public Driver driver;

  public static class Driver {
    // wrapping it in the constants class allows you to listen for changes
    // with addListener and you can retreive the value it has with getValue
    public Constant<Integer> b;

    public Driver() {
      // this value doesn't matter it just needs to be initialized at some point
      this.b = new Constant<>(0);
    }
  }

  public Constants() {
    // these values don't matter they just need to be initialized at some point
    this.val = 0;
    this.driver = new Driver();
  }
}
```
## GUI
Available at https://github.com/EarthQuakers9094/bindings_gui
### Usage
locked tabs (made to prevent the driver team from accidentally touching something they aren't supposed to not meant to be secure) password `theyWillNeverKnow!`

add the commands that can be bound in the manage commands tab (can't bind a command without first adding it)

switch profiles in the profiles tab

# Chat Commands
A library for the Warcraft 3 programming language Wurst, allowing you to easily register commands entered via chat, and run your own custom code. 


_Example_
```
...
import ChatCommands

init
    new ChatCommand("-kill")
    ..setAction() (p, args, opts) ->
        forUnitsSelected(p) u ->
            u.kill()
```  

Now, when a player writes `-kill` in the chat, all units selected by the commanding player will die 💀.


## Table of Contents

- ### [Installing](#Installing)
- ### [Documentation](#Documentation)
  - [Arguments](#Arguments)
  - [Options](#Options)
  - [Sub Commands](#Sub-Commands)
  - [Error Handling](#Error-Handling)
- ### [Utility Commands](#Utility-Commands)
  - [Command List](#Command-list)
  - [Custom Commands](#Customizing-Utility_Commands)



# Installing
There are 2 ways of using the library in your project:

  1. ___Wurst Dependency___  
  Install it like any other Wurst library by adding the github repo to your project via the Wurst setup tool.
  
  
  2. ___Specific Release___  
  Wurst doesn't handle version numbers of dependencies yet, so a release of each significant version is stored in the [Releases tab](https://github.com/maltebp/ChatCommands/releases).  
    These may be downloaded manually and copied into your project.


# Documentation
___Hint:___ _Use the commands in `UtilCommands` package for additional inspiration / documentation_

## Arguments
You may define a series of _required_ parameters for your command by using _arguments_:

```
new ChatCommand("-cmd")
..addArgument(ArgumentType.INT)
```

__Entering Arguments__  
The Player entering the command is _required_ to provide a value for each argument. For the above command this would mean:


```
-cmd 10      ✔
-cmd 10 10   ✖
-cmd         ✖
```

Argument values are provided immediately after the command.
  

__Argument Type__  
You add an argument by providing an _argument type_. This is an _enum_ with following options:

  - _INT_
  - _REAL_
  - _STRING_

The Player may only provide a value for the argument matching its type, meaning that given a single INT argument the followins holds:

```
-cmd 10      ✔
-cmd hello   ✖
-cmd 10.5    ✖
```

__Using Arguments__  
Entered arguments can be fetched from the ArgumentList provided with the action callback:

```
new ChatCommand("-hello")
..addArgument(ArgumentType.STRING)
..setAction() (p, args, opts) ->
    print("Hello " + args.getString())
```

The library makes sure a correct value is entered, but it's the responsibility of map maker to fetch the correct argument type (i.e. `args.getInt()` would cause an error in this case). Also the arguments must be fetched __in the order they're defined__.



## Options
You may provide _optional_ parameters to your command using _options_:

```
new ChatCommand("-cmd")
..addOptionInt("a", 0)
..addOptionString("b", 0)
..addOptionReal("c", 0)
..addOptionSwitch("d")
```

Options are _not required_ to be provided by the player, but all options have a default value (second parameter).

__Option Type__  
Options use the same types as the Arguments, except for _switch_ which is specific to options. A switch is basically a boolean value: if i'ts present, it's true, otherwise it's false.

__Order__  
Options may be provided by the Player _after_ the arguments, but otherwise in _any_ order:

```
new ChatCommand("-cmd")
..addOptionInt("a", 0)
..addOptionString("b", 0)

Chat:
 -cmd a=10 b=hello  ✔
 -cmd b=hello a=10  ✔
```


__Using Options__  
Options are fetched from the OptionList provided with the action callback:

```
new ChatCommand("-hello")
..addOptionInt("count", 1)
..setAction() (p, args, opts) ->
    for i=1 to opts.getInt("count")
        print("Hello")
```

If the player hasn't provided a custom value for an option, fetching an option value will return the defined default value.

Just like arguments, the library makes sure the option is entered correctly, but its the map makers responsibility to fetch the correct value type.





## Sub Commands
Any command may be given several _sub commands_. A sub command works identically to a regular command, only the command is prefixed with its parent command:

```
    new ChatCommand("-cmd")
    ..addSubCommand("sub1")
    ..addSubCommand("sub2")

    Chat:
    -cmd sub1
    -cmd sub2
```

A command may have both sub commands __and__ its own action / arguments / options.





# Utility Commands
The library includes an additional utility package named `UtilCommands`, which provides a series of commands useful for general map development and testing, such as creating and removing units.  

All utility commands are prefixed with the UTILCOMMAND_PREFIX (default is `-u`, i.e. `-u kill`).  

_Help_  
All default utility commands are provided with a `help` command, making it possible to get help in-game (i.e. `-u help` to list all commands, and `-u restore help` to get help for the _restore_ command).

## Command list
List of current default utility commands. Arguments are displayed in square brackets, and options are displayed in parenthesis.



_Unit Commands_

- ```remove```  
Removes all selected units

- ```kill```  
Kill all selected units

- ```restore (h, m, a=0)```  
Restore the health and mana of selected units
  - `h`: Restore only health
  - `m`: Restore only mana 
  - `a=0`: The amount restored. If `0` the units are restored to full.

- ```createunit [STRING] (n=1, p=-1)```  
Create one or several units with the given unit type ID (i.e. `hfoo`) for a player.
  - `n`: Number of units to create
  - `p`: Player ID of unit owner. If `-1`, the commanding player is used.

- ```typeid```  
Display the Unit Type ID of selected units.


_Unit Stats_
- ```lvl [INT] (a)```  
Set the level of selected Heroes.
  - `a`: Adjust with the value, rather than setting it

- ```exp [INT] (s)```  
Adjust the experience of selected Heroes by value.
  - `s`: The experience will be set rather than adjusted. 

- ```health/mana [INT] (a, c)```  
Set the maximum mana/health to the given value for selected units.  
  - `a`: The health/mana is adjusted with given value rather than setting it
  - `c`: The current health/mana is manipulated rather than the total.  

- ```damage [INT] (a, i=0)```  
Set the damage for selected units to given value.
  - `a`: Adjust with the value, rather than setting it
  - `i`: The weapon index (either 0 or 1)

- ```armor [INT]```  
Set the armor for selected units to given value.
  - `a`: Adjust with the value, rather than setting it

- ```movespeed [INT]```  
Set the movespeed for selected units to given value.
  - `a`: Adjust with the value, rather than setting it

_Unit Abilities_
- ```abil add/remove [STRING]```  
Remove add ability with given ability ID for selected units.

- ```abil lvl [STRING]```  
Get the level of ability with given ability ID for selected units. 

- ```abil lvl set [STRING] [INT]```  
Set the level of ability with given ability ID for selected units to the given value.

_Resource_  
- ```gold/lumber [INT] (p=-1, a)```  
Adjust the player resource with a given value.
  - `s`: The resource will be set rather than adjusted. 
  - `p`: Player ID of player to adjust resource of. If `-1`, the commanding player is used.


_Other_
- ```createitem [STRING] (n=1)```  
Create `n` number of items with the given item type ID (i.e. `ratc`).


## Customizing Utility Commands

__Adding Utility Commands__  
If you need additional custom utility commands, its recommended to create these using the utility library:

```
defineUtilityCommand("mycommand")
```

This allows you to disable and enable the commands together with the default commands.

__Changing Existing Commands__  
A function exists for each default utility command, and each function is _configurable_ allowing you to define your own, or removing the command entirely.  




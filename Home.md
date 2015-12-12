## Quick Start

Using the CommandLineParser is intuitive. Let's say you want to support
 1. a switch (`-s` or `--show`)
 2. an argument which requires a value of type decimal (`-v 1.0` or `--version 1.0`) 
 3. an argument which allows to choose from 3 predefined values: red, green or blue (`-c red` or `--color red`)
(For detailed information about argument types and settings, see **TBD**)

### Setup

```csharp
CommandLineParser.CommandLineParser parser = 
    new CommandLineParser.CommandLineParser();
SwitchArgument showArgument = new SwitchArgument(
    's', "show", "Set whether show or not", true);
ValueArgument<decimal> version = new ValueArgument<decimal>
    'v', "version", "Set desired version");
EnumeratedValueArgument<string> color = new EnumeratedValueArgument<string>
    'c', "color", new string[] { "red", "green", "blue" });
parser.Arguments.Add(showArgument);
parser.Arguments.Add(version);
parser.Arguments.Add(color);
```

### Parsing arguments 

In your main method, you can parse the arguments: 
```csharp
public static void main(string[] args) {
    CommandLineParser.CommandLineParser parser; 
    // set up the parser here as above...  
    parser.ParseCommandLine(args); 
    // access the values 
    if (color.Parsed) /// test, whether the argument appeared on the command line
    {
        var c = color.Value; //contains value of the level argument
    }  
}
```

### Show Usage 
You might want to show an overview of all the arguments your application supports. This is very easy, the following code: 
'''csharp
parser.ShowUsageHeader = "Here is how you use the app: ";
parser.ShowUsageFooter = "Have fun!";
parser.ShowUsage();
'''
will print: 
```
Here is how you use the app:
Usage:
        -s, --show [optional] ... Set whether show or not
   
        -v, --version [optional] ... Set desired version

        -c, --color [optional] ...
Have fun!
```

You can also add long descriptions, use resource files to store them (see TBD) and even use localization (see TBD). 
You can also tell the parser to print the Usage info automatically when no arguments are supplied (see `ShowUsageOnEmptyCommandline` property)

### Attributes & declarative syntax

You can create a custom class to hold all your argument values in fields or properties which you decorate with attributes - `CommandLineParser` will take care of extracting the information and populating the values. 

```csharp 
class ParsingTarget
{
    [SwitchArgument('s', "show", true, Description = "Set whether show or not")]
    public bool show;

    [ValueArgument(typeof(decimal), 'v', "version", Description = "Set desired version")]
    public decimal version;

    [EnumeratedValueArgument(typeof(string),'c', "color", AllowedValues = "red;green;blue")]
    public string color;
}

...

ParsingTarget p = new ParsingTarget();
parser.ExtractArgumentAttributes(p);
parser.ParseCommandLine(args);

// all arguments are now stored in parsing target
if (p.version > 2)
{
   ... 
}
```

See **TBD** for more details on declarative syntax. 

### Extensibility

The library offers a set of argument types which will cover the most use cases, but you can extend it with your own type of attributes. See **TBD** on how to do that. 

### Combination of attributes

`CommandLineParser` can be set up so that only certain combination of attributes are valid. You can do this with `ArgumentCertification`. This will even be reflected in ShowUsage command. See **TBD** for details. 



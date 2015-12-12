The usual scenario for command line arguments is setting a value of some object to a value of the argument. Because this is so common, CommandLieParser library provides additional support for this scenario. 

For each argument type there exists a corresponding attribute type. Such an attribute can be used for a field or property of your class. 

When CommandLineParser parses an attribute, the value of the attribute is then passed to you object. 

You can mark as many object as you want with argument attributes. There are only obvious limitations - each field can be bound only to one attribute and there must not exist arguments with colliding names. 

This is an example of using SwitchArgumentAttribute for binding a SwitchArgument to a boolean field of a class. 

```csharp
class MyObject
{
    [SwitchArgument('s', "show", true, Description = "Set whether show or not")]
    public bool show;
}
```

later the parser class is initialized like this:
```csharp
CommandLineParser parser = new CommandLineParser();
MyObject obj = new MyObject();
// read the argument attributes 
parser.ExtractArgumentAttributes(obj);
...
parser.ParseCommandLine(args);
...
if (obj.show) // if the argument appeared on the command line, the field will now have the correct value
{
   ... 
}
```
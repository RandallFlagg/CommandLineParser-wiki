`CommandLineParser` library allows you to define arguments of your own (via deriving from `Argument` class), but there are several argument types already included and ready to use. 

## Arguments 

All argument types derive from the base class `Argument`, which provides these common properties and settings: 

 * `ShortName` and `LongName` (single character or single word resp.)
 * `ShortAliases` and `LongAliases` (e.g. --ver and --version can map to the same value)
 * `Optional` - if set to false, parsing throws an exception when this argument does not appear on the command line
 * `AllowMultiple` - if set to true, the argument can appear multiple times on the command line. Most useful for `ValueArguments`. 
 * `Description` - short description of the argument 
 * `FullDescription` - full description of the argument (can contain examples etc.)
 * `Parsed` - after parsing, this flag will be set to `true` if the argument appeared on the command line 
 

## SwitchArgument

You can use `SwitchArgument` to define an argument with true/false logic. Switch argument has its default value - the initial value and value that argument holds when the user does not use the argument on the command line. Each time the user uses the argument, the value is flipped. Suppose "Finder" application that has "distinct" switch argument with default value false. This usage: 
```
Finder.exe --distinct
```
flips the value of distinct argument to true. Note that the switch argument is not followed by value therefore this: `Finder.exe --distinct false` would result in something that the user probably didn't want - default value of "distinct" argument (false) would be flipped to true and the word false would be interpreted as an additional argument not related to "distinct" argument. To define a bool argument followed by value, use ValueArgument<bool> instead. 

A full example of `SwitchArgument`: 

```csharp
    public static void Main(string[] args)
    {
        /* create the parser class */
        CommandLineParser.CommandLineParser parser =
            new CommandLineParser.CommandLineParser();
        /* define switch argument -d (resp. --distinct) */
        SwitchArgument showArgument = new SwitchArgument(
            'd', "distinct", "Remove duplicities from the result set.", false);
        /* add the argument to the set of accepted arguments */
        parser.Arguments.Add(showArgument);
        try
        {
            /* parse command line arguments */
            parser.ParseCommandLine(args);
            /* this prints results to the console */
            parser.ShowParsedArguments();
        }
        catch (CommandLineException e)
        {
            Console.WriteLine(e.Message);
            parser.ShowUsage();
        }
        //...
        if (showArgument.Value)
        {
            // remove duplicities
        }
        else
        {
            // print the whole set with duplicities
        }
    }
```

## ValueArgument

You can use `ValueArgument` to define an argument with value (of any type). User can then type the argument and its value on the command line. Suppose "Finder" application that has "subdirectories" argument with integer value. You would define it as `ValueArgument<int>` and then it could be used like this: 

```
Finder.exe --subdirectories 3
```
`ValueArgument` class takes care of parsing the value and converting it to the right type. For built-in C# types (bool, int, double, string, char etc.) the static .NET function Parse is used. If you want to use some other type (your class or struct for example) as arguments value, you have to specify a conversion routine that converts string on the command line to your type. 

A full example of `ValueArgument`: 

```csharp
    public static void Main(string[] args)
    {
        /* create the parser class */
        CommandLineParser.CommandLineParser parser =
            new CommandLineParser.CommandLineParser();
        /* define switch argument -d (resp. --distinct) */
        ValueArgument<string> patternArgument = new ValueArgument<string>(
            'p', "pattern", "The searched pattern.");
        /* set the argument as mandatory */
        patternArgument.Optional = false;
        /* add the argument to the set of accepted arguments */
        parser.Arguments.Add(patternArgument);
        try
        {
            /* parse command line arguments */
            parser.ParseCommandLine(args);
            /* this prints results to the console */
            parser.ShowParsedArguments();
        }
        catch (CommandLineException e)
        {
            Console.WriteLine(e.Message);
            parser.ShowUsage();
        }
        //...
        /* work with the value of the argument */
        Search(patternArgument.Value);
    }

    public static void Search(String pattern)
    {
        //search for given pattern...
    }
```

### Multiple values

If you want to allow multiple values for one argument, set `AllowMultiple` property to true. To get the parsed values, use `ValueArgument.Values` collection. If you are using attributes (declarative syntax), you need to use the attribute on an array or list property/field

## EnumeratedValueArgument

`EnumeratedValueArgument` is similar to `ValueArgument`, only its values must belong to a user defined set of possible values. Remark - if you want to map the set of values to your `enum` type, you can use ValueArgument<TValue> where `TValue` is your `enum` type instead. 

A full example of `EnumeratedValueArgument`

```csharp
    public static void Main(string[] args)
    {
        /* create the parser class */
        CommandLineParser.CommandLineParser parser =
            new CommandLineParser.CommandLineParser();
        /* define argument -c (resp. --color) that accepts values red, green or blue */
        EnumeratedValueArgument<string> colorArgument = new EnumeratedValueArgument<string>(
            'c', "color", "Desired color", new string[] {"red", "green", "blue"});
        /* add the argument to the set of accepted arguments */
        parser.Arguments.Add(colorArgument);
        try
        {
            /* parse command line arguments */
            parser.ParseCommandLine(args);
            /* this prints results to the console */
            parser.ShowParsedArguments();
        }
        catch (CommandLineArgumentOutOfRangeException e)
        {
            //argument was out of range
            Console.WriteLine(e.Message);
            parser.ShowUsage();
        }
        catch (CommandLineException e)
        {
            Console.WriteLine(e.Message);
            parser.ShowUsage();
        }
        //...
        /* work with the value of the argument */
        Draw(colorArgument.Value);
    }

    public static void Draw(string color)
    {
        // draw something
    }
```

## BoundedValueArgument

`BoundedValueArgument` is similar to `ValueArgument` and `EnumeratedValueArgument`. You can use any comparable type as `TValue` (type implementing `IComparable`) and define `minimum` and `maximum` allowed value of the argument. 

A full example of `BoundedValueArgument`

```csharp
    public static void Main(string[] args)
    {
        /* create the parser class */
        CommandLineParser.CommandLineParser parser =
            new CommandLineParser.CommandLineParser();
        /* define argument with allowed values between 0 and 10 */
        BoundedValueArgument<int> recursionDepthArgument =
            new BoundedValueArgument<int>('r', "recursion", "Depth of recursion.");
        recursionDepthArgument.MinValue = 0;
        recursionDepthArgument.MaxValue = 10;
        /* add the argument to the set of accepted arguments */
        parser.Arguments.Add(recursionDepthArgument);
        try
        {
            /* parse command line arguments */
            parser.ParseCommandLine(args);
            /* this prints results to the console */
            parser.ShowParsedArguments();
        }
        catch (CommandLineArgumentOutOfRangeException e)
        {
            //argument was out of range
            Console.WriteLine(e.Message);
            parser.ShowUsage();
        }
        catch (CommandLineException e)
        {
            //some other error
            Console.WriteLine(e.Message);
            parser.ShowUsage();
        }
        //...
        if (recursionDepthArgument.Parsed)
        {
            // use the recursionDepthArgument.Value here
        }
    }
```

## FileArgument

`FileArgument` can be used to define input and output files for the program. The value of the argument is of type `FileInfo` and via property `FileArgument.FileMustExist` you can declare whether the file must already exist (useful for input files) or not. 

## DirectoryArgument

`DirectoryArgument` can be used to specify directory in the file system. The value of the argument is of type `DirectoryInfo` and via property `DirectoryArgumet.DirectoryMustExist` you can declare whether the file must already exist or not. 

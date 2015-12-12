Here is an example of using CommandLine Parser Library. The first example uses the programative approach - defining the arguments explicitly, in a method body. 

```csharp
//example class used as a type of a ValueArgument
class Point
{
    public int x;
    public int y;

    public override string ToString()
    {
        return String.Format("Point[{0};{1}]", x, y);
    }
}

// args contains commandl line arguments
static void Main(string[] args)
{
    /* create the CommandLineParser instance */
    CommandLineParser.CommandLineParser parser = new CommandLineParser.CommandLineParser();

    /* now define several arguments of various types */

    /* SwitchArguments - for on/off boolean logic */
    SwitchArgument showArgument = new SwitchArgument(
        's', "show", "Set whether show or not", true);

    SwitchArgument hideArgument = new SwitchArgument(
        'h', "hide", "Set whether hid or not", false);

    /* ValueArguments - used like this: --level easy --version 1.3 */
    ValueArgument<string> level = new ValueArgument<string>(
        'l', "level", "Set the level");

    ValueArgument<decimal> version = new ValueArgument<decimal>(
        'v', "version", "Set desired version");

    /* ValueArguments can be used with your own objects, in this cas like: --point [3;24] 
     * see assignment of point.ConvertValueHandler below 
     */
    ValueArgument<Point> point = new ValueArgument<Point>(
        'p', "point", "specify the point");

    /* BoundedValueArgument checks the value belongs to an interval => -o 4 would throw an error */
    BoundedValueArgument<int> optimization = new BoundedValueArgument<int>(
        'o', "optimization", 0, 3);

    /* EnumeratedValueArgument allows only some values => --color yellow would throw an error*/
    EnumeratedValueArgument<string> color = new EnumeratedValueArgument<string>(
        'c', "color", new string[] { "red", "green", "blue" });

    /* FileArgument has standard .NET FileInfo class as a value, it can be used when 
     * the progrem processes input files */
    FileArgument inputFile = new FileArgument('i', "input", "Input file");
    /* input file must exist */
    inputFile.FileMustExist = true; 

    /* FileArgument can also be used for output files, in this case you will want to 
     * set FileMustExist flag to false */
    FileArgument outputFile = new FileArgument('x', "output", "Output file");
    /* output file does not have to exist*/
    outputFile.FileMustExist = false;

    /* DirectoryArgument is simalar to FileArgument*/
    DirectoryArgument inputDirectory = new DirectoryArgument('d', "directory", "Input directory");
    inputDirectory.DirectoryMustExist = true;

    // define conversion from string to Point type, which is one of the ways to
    // support custom class ValueParameter
    point.ConvertValueHandler = delegate(string stringValue)
    {
        if (stringValue.StartsWith("[") && stringValue.EndsWith("]"))
        {
            string[] parts =
                stringValue.Substring(1, stringValue.Length - 2).Split(';', ',');
            Point p = new Point();
            p.x = int.Parse(parts[0]);
            p.y = int.Parse(parts[1]);
            return p;
        }
        else
            throw new CommandLineArgumentException("Bad point format", "point");
    };

    //add the paramters to parser
    parser.Arguments.Add(showArgument);
    parser.Arguments.Add(hideArgument);
    parser.Arguments.Add(level);
    parser.Arguments.Add(version);
    parser.Arguments.Add(point);
    parser.Arguments.Add(optimization);
    parser.Arguments.Add(color);
    parser.Arguments.Add(inputFile);
    parser.Arguments.Add(outputFile);
    parser.Arguments.Add(inputDirectory);

    try
    {
        /* parse the command line */
        parser.ParseCommandLine(args);
        /* show parsed values for debugging purposes */
        parser.ShowParsedArguments();

        // now you can work with the arguments ...

        // if (level.Parsed) ... test, whether the argument appeared on the command line
        // {
        //     level.Value ... contains value of the level argument
        // }
        // if (showArgument.Value) ... test the switch argument value
        //     ...
    }
    /* CommandLineException is thrown when there is an error during parsing */
    catch (CommandLineException e)
    {
        Console.WriteLine(e.Message);
        /* you can help the user by printing all the possible arguments and their
         * description, CommandLineParser class can do this for you.
         */
        parser.ShowUsage();
    }
}
```

An alternative to programmative way is the declarative way using attributes:

```csharp
/* arguments are defined using attributes, parsed values will be mapped to fields or properties*/
class ParsingTarget
{
    /* SwitchArguments - for on/off boolean logic */
    [SwitchArgument('s', "show", true, Description = "Set whether show or not")]
    public bool show;

    private bool hide;
    [SwitchArgument('h', "hide", false, Description = "Set whether hide or not")]
    public bool Hide
    {
        get { return hide; }
        set { hide = value; }
    }

    /* ValueArguments - used like this: --level easy --version 1.3 */
    [ValueArgument(typeof(decimal), 'v', "version", Description = "Set desired version")]
    public decimal version;

    [ValueArgument(typeof(string), 'l', "level", Description = "Set the level")]
    public string level;

    /* BoundedValueArgument checks the value belongs to an interval => -o 4 would throw an error */
    [BoundedValueArgument(typeof(int), 'o', "optimization",
        MinValue = 0, MaxValue = 3, Description = "Level of optimization")]
    public int optimization;

    /* EnumeratedValueArgument allows only some values => --color yellow would throw an error */
    [EnumeratedValueArgument(typeof(string), 'c', "color", AllowedValues = "red;green;blue")]
    public string color;

    /* FileArgument has standard .NET FileInfo class as a value, it can be used when 
     * the progrem processes input files */
    [FileArgument('i', "input", Description = "Input file", FileMustExist = true)]
    public FileInfo inputFile;

    /* FileArgument can also be used for output files, in this case you will want to 
     * set FileMustExist flag to false */
    [FileArgument('x', "output", Description = "Output file", FileMustExist = false)]
    public FileInfo outputFile;

    /* DirectoryArgument is simalar to FileArgument*/
    [DirectoryArgument('d', "directory", Description = "Input directory", DirectoryMustExist = false)]
    public DirectoryInfo InputDirectory;
}

/* args contains command line arguments */
static void Main(string[] args)
{
    /* create the CommandLineParser instance */
    CommandLineParser.CommandLineParser parser = new CommandLineParser.CommandLineParser();
    ParsingTarget p = new ParsingTarget();
    /* read the argument attributes from the class definition */
    parser.ExtractArgumentAttributes(p);

    try
    {
        /* parse the command line */
        parser.ParseCommandLine(args);
        /* show parsed values for debugging purposes */
        parser.ShowParsedArguments();

        /* 
         * now you can work with the arguments, values are mapped to properties of 
         * ParsingTarget object ...
         */

        // if (p.Hide)
        //{
        //   Hide();
        //}
    }
    /* CommandLineException is thrown when there is an error during parsing */
    catch (CommandLineException e)
    {
        Console.WriteLine(e.Message);
        /* 
         * you can help the user by printing all the possible arguments and their
         * description, CommandLineParser class can do this for you.
         */
        parser.ShowUsage();
    }
}
```
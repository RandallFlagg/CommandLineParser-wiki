You may need more precise settings for the usage of arguments. Every argument can be marked Optional/Mandatory using the `Optional` field. Furthermore, you may set conditions for whole groups of arguments. 

You can specify that 
 * only one of the arguments from a set of arguments must be used
 * exactly one of the arguments from a set of arguments must be used
 * at least one of the arguments from a set of arguments must be used
 * arguments from one set cannot be used together with arguments from another set

Here is an example (uses declarative syntax - see Declarative argument syntax (attributes):

```csharp
/*
 * example of using argument certification - this example uses declarative 
 * certification syntax. You can also add certifications to the parser using
 * CommandLineParser.Certifications collection
 */
// exactly one of the arguments x, o, c must be used
[ArgumentGroupCertification("x,o,c", EArgumentGroupCondition.ExactlyOneUsed)]
// only one of the arguments f, u must be used
[ArgumentGroupCertification("f,u", EArgumentGroupCondition.OneOreNoneUsed)]
// arguments j and k can not be used together with arguments l or m
[DistinctGroupsCertification("j,k","l,m")]
public class Archiver
{
    [ValueArgument(typeof(string), 'f', "file", Description="Input from file")]
    public string InputFromFile;

    [ValueArgument(typeof(string), 'u', "url", Description = "Input from url")]
    public string InputFromUrl;

    [ValueArgument(typeof(string), 'c', "create", Description = "Create archive")]
    public string CreateArchive;

    [ValueArgument(typeof(string), 'x', "extract", Description = "Extract archive")]
    public string ExtractArchive;

    [ValueArgument(typeof(string), 'o', "open", Description = "Open archive")]
    public string OpenArchive;

    [SwitchArgument('j', "g1a1", true)]
    public bool group1Arg1;

    [SwitchArgument('k', "g1a2", true)]
    public bool group1Arg2;

    [SwitchArgument('l', "g2a1", true)]
    public bool group2Arg1;

    [SwitchArgument('m', "g2a2", true)]
    public bool group2Arg2;
}

static void Main(string[] args)
{
    CommandLineParser.CommandLineParser parser = new CommandLineParser.CommandLineParser();
    //create instance of a manipulated object
    ParsingTarget p = new ParsingTarget();
    //look for the bound fields in the object p
    parser.ExtractArgumentAttributes(p);

    try
    {
        //parse the commandline and show the results
        parser.ParseCommandLine(args);
        parser.ShowParsedArguments();

        //work with the p object here, its values 
        //are set to the values found on the command line
    }
    catch (CommandLineException e)
    {
        Console.WriteLine(e.Message);    
    }
}
```
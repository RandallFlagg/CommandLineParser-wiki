The set of provided argument is quite rich and should cover most of the usual use cases, but you are free to create your own types of arguments. 

You have to derive from `Argument` class or one of its provided subclasses. Consider deriving from `CertifiedValueArgument` which is useful for arguments with value that have check some requirements on the correct value than the value being of a certain type. 

You will probably want to override `Argument.Parse` method (you will have to if you derive from Argument class directly). Parse method gets as an input ofall the command line arguments and an index to the argument. It can then process the command line and should move the index to the next argument before terminating. 

To support declarative syntax (using attributes), you need to create an accompanying Attribute class. You should derive your accompanying attribute from `ArgumentAttribute`. Take a look at one of the existing Attributes to see how to do that. 

Here is an example of how to use CertifiedValueArgument for defining custom argument type (which validates an argument using a regular expression). 

```csharp
using System.Text.RegularExpressions;
using CommandLineParser.Exceptions;

namespace CommandLineParser.Arguments
{
    /// <summary>
    /// Regex argument accepts values that match regular expression pattern.
    /// </summary>
    public class RegexCertifiedArgument : CertifiedValueArgument<string>
    {
        private Regex regex;

        public Regex Regex
        {
            get { return regex; }
            set { regex = value; }
        }

        #region constructor
        public RegexCertifiedArgument(char shortName, Regex regex)
            : base(shortName)
        {
            this.regex = regex;
        }

        public RegexCertifiedArgument(char shortName, string longName, Regex regex)
            : base(shortName, longName)
        {
            this.regex = regex;
        }

        public RegexCertifiedArgument(char shortName, string longName, string description, Regex regex)
            : base(shortName, longName, description)
        {
            this.regex = regex;
        }
        #endregion

        internal override void Certify(string value)
        {
            // override the Certify method to validate value against regex
            if (regex != null)
            {
                if (!regex.IsMatch(value))
                {
                    throw new CommandLineArgumentOutOfRangeException(
                        "Argument does not match the regex pattern.", Name);
                }
            }
        }
    
        // Parse implementation inherited from ValueArgument is fine for RegexCertifiedArgument too
    }
}
```
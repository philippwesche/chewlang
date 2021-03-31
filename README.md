**Chew** or **chewlang** is a high-level programming language and code generator with heavy emphasis on regular expressions and anticipated use in data scraping.

It currently compiles down to Perl5.

Design priorities:
* Short, readable code
* Ease of learning (esp. being incrementally useful)
* Avoidance of non-alphanumeric (modal) characters when reasonably possible
* Modelled after natural English usage
* Experimental introduction of "contexts" to replace/unify flow control and parameters

# Online demo
A quick live demo can be found at http://chewlang.com

# Dependencies
* Perl 5 (earlier versions untested)
* Getopt::Long
* LWP >=5.64 (if reading from http(s))

# Syntax principles
## Gerund sets up a context
In Chew, the gerund form of a verb is used to indicate that further instructions follow that should be processed in the context of the first command, or that modify its intended meaning. This is similar to the use of a semicolon in Python, or curly brackets in languages styled after C (C++, Perl, Java). One noteworthy example is *Take* versus *Taking*, where the latter allows and enforces further processing.

## Indenting
Commands with global effect are specified without indent (currently Reading, Starting, Stopping, Skipping, Hand-over, Output, Outputting). For other commands, you would start in the base column and then use a consistent indenting pattern thereafter. Basically the same as Python.

## Variable naming
While Chew is very permissive with respect to variable naming, it's recommended to use variable names that are generally considered sensible, so CamelCase or underscore\_joined, starting with English alphabet letters and optionally containing numbers. Following this practice will pretty much guarantee that your variable naming is future proof.

# Syntax
## Obligatory statements
### Reading input
* Reading *filename*
  * This tells chew which file to open. You may specify an http or https URL instead (experimental, dependencies may change). The Reading statement must be the first line of a chew file. *filename* can include ranges in the form *{{[first number]..[second number]}}*. Ranges currently must be numeric and ascending.

### Generating output
* Output *format*
  * Obligatory statement to specify output format. The first supported format is "tsv+header". The Output statement must be the last line of a chew file.

## Defining a processing area
* Starting at /*expression*/
  * Can be used to indicate that nothing prior to a line matching the *Starting at* expression should be processed. The *expression* should be a regular expression.

* Stopping at /*expression*/
  * Can be used to indicate that nothing on or after a line matching the *Stopping at* expression should be processed. The *expression* should be a regular expression.

* Skipping *number* at /*expression*/
  * Can be used to indicate that processing should "take a break" for ("skip") *number* lines when *expression* is encountered. The *expression* should be a regular expression.

The three above instructions should be used after *Reading* and before beginning the rest of your processing.

## Splitting and "taking"
* Splitting at /*expression*/
  * This divides a line into chunks using *expression* as a divider. Splitting must be followed by one or several *Take* statements.

* Take *number* as *outputname*
  * This preserves the chunk indicated by *number* (starting at 0 for the first chunk, such as the beginning of a line). In output, the chunk will then appear in a column whose heading is *outputname*.

## The Locating context
The locating context allows accessing the lines around a matching line. It is similar in spirit to the "Case" construct known from other programming languages.

* Locating around /*expression*/
  * Defines where the origin of the environment should be set.

* At *number*
  * Selects a line for further processing relative to the origin. "At 0" is the same line as the origin; negative numbers can be used to select lines before the origin, positive numbers after it. "At *number*" in itself does nothing. It requires further statements such as Splitting, to determine the action to be taken.

## Matching a pattern
* Capture /*expression*/ as *outputname*
  * Rather than relying on a segmentation of the source document, Capture directly catches a part of the document that conforms to the pattern defined in *expression*. As a current limitation, it only catches the first match on any given line.

## Coping with bad input
As HTML in particular is predominantly line break agnostic, some websites are delivered as a single line of code. We can remedy this via:

* Reframe [/*replacement expression*/]
  * Reframe on its own cuts at every '><' border; with a replacement regex of the format /*where you find this*/*replace with this*/, it becomes more versatile. If the replacement expression contains one or several newline characters, the file will be broken into lines at those points.

When it's needed, reframing is one of the first things you'll want to instruct Chew to do when you're starting to write a script, and so output from the reframe action will be printed when your *Reframe* line is the last line in your file, to help you check your working.

## Clean output
Output data cleaning can also be performed in Chew:

* Clean up /*expression*/
  * This can be used in a *Taking* context to clean up the captured data, more specifically to remove excess characters. Cleaning is applied "globally" by default, not just once.

* Cleaning up /*expression*/
  * When used as a context, *Cleaning up* takes an indented *Replace with* command on the next line:

* Replace with *string*

* Drop rows where *fieldname* matches /*expression*/
  * In contrast with Clean, which is applied immediately during data capture, and takes effect on only one field, *Drop rows* is applied just prior to output and drops the entire row across all data fields. To those coming from SQL, the statement will look quite familiar.

## Following links to more data
Sometimes, links that you need to follow to get all of your data may have arbitrary tokens rather than falling in a numeric range. You will usually be able to obtain these links from the first, and/or subsequent pages in the range. In a *Splitting* context, rather than using the *Take* statement to use an extract as a data point, you can interpret the extract as a link to be followed:

* Follow *number*
  * This appends the extract at *number* to the list of files (pages) to be read and processed.

## Advanced topic: Integration with Perl code
Chew can act as a superset of sorts for Perl:

* Hand-over
  * Code after this marker will be assumed to be Perl. So you first collect the data using Chew and then continue writing Perl in the same file. Do not write any Chew code after issuing Hand-over.

Your Perl code can access the captured data via the hash of arrays, *%capturedData*, in which each column is accessed under the field name assigned at capture. So if you captured using *Take [number] as foo*, you would access the column using *@{ $capturedData{foo} }* in Perl. You can also access *@fieldNames*, which contains the field names in the order originally specified, *@fileList*, which contains a list of the accessed files and URLs, and *$outputRowCount*, which gives the number of rows in the longest data column (appropriate warnings about uneven data columns are given by default). All other variables and functions have been masked by adding "chew" into the variable name. You will not usually want to access them. By masking them in this way, the potential for variable name collisions is minimised.

**There are some rules for this to work.** For better readability, functions that Chew needs will be placed *after* your own Perl code in the compiled file, along with the call to print warnings. This only works if the Perl code you're adding does not interfere with allowing these functions to be read by Perl.

# Current status of feature creep
14/20 completed approximately

[//]: # (&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;)


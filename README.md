**Chew** or **chewlang** is intended to be a high-level programming language and code generator with heavy emphasis on regular expressions and anticipated use in data scraping.

It currently compiles down to Perl5.

Design priorities:
* Short, readable code
* Ease of learning (esp. being incrementally useful)
* Modelled after natural English usage
* Experimental introduction of "contexts" to replace/unify control flow and parameters

# A few essential commands ("verbs")
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*NB This is an introductory list. More than shown here is currently supported and will be added with documentation and examples in due course. However, this selection should serve to illustrate how chew can do a lot with a few short commands.*

* Reading *filename*
  * This tells chew which file to open. You may specify an http or https URL instead (experimental, dependencies may change).

* Starting at /*expression*/
* Stopping at /*expression*/
  * Can be used to indicate that nothing prior to a line matching the *Starting at* expression and nothing after a line matching the *Stopping at* expression should be processed. The *expression* should be a regular expression.

* Matching /*expression*/
  * Only lines matching *expression* should be processed.

* Cut at /*expression*/
  * This divides a line into chunks using *expression* as a divider. Cut must be followed by one or several *Take* statements.

* Take *number* as *outputname*
  * This preserves the chunk indicated by *number* (starting at 0 for the first chunk, such as the beginning of a line). In output, the chunk will then appear in a column whose heading is *outputname*.

[//]: # (&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;)

# Dependencies
* Perl 5 (earlier versions untested)
* Getopt::Long
* LWP >=5.64

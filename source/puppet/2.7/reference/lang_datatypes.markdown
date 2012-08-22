---
layout: default
title: "Language: Data Types"
---


[attribute]: ./lang_resources.html#syntax
[function]: ./lang_functions.html
[variables]: ./lang_variables.html
[if]: ./lang_conditional.html#if-statements
[comparison]: ./lang_expressions.html#comparison-operators
[stdlib]: http://forge.puppetlabs.com/puppetlabs/stdlib
[facts]: ./lang_variables.html#facts
[reserved]: ./lang_reserved.html#reserved-words
[attribute_override]: ./lang_resources.html#adding-or-modifying-attributes
[resourcedefault]: ./lang_defaults.html
[node_def]: ./lang_node_definitions.html
[relationship]: ./lang_relationships.html
[chaining]: ./lang_relationships.html#chaining-arrows

The Puppet language allows several data types as [variables][], [attribute][] values, and [function][] arguments:

Booleans
-----

The boolean type has two possible values: `true` and `false`. Literal booleans must be one of these two bare words (that is, not quoted). 

The condition of an ["if" statement][if] is a boolean value. All of Puppet's [comparison expressions][comparison] return boolean values, as do many [functions][function]. 

### Automatic Conversion to Boolean

If a non-boolean value is used where a boolean is required, it will be automatically converted to a boolean as follows:

Strings
: Empty strings are false; all other strings are true. That means the string `"false"` actually resolves as true. **Warning: all [facts][] are strings in this version of Puppet, so "boolean" facts must be handled carefully.**

  > Note: the [puppetlabs-stdlib][stdlib] module includes a `str2bool` function which converts strings to boolean values more intelligently. 

Numbers
: All numbers are true, including zero and negative numbers. 

  > Note: the [puppetlabs-stdlib][stdlib] module includes a `num2bool` function which converts numbers to boolean values more intelligently. 

Undef
: The special data type `undef` is false.

Arrays and Hashes
: Any array or hash is true, including the empty array and empty hash.

Resource References
: Any resource reference is true, regardless of whether or not the resource it refers to has been evaluated, whether the resource exists, or whether the type is valid.

Regular expressions cannot be converted to boolean values.

* * *

Undef
-----

Puppet's special undef value is roughly equivalent to nil in Ruby; variables which have never been declared have a value of `undef`. Literal undef values must be the bare word `undef`.

The undef value is usually useful for testing whether a variable has been set. It can also be used as the value of a resource attribute, which can let you un-set any value inherited from a [resource default][resourcedefault] and cause the attribute to be unmanaged. 

When used as a boolean, `undef` is false.

* * *

Strings
-----

Strings are unstructured text fragments of any length. They may or may not be surrounded by quotation marks. Use single quotes for all strings that do not require variable interpolation, and double quotes for strings that do require variable interpolation.

### Bare Words

Bare (that is, not quoted) words are usually treated as single-word strings. To be treated as a string, a bare word must:

* Not be a [reserved word][reserved]
* Begin with a letter, and contain only letters, digits, hyphens (-), and underscores (_).

Bare word strings are usually used with attributes that accept a limited number of one-word values, such as `ensure`.

### Single-Quoted Strings

Strings surrounded by single quotes `'like this'` do not interpolate variables, and the only escape sequences permitted are `\'` (a literal single quote) and `\\` (a literal backslash).

Lone backslashes are literal backslashes, unless followed by a single quote or another backslash. That is:

* When a backslash occurs at the very end of a single-quoted string, a double backslash must be used instead of a single backslash. For example: `path => 'C:\Program Files(x86)\\'`
* When a literal double backslash is intended, a quadruple backslash must be used.

### Double-Quoted Strings 

Strings surrounded by double quotes `"like this"` allow variable interpolation and several escape sequences. 

#### Variable Interpolation

Any `$variable` in a double-quoted string will be replaced with its value. To remove ambiguity about which text is part of the variable name, you can surround the variable name in curly braces:

{% highlight ruby %}
    path => "${apache::root}/${apache::vhostdir}/${name}",
{% endhighlight %}
    
#### Escape Sequences

The following escape sequences are available:

* `\$` --- literal dollar sign
* `\"` --- literal double quote
* `\\` --- single backslash
* `\n` --- newline
* `\r` --- carriage return
* `\t` --- tab

* * *

Resource References
-----

Resource references identify a specific existing Puppet resource by its type and title. Several attributes, such as the [relationship][] metaparameters, require resource references.

{% highlight ruby %}
    # A reference to a file resource:
    subscribe => File['/etc/ntp.conf'],
    ...
    # A type with a multi-segment name:
    before => Concat::Fragment['apache_port_header'],
{% endhighlight %}

The general form of a resource reference is: 

* The resource **type,** capitalized (every segment must be capitalized if the type includes a namespace separator \[`::`\])
* An opening square bracket
* The **title** of the resource, or a comma-separated list of titles
* A closing square bracket

Unlike variables, resource references are not parse-order dependent, and can be used before the resource itself is declared. 

### Multi-Resource References

Resource references with an **array of titles** or **comma-separated list of titles** refer to multiple resources of the same type:

{% highlight ruby %}
    # A multi-resource reference:
    require => File['/etc/apache2/httpd.conf', '/etc/apache2/magic', '/etc/apache2/mime.types'],
    # An equivalent multi-resource reference: 
    $my_files = ['/etc/apache2/httpd.conf', '/etc/apache2/magic', '/etc/apache2/mime.types']
    require => File[$my_files]
{% endhighlight %}

They can be used wherever an array of references might be used. They can also go on either side of a [chaining arrow][chaining] or receive a [block of additional attributes][attribute_override].


* * *

Numbers
-----

Puppet's arithmetic expressions accept integers and floating point numbers. Internally, Puppet treats numbers like strings until they are used in a numeric context.

Numbers can be written as bare words or quoted strings, and may consist only of digits and an optional sign and decimal point. 

{% highlight ruby %}
    $some_number = 8 * -7.992
    $another_number = $some_number / 4
{% endhighlight %}

* * *

Arrays
-----

Arrays are written as comma-separated lists of items surrounded by square brackets:

{% highlight ruby %}
    [ 'one', 'two', 'three' ]
{% endhighlight %}

The items in an array can be any data type, including hashes or more arrays.

Resource attributes which can optionally accept multiple values (including the relationship metaparameters) expect those values in an array.

You can access items in an array by their numerical index (counting from zero). Square brackets are used for indexing. 

Example:

{% highlight ruby %}
    $foo = [ 'one', 'two', 'three' ]
    notice( $foo[1] )
{% endhighlight %}

This manifest would log `two` as a notice. (`$foo[0]` would be `one`, since indexing counts from zero.)

Nested arrays and hashes can be accessed by chaining indexes:

{% highlight ruby %}
    $foo = [ 'one', {'second' => 'two', 'third' => 'three'} ]
    notice( $foo[1]['third'] )
{% endhighlight %}

This manifest would log `three` as a notice. (`$foo[1]` is a hash, and we access a key named `'third'`.)

Arrays support negative indexing, with `-1` being the final element of the array:

{% highlight ruby %}
    $foo = [ 'one', 'two', 'three', 'four', 'five' ]
    notice( $foo[2] )
    notice( $foo[-2] )
{% endhighlight %}

The first notice would log `three`, and the second would log `four`.

The [puppetlabs-stdlib][stdlib] module contains several additional functions for dealing with arrays, including: 

* `delete`
* `delete_at`
* `flatten`
* `grep`
* `hash`
* `is_array`
* `join`
* `member`
* `prefix`
* `range`
* `reverse`
* `shuffle`
* `size`
* `sort`
* `unique`
* `validate_array`
* `values_at`
* `zip`


* * * 

Hashes
-----

Hashes are written as key/value pairs surrounded by curly braces; a key is separated from its value by a `=>` (arrow, fat comma, or hash rocket), and adjacent pairs are separated by commas. 

{% highlight ruby %}
    { key1 => 'val1', key2 => 'val2' }
{% endhighlight %}

Hash keys are strings, but hash values can be any data type, including arrays or more hashes.

You can access hash members with their key; square brackets are used for indexing. Nested arrays and hashes can be accessed by chaining indexes. 

Example:

{% highlight ruby %}
    $myhash = { key => { subkey => 'b' }}
    notice( $myhash[key][subkey] )
{% endhighlight %}

This example manifest would log `b` as a notice.

The [puppetlabs-stdlib][stdlib] module contains several additional functions for dealing with hashes, including: 

* `has_key`
* `is_hash`
* `keys`
* `merge`
* `validate_hash`
* `values`


* * * 

Regular Expressions
-----

Regular expressions (regexes) are Puppet's one **non-standard** data type. They cannot be assigned to variables, and they can only be used in the few places that specifically accept regular expressions. These places include: the `=~` and `!~` regex match operators, the cases in selectors and case statements, and the names of [node definitions][node_def]. They cannot be passed to functions or used in resource attributes.

Regular expressions are written as [standard Ruby regular expressions](http://www.ruby-doc.org/core/Regexp.html) (valid for the version of Ruby being used by Puppet) and must be surrounded by forward slashes:

{% highlight ruby %}
    if $host =~ /^www(\d+)\./ {
      notify { "Welcome web server #$1": }
    }
{% endhighlight %}

Alternate forms of regex quoting are not allowed.

Regexes in Puppet cannot have options or encodings appended after the final slash. However, you may turn options on or off for portions of the expression using the `(?<ENABLED OPTION>:<SUBPATTERN>)` and `(?-<DISABLED OPTION>:<SUBPATTERN>)` notation. The following example enables the `i` option while disabling the `m` and `x` options:

{% highlight ruby %}
     $packages = $operatingsystem ? {
       /(?i-mx:ubuntu|debian)/        => 'apache2',
       /(?i-mx:centos|fedora|redhat)/ => 'httpd',
     }
{% endhighlight %}

The following options are allowed: 

* i --- Ignore case
* m --- Treat a newline as a character matched by `.`
* x --- Ignore whitespace and comments in the pattern

# Powershell

## Basics

Self-evaluating

```powershell
> 1
1
> "bla"
bla
> 1 + 2
3
```

### A case for insensitivity

In general powershell is case insensitive (variable names, commands, functions, string comparisons).

### Comments
Line comment
```powershell
> # comment
```

Block comment
```powershell
> 2 * <# 2 #> 3
```

### Seek aid
```powershell
> get-help write-output
# prints syntax
> get-help write-output -online
# browser opens
```

### Variables and assignment

Variables are prefixed by `$` and are not be declared. Variable names are case insensitive.
```powershell
> $foo = "bar"
> $FOO
bar
> $foo = 1
> $foo
1
```

Undefined variables are not an error (they evaluate to $null)
```powershell
> $does_not_exist
> $does_not_exist -eq $null
True
> $does_not_exist + 2
2
> $does_not_exist * 2
```

Assignments are chainable
```powershell
> $x = $y = 2
> $x
2
```

A selection of pre-defined variables
```powershell
> $null
> $true
True
> $false
False
> $PWD
> $args     # in scripts
arg1
arg2
```


### Strings

```powershell
> "a"
a
> 'b'
b
> "a" + "b"
ab
```

Double quoted strings with interpolation
```powershell
> "two=$(1+1)"
two=2
> "Home directory: $($Home)"
Home directory: /Users/moebius
```

Single quoted strings are not interpolated
```powershell
> '$(1+2)'
$(1+2)
```

### Arrays

Array literals
```powershell
> @("foo", "bar")
foo
bar
> "foo", "bar"
foo
bar
> $empty_array = @()
```

Concatenation
```powershell
> @("foo") + "bar"
foo
bar
> @("foo") + @("bar")
foo
bar
> $x = @("a","b")
> $x += "c"
> $x
a
b
c
```

### Operators

Equality
```powershell
> "x" -eq "x"       # NOT "x" == "y"
True
> "x" -ne "y"
True
> 1 -eq "1"
True                # Hmm...
> "" -eq 0          # What about this...?
False               # Makes sense!
> 0 -eq ""          # This can't be?
True                # WTF?!
> "" -ne 0
True                # Phew :)
> 0 -ne ""
False               # Rogntudju #$@! AARGH!!!
```

Relational

```powershell
-lt, -le, -gt, -ge
```

Algebraic operators and real number gotchas
```powershell
> 1 + 1
2
> 3 * 0.5
1,5
> 3 / 2             # Look out!
1,5
> [int](3 / 2)
2                   # Rounded, NOT truncated!
> [Math]::Floor(3/2)
1                   # Ahh!
```

## .NET

It's all .NET: https://docs.microsoft.com/en-us/dotnet/api/?view=netframework-4.7.1

```powershell
> "x".GetType().Name
String
> "x" -is [System.String]
True
> $true.GetType().Name
Boolean
> @() -is [System.Collections.ICollection]
False
```

## Control structures

if
```powershell
# Parens around condition and block are not optional
> if ($true) { "indeed" }
indeed
> if ($false) { "wtf!" } else { "false, indeed" }
false, indeed
```

while
```powershell
# Parens around condition and block are not optional
> if ($true) { "indeed" }
indeed
> if ($false) { "wtf!" } else { "false, indeed" }
false, indeed
```

## More strings

Multi-line strings
```powershell
$longString = @"
Truthy : $($true)
Falsy  : $($false) 
"@
```

String matching is case insensitive by default.

```powershell
> 'Ab' -eq 'aB'
True
```

Add a `c` before an operator to make it case sensitive, e.g `-ceq`.
```powershell
> 'Ab' -ceq 'ab'
False
```

Globs

```powershell
> 'abc' -like '*b*'
True
> 'abc' -like '*b'
False
```

Regex matching

```powershell
> 'foo=bar' -match '(\w+)=(\w+)' 
True
> $Matches[1]
foo
> $Matches[2]
bar
```

Match and replace
```powershell
> 'abba' -replace 'b','*'
a**a
> 'foo,bar' -replace '(\w+),(\w+)','$2,$1'
bar,foo
```


Subscripting
```powershell
> 'ABCDE'[2]
C
> 'ABCDE'[-1]
E
> 'ABCDE'[0,4]
A
E
> 'ABCDE'[1..3]
B
C
D
> 'ABCDE'[1..3] -join ''
BCD
> 'ABCDE'.SubString(1,3)
BCD
```

Misc
```powershell
> "foo" * 3
foofoofoo
> "Blue Öyster Cult".ToLower()
blue öyster cult
```

## Files and directories

Write a file

```powershell
> Set-Content test.ps1 '$args'
> ./test.ps1 hello
hello
```

Read a file
```powershell
> Get-Content test.ps1
$args
```

Better be explicit about character encoding or you'll be sorry
```powershell
> Set-Content -Encoding 'UTF8' 'multilingual.txt' 'HellÃ¶, wÃ¤rlden!'
> Get-Content -Encoding 'UTF8' 'multilingual.txt'
```

Current directory
```powershell
> pwd
Path
----
/Users/moebius
> $PWD
Path
----
/Users/moebius
> $PWD.Path
/Users/moebius
```

Get file/directory information
```powershell
> $f = Get-Item test.txt
> $f.Length
18
> $f.FullName
/Users/moebius/test.txt
```

List files
```powershell
> Get-ChildItem
    Directory: /Users/moebius


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
------       2017-11-28     22:06             18 multilingual.txt
------       2017-11-28     22:03           4012 README.md
------       2017-11-28     21:58             18 test.txt
> Get-ChildItem *.txt | ForEach {$_.Name}
multilingual.txt
test.txt
> Get-ChildItem *.txt -Recurse | ForEach { "Found $($_.Name) in $($_.Directory.Fullname)" }
Found multilingual.txt in /Users/moebius
Found test.txt in /Users/moebius
> Get-ChildItem -Path $Home -Filter *.txt -Recurse
...
```

Create a directory
```powershell
> New-Item -Path newdir -ItemType directory
    
    Directory: /Users/moebius


Mode                LastWriteTime     Length Name
----                -------------     ------ ----
d----        2017-11-29     09:05            newdir
```

Resolve paths
```powershell
> Resolve-Path .
   
Path
----
/Users/moebius

> Resolve-Path '*.md'
Path
----
/Users/moebius/README.md

> Resolve-Path '*.md' -Relative
Path
----
./README.md

> Resolve-Path does_not_exist
Resolve-Path : Cannot find path '/Users/moebius/does_not_exist' because it does not exist.
```

Combine paths
```powershell
> $base = Resolve-Path .
> Join-Path $base newdir
/Users/moebius/newdir
> Join-Path $base does_not_exist
/Users/moebius/does_not_exist
> Join-Path . newdir -Resolve
/Users/moebius/newdir
> Join-Path . does_not_exist -Resolve
Join-Path : Cannot find path '/Users/moebius/does_not_exist' because it does not exist.
```

Taking paths apart
```powershell
> $base = Resolve-Path newdir
> $base.Path
/Users/moebius/newdir
> Split-Path $base
/Users/moebius
> Split-Path $base -Leaf
newdir
> Split-Path . -IsAbsolute
False
> [System.IO.Path]::IsPathRooted('.')
False
> Split-Path $Home -IsAbsolute
True
> [System.IO.Path]::IsPathRooted($Home)
```

## Functions

Simple function without parameters
```powershell
function Get-Foo
{
    "foo"
}
```

Simple but bad function parameters
```powershell
function Get-First-Arg
{
    $args[0]
}

> Get-First-Arg 1
1
```

Better function parameters
```powershell
function Get-First-Arg
{
    param([string]$arg)
    # use return to be explicit or exit early
    return $arg
}

> Get-First-Arg 1
1
> Get-First-Arg -arg 1
1
> Get-First-Arg -foo 1
Get-First-Arg : A parameter cannot be found that matches parameter name 'foo'.
```

Even better function parameters
```powershell
function Get-First-Arg
{
    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true)]
        [string]$arg,
        [switch]$flag = $false
    )
    if ($flag)
    {
        return "$($arg) !"
    }
    return $arg
}

> Get-First-Arg -flag
cmdlet Get-First-Arg at command pipeline position 1
Supply values for the following parameters:
arg: Good
Good!
```


Declare return type for documentation and better aid in development tools
```powershell
function Negate-Flag
{
    [OutputType([Boolean])]
    param(
        [Parameter(Mandatory=$true)]
        [Boolean]$flag
    )
    return -not $flag
}

> Negate-Flag $true
False
```

Parameters also work for scripts.

greet.ps1:
```powershell
param(
    [Parameter(Mandatory=$true)]
    [string]$who
)

Write-Output "Hello, $($who)!"
```

```powershell
> ./greet.ps1 -who you
Hello, you!
```

## Output

Write-Output might not do what you expect
```powershell
function Frob
{
    Write-Output "hello"
    return "frob"
}
> $x = Frob
> $x
hello
frob
```

Write-Host is perhaps what you wanted
```powershell
function Frob
{
    Write-Host "hello"
    return "frob"
}
> $x = Frob
hello
> $x
frob
```

Add some color
```powershell
function Write-Blues
{
    Write-Host -BackgroundColor blue "got the blues?"
}
> Write-Blues
got the blues
```

Error output
```powershell
function Buggy
{
    Write-Error "darn it"
    return "ok"
}
> Buggy
Buggy : darn it
At line:1 char:1
+ Buggy
+ ~~~~~
    + CategoryInfo          : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,Funky

ok    
```

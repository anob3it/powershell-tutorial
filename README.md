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

### Comments
Line comment
```powershell
> # comment
```

Block comment
```powershell
> 2 * <# 2 #> 3
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

## Files

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
> Set-Content -Encoding 'UTF8' 'multilingual.txt' 'Hellö, wärlden!'
> Get-Content -Encoding 'UTF8' 'multilingual.txt'
```

Current directory
```powershell
> $PWD
Path
----
/Users/moebius
> $PWD.Path
/Users/moebius
```

Get file information
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

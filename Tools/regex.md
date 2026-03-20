# Regular Expression

## Reference
Thanks to the kind folks below! 
- [TrustedSEC Regex Cheat Sheet](https://trustedsec.com/blog/regex-cheat-sheet) 
- [Regular Expressions Cheat Sheet by DaveChild
](https://cheatography.com/davechild/cheat-sheets/regular-expressions/)

## Character Classes
> Basic alphanumeric matching.

|regex|description|example|
|---|---|---|
|[a-z]|All lowercase letters| a, b, c |
|[A-Z]|All uppercase letters| A, B, C |
[0-9]|All numbers| 1, 2, 3 |
|[aeiou]|Custom list of char to match| a, e, i, o, u |
|[\^0-9]|Negative match - any char that are not in the list |A, B, C |

## Occurences
> Anything that might repeats, i.e. IP address

|regex|description|example|
|---|---|---|
|{1,3}|define range - minimum && maximum| a{1,3} -> a, aa, aaa |
|{4}|define fixed range| a{4} -> aaaa |
|{4, }|define range - minimum value and more| a{4,} -> aaaa, aaaaaa ... |
|+|match 1 or more of the same pattern| a+ -> a, aa, aaa ... |
|*|match 0 or more of the same pattern| a* -> `none` or a, aa ... |
|?|make pattern optional| colou?r -> color / colour |
|\||OR operator for regex| a|b -> a OR b|

## Escape Character
> if you want to use any char as pattern instead of syntax then you will to escape it

|regex|description|example|
|---|---|---|
|`\`|escape any character that comes after it including itself|`\+` will match char `+`|

## Meta Characters
> Shortcut for long commands, usually starts with escape character

|regex|description|example|
|---|---|---|
|`.`|a literal dot, wildcard but not newline(\n)|h.t -> hot, hat, h@t|
|\d|matches any digit|1, 2, 3|
|\D|matches any non-digit, reverse of \d|a, B, "|
|\s|matches any whitespace character|space, \n, tab \t, carriage return \r, form feed \f|
|\S|matches any non-whitespace character|Hello@?123|
|\w|matches word, same as [A-Za-z0-9_]|anything alphanumeric and underscore|
|\W|reverse of \w|matches spaces, punctuation, symbols, emojis|
|^|start of the line|^He -> Hello, Hey, Help|
|$|end of the line|llo$ -> hello, jello, Armadillo|
|\A|start of the input, not multiline|\Aaa -> aa, aaa, aab|
|\Z|end of the input, not multiline|aa\Z -> baa, zzaaa, aa|
|\b|matches word boundaries| \bcat\b -> strictly `cat`, no `cats`|
|\B|matches non-word boundaries| \Bcat\B -> `concatenate`, strictly no `cat`|

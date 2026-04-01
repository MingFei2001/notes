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
|\xxx|Octal character - allow matching by octal value, legacy usage avoid unless necessary|`\101` -> `A`|
|\xhh|Hex Character - allow matching by hexadecimal value, suprisingly common|`\41` -> `A`|
|\uhhhh|4-digit Unicode Character - allow matching by code point|`\u0041` -> `A`|
|\uhhhhhhhh|8-digit Unicode Character - allow matching of full Unicode range including emoji and rare characters|`\u0001F600` -> `😀`|

## Occurences
> Anything that might repeats, i.e. IP address

|regex|description|example|
|---|---|---|
|{1,3}|define range - minimum && maximum| a{1,3} -> a, aa, aaa |
|{4}|define fixed range| a{4} -> aaaa |
|{4, }|define range - minimum value and more| a{4,} -> aaaa, aaaaaa ... |
|+|match 1 or more of the same pattern| a+ -> a, aa, aaa ... |
|*|match 0 or more of the same pattern| a* -> `none` or a, aa ... |
|?|make pattern optional, often refer as lazy or greedy| colou?r -> color / colour |
|\||OR operator for regex| a|b -> a OR b|
|(...)|Capture group - group pattern together for later use as numbered group|`(ab)+` -> `ab or abab` and match can be reference as '\1'|
|(?...)|Passive Capture group - group pattern but does not capture as numbered group|`(?:ab)+` -> `ab or abab` but no group created|

## Escape Character
> if you want to use any char as pattern instead of syntax then you will to escape it

|regex|description|example|
|---|---|---|
|\\ |escape any character that comes after it including itself|`\+` will match char `+`|
|\Q and \E|Begin and end of literal - turn off special char parsing and match everything in between as is|`\Qabc.def*\E` -> `.` and `*` got parse as char not patterns -> match only `abc.def*`|

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

## Position Anchors
> does not match character but position

|regex|description|example|
|---|---|---|
|^|start of the line|^He -> Hello, Hey, Help|
|$|end of the line|llo$ -> hello, jello, Armadillo|
|\A|start of the input, not multiline|\Aaa -> aa, aaa, aab|
|\Z|end of the input, not multiline|aa\Z -> baa, zzaaa, aa|
|\b|matches word boundaries| \bcat\b -> strictly `cat`, no `cats`|
|\B|matches non-word boundaries| \Bcat\B -> `concatenate`, strictly no `cat`|

## Assertion
> Check for condition but do not make modification

|regex|description|example|
|---|---|---|
|?=|Lookahead - match only if what follows matches|`\d(?=kg)` -> matches `5 in 5kg` not `5 in 5lbs`|
|?!|Negative Lookahead - match only if what follows doesn't match|`\d(?!kg)` -> matches `5 in 5lbs` not `5 in 5kg`|
|?<=|Lookbehind - match only if what before matches|`(?<=\$)\d+` -> matches `100 in $100` not `100 in 100$`|
|?!= or ?<!|Negative Lookbehind - match only if what before doesn't match (engine-dependent)|`(?<!\$)\d+` -> matches `100 in EUR100` not `100 in 100$`|
|?>...|Once-only/Atomic Group - no backtrack so once matches then locked in even if later part failed|`a(?>bc\|b)c` -> matches `abc` not `abcc`|
|(?(condition)then)|If-then - if (condition) is true, (then) must match as well|(foo)?bar(?(1)baz) -> optionally matches foo but if matches then baz **MUST** match -> `foobar baz or bar` not `foobar or baz bar`|
|(?(condition)then\|else)|If-then-else - the same as if-then but with a compulsory else when `if` failed to match|(foo)?bar(?(1)baz\|qux) -> optionally matches `foo`, if foo then requires baz else MUST match qux -> `foorbarbaz` or `barqux`|
|?#|comment - like how comment in bash works (engine-dependent)|`abc(?# matches abc)def` -> no effect just a plain comment|

## Special Characters
> Just some special characters, usually non-printable characters, good to know for handling some patterns

|Special Characters|description|Remarks|
|---|---|---|
|`\n`|New line - matches a newline character|Common in Unix/Linux text files to indicate new line|
|`\r`|Carriage Return a.k.a `<CR>` - Old Mac/Windows line ending, move paper on typewriter to far right so the pointer goes left on a typewriter|Windows uses `\r\n` to indicate new line|
|\t|Horizontal tab space|e.g. -> `name\tage` name \<TAB\> age|
|\v|Vertical tab space, rarely used|meant for legacy printer to move the print head literal down|
|\f|Form feed a.k.a. Page Break - indicate start of new page|Mostly historical|

## Pattern Modifiers
> These doesn't match anything but changes the behaviour of regex engine

|Modifiers|description|example|
|---|---|---|
|g|Global match - dont stop after the first match, match all occurences|`sed 's/foo/bar/g'` -> go thru all foo and change to bar|
|i|Case-insensitive - ignore letter case, include both uppercase and lowercase|`sed 's/foo/bar/i'` -> match `foo FOO FoO`|
|m|Multiline mode - do it over line instead of input|less relevant as most engine processed them one line at a time|
|s|Single-line (DOTALL) - make `.` match newline too, less common|wildcard `.` also matches `\n` as well|
|x|Extended/Verbose - allow space and comment inside regex for readability|`\d+ # match number` -> comment works|
|e|Evaluate replacement **!!DANGEROUS!!** - allow execution of command in regex, |`sed 's/.*/echo &/e' -> will execute `echo` when match|
|U|Ungreedy - make quantifiers lazy by default, stop at first possible match and only backtrack when needed|`<.*>` in `<tag>content<tag>` will only match the first `<tag>`, the rest will be ignored|

## String Replacement Variables
> Regex engine remember previous matches, this allow reuse of it mostly for replacement after match

|Modifiers|description|example|
|---|---|---|
|$n|nth capture group - allow reuse of content via numbered capture group|you can do `(\w) (\d+)` then `$2 $1` to swap them around|
|$&|Entire matched string - hold the entire regex match|`\d+` for `abc123def` -> `$&` will store the matched `123`|
|$` (dollar backtick)|Before matched string - everything before $&|`\d+` for `abc123def` -> `$`` will store `abc`|
|$' (dollar single quote)|After matched string - everything after $&|`\d+` for `abc123def` -> `$'` will store `def`|
|$+|Last matched capturing group - the last capture group that was matched|`(a)|(b)` for `b` -> `$+` will store `b`|

---
layout: post
title:  "????"
date:   2023-05-10 10:22:14 -0700
permalink: apl-lisp-alike
---

### Inspiration
> They appear to have implemented a lisp in python?
 
>  for a wacky indentation-sensitive definition of lisp [https://scopes.readthedocs.io/en/latest/dataformat/](https://scopes.readthedocs.io/en/latest/dataformat/)
>  though this one is, an affiliated build tool using the same input syntax 
>  `package.json` as it were
>  example [https://github.com/radgeRayden/bottle/blob/7993c2/recipes/sdl2.eo](https://github.com/radgeRayden/bottle/blob/7993c2/recipes/sdl2.eo)

>> okay so
> (yaml is basically markdown-flavored and scopes-lang is basically yaml, right? (: )
> poking at the string lexing in APL, source material

#### source material
```python
def unescape_string (buf):
    dst = ""
    src = 0
    l = len(buf)
    while src < l:
        c = buf[src]
        if c == '\\' and ((src+1) < l):
            c = buf[src+1]
            if c == 'n':
                dst += '\n'
                src += 2
                continue
            elif c == 't':
                dst += '\t'
                src += 2
                continue
            elif c == 'r':
                dst += '\r'
                src += 2
                continue
            elif c == '"':
                dst += '"'
                src += 2
                continue
            elif c == '\n':
                src += 2
                # skip until next non whitespace character
                while src < l:
                    c = buf[src]
                    if c in ' \t':
                        src += 1
                    else:
                        break
                continue
            elif c == 'x' and ((src+3) < l):
                c0 = parse_hexchar(buf[src+2])
                c1 = parse_hexchar(buf[src+3])
                if (c0 >= 0) and (c1 >= 0):
                    dst += chr((c0 << 4) | c1)
                    src += 3
                    continue
        dst += c
        src += 1
    return dst
```

### Part 1: segmentation, per the outer
```python
while src < l:
        c = buf[src]
        if c == '\\' and ((src+1) < l):
            c = buf[src+1]
            if c == ...:
                src += 2
                continue
            elif c == '\n':
                raise "we'll get to that later"
            elif c == 'x' and ((src+3) < l):
                # and hex but we'll error otherwise
                src += 3
                continue
        src += 1
```

```apl
      '\'='abc\nde\"fg\x12h'
0 0 0 1 0 0 0 1 0 0 0 1 0 0 0 0
      ⍳⍴'abc\nde\"fg\x12h'
1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16
      {⍳⍴⍵}'abc\nde\"fg\x12h'
1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16
      {(⍳⍴⍵)+'\'=⍵}'abc\nde\"fg\x12h'
1 2 3 5 5 6 7 9 9 10 11 13 13 14 15 16
      {↑⍵ ((⍳⍴⍵)+'\'=⍵)}'abc\nde\"fg\x12h'
a b c \ n d e \ "  f  g  \  x  1  2  h
1 2 3 5 5 6 7 9 9 10 11 13 13 14 15 16
      {⊆⍨((⍳⍴⍵)+'\'=⍵)}'abc\nde\"fg\x12h'
┌─┬─┬─┬───┬─┬─┬───┬──┬──┬─────┬──┬──┬──┐
│1│2│3│5 5│6│7│9 9│10│11│13 13│14│15│16│
└─┴─┴─┴───┴─┴─┴───┴──┴──┴─────┴──┴──┴──┘
      ⍝  which way do the args go again
      1 1 2 3⊆1 2 2 3
┌───┬─┬─┐
│1 2│2│3│
└───┴─┴─┘
```

```apl
      {⍵[∪((⍳⍴⍵)+'\'=⍵)]}'abc\nde\"fg\x12h'
abcnde"fgx12h
```

```apl
      {↑⍵ ('x'=⍵)}'abc\nde\"fg\x12h'
a b c \ n d e \ " f g \ x 1 2 h
0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0
      {↑⍵ (1⌽'x'=⍵)}'abc\nde\"fg\x12h'
a b c \ n d e \ " f g \ x 1 2 h
0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0
      {↑⍵ ('\'=⍵) (2×1⌽'x'=⍵)}'abc\nde\"fg\x12h'
a b c \ n d e \ " f g \ x 1 2 h
0 0 0 1 0 0 0 1 0 0 0 1 0 0 0 0
0 0 0 0 0 0 0 0 0 0 0 2 0 0 0 0
      {↑⍵ (('\'=⍵)+2×1⌽'x'=⍵)}'abc\nde\"fg\x12h'
a b c \ n d e \ " f g \ x 1 2 h
0 0 0 1 0 0 0 1 0 0 0 3 0 0 0 0
      {↑⍵ (('\'=⍵)+2×1⌽'x'=⍵)}'abc\nde\"fg\x12hxxx'
a b c \ n d e \ " f g \ x 1 2 h x x x
0 0 0 1 0 0 0 1 0 0 0 3 0 0 0 2 2 2 0
      ⍝  that's no good
      {↑⍵ ('\'=⍵) (2×1⌽'x'=⍵)}'abc\nde\"fg\x12hxxx'
a b c \ n d e \ " f g \ x 1 2 h x x x
0 0 0 1 0 0 0 1 0 0 0 1 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0 0 0 0 2 0 0 0 2 2 2 0
      {↑⍵ (('\'=⍵)×2×1⌽'x'=⍵)}'abc\nde\"fg\x12hxxx'
a b c \ n d e \ " f g \ x 1 2 h x x x
0 0 0 0 0 0 0 0 0 0 0 2 0 0 0 0 0 0 0
      {↑⍵ (('\'=⍵)(⊣×+)2×1⌽'x'=⍵)}'abc\nde\"fg\x12hxxx'
a b c \ n d e \ " f g \ x 1 2 h x x x
0 0 0 1 0 0 0 1 0 0 0 3 0 0 0 0 0 0 0
      {↑⍵ ((⍳⍴⍵)+('\'=⍵)(⊣×+)2×1⌽'x'=⍵)}'abc\nde\"fg\x12hxxx'
a b c \ n d e \ "  f  g  \  x  1  2  h  x  x  x
1 2 3 5 5 6 7 9 9 10 11 15 13 14 15 16 17 18 19
      ⍝                     ^^^ gap
      {↑⍵ (⌈\(⍳⍴⍵)+('\'=⍵)(⊣×+)2×1⌽'x'=⍵)}'abc\nde\"fg\x12hxxx'
a b c \ n d e \ "  f  g  \  x  1  2  h  x  x  x
1 2 3 5 5 6 7 9 9 10 11 15 15 15 15 16 17 18 19
      {⍵⊆⍨(⌈\(⍳⍴⍵)+('\'=⍵)(⊣×+)2×1⌽'x'=⍵)}'abc\nde\"fg\x12hxxx'
┌─┬─┬─┬──┬─┬─┬──┬─┬─┬────┬─┬─┬─┬─┐
│a│b│c│\n│d│e│\"│f│g│\x12│h│x│x│x│
└─┴─┴─┴──┴─┴─┴──┴─┴─┴────┴─┴─┴─┴─┘
      segment←{⍵⊆⍨(⌈\(⍳⍴⍵)+('\'=⍵)(⊣×+)2×1⌽'x'=⍵)}
```

#### Part 2: \t\n\r escapes, per
```python
            if c == 'n':
                dst += '\n'
                src += 2
                continue
            elif c == 't':
                dst += '\t'
                src += 2
                continue
            elif c == 'r':
                dst += '\r'
                src += 2
                continue
            elif c == '"':
                dst += '"'
                src += 2
                continue
```

```apl
      {'#',⍵,'#'}' '
# #
      {'#',⍵,'#'}⎕ucs 9
#    #
      {'#',⍵,'#'}⎕ucs 10
#
#
      {'#',⍵,'#'}⎕ucs 13
#
#
```

```apl
      'abc'∊⍨'a b x c'
1 0 1 0 0 0 1
      'abc'⍳'a b x c'
1 4 2 4 4 4 3
      'ABC.'⌷⍨⊂'abc'⍳'a b x c'
A.B...C
      'ABC.'{⍺[⍵]}'abc'⍳'a b x c'
A.B...C
      I←{⍺[⍵]}  ⍝ eventually the better ⌷ operator will come out
      'ABC.'I'abc'⍳'a b x c'
A.B...C
      'ABC.'{⍺I'abc'⍳⍵}'a b x c'
A.B...C
      'ABC.'{⍺⍺I⍵⍵⍳⍵}'abc'⊢'a b x c'
A.B...C
      'ABC.'{⍵⍵(⍺⍺I⍳)⍵}'abc'⊢'a b x c'
A.B...C
      'ABC.'{(∊∘⍵⍵)⍵}'abc'⊢'a b x c'
1 0 1 0 0 0 1
      'ABC.'{⍵⍵(⍺⍺I⍳)@(∊∘⍵⍵)⍵}'abc'⊢'a b x c'
A B x C
      'ABC'{⍵⍵(⍺⍺I⍳)@(∊∘⍵⍵)⍵}'abc'⊢'a b x c'
A B x C
      sub←{⍵⍵(⍺⍺I⍳)@(∊∘⍵⍵)⍵}
      (⎕ucs 9 10)sub'tn'⊢'sometextherenow'
some    ex    here
ow 
```

```apl
      segment 'some.\t.ex\t."here"\n.ow\x12'
┌─┬─┬─┬─┬─┬──┬─┬─┬─┬──┬─┬─┬─┬─┬─┬─┬─┬──┬─┬─┬─┬────┐
│s│o│m│e│.│\t│.│e│x│\t│.│"│h│e│r│e│"│\n│.│o│w│\x12│
└─┴─┴─┴─┴─┴──┴─┴─┴─┴──┴─┴─┴─┴─┴─┴─┴─┴──┴─┴─┴─┴────┘
      segment 'some.\t.ex\t.\"here\"\n.ow\x12'
┌─┬─┬─┬─┬─┬──┬─┬─┬─┬──┬─┬──┬─┬─┬─┬─┬──┬──┬─┬─┬─┬────┐
│s│o│m│e│.│\t│.│e│x│\t│.│\"│h│e│r│e│\"│\n│.│o│w│\x12│
└─┴─┴─┴─┴─┴──┴─┴─┴─┴──┴─┴──┴─┴─┴─┴─┴──┴──┴─┴─┴─┴────┘
      ≢¨segment 'some.\t.ex\t.\"here\"\n.ow\x12'
1 1 1 1 1 2 1 1 1 2 1 2 1 1 1 1 2 2 1 1 1 4
      (⎕ucs 9 10 13)sub'tnr'¨@(2=≢¨)segment 'some.\t.ex\t.\"here\"\n.ow\x12'
┌─┬─┬─┬─┬─┬──┬─┬─┬─┬──┬─┬──┬─┬─┬─┬─┬──┬─┬─┬─┬─┬────┐
│s│o│m│e│.│\    │.│e│x│\    │.│\"│h│e│r│e│\"│\│.│o│w│\x12│
│ │ │ │ │ │  │ │ │ │  │ │  │ │ │ │ │  │ │ │ │ │    │
└─┴─┴─┴─┴─┴──┴─┴─┴─┴──┴─┴──┴─┴─┴─┴─┴──┴─┴─┴─┴─┴────┘
      ⍝  hah
      'TNR'sub'tnr'¨@(2=≢¨)segment 'some.\t.ex\t.\"here\"\n.ow\x12'
┌─┬─┬─┬─┬─┬──┬─┬─┬─┬──┬─┬──┬─┬─┬─┬─┬──┬──┬─┬─┬─┬────┐
│s│o│m│e│.│\T│.│e│x│\T│.│\"│h│e│r│e│\"│\N│.│o│w│\x12│
└─┴─┴─┴─┴─┴──┴─┴─┴─┴──┴─┴──┴─┴─┴─┴─┴──┴──┴─┴─┴─┴────┘
      ¯1↑¨'TNR'sub'tnr'¨@(2=≢¨)segment 'some.\t.ex\t.\"here\"\n.ow\x12'
┌─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┐
│s│o│m│e│.│T│.│e│x│T│.│"│h│e│r│e│"│N│.│o│w│2│
└─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┘
      ∊¯1↑¨'TNR'sub'tnr'¨@(2=≢¨)segment 'some.\t.ex\t.\"here\"\n.ow\x12'
some.T.exT."here"N.ow2
      ∊¯1↑¨(⎕ucs 9 10 13)sub'tnr'¨@(2=≢¨)segment 'some.\t.ex\t.\"here\"\n.ow\x12'
some.    .ex    ."here"
.ow2             
      wesc←(⎕ucs 9 10 13)sub'tnr'
```

#### Part 3: hex codes, per ~~previous discussion and~~
```python
            elif c == 'x' and ((src+3) < l):
                c0 = parse_hexchar(buf[src+2])
                c1 = parse_hexchar(buf[src+3])
                if (c0 >= 0) and (c1 >= 0):
                    dst += chr((c0 << 4) | c1)
                    src += 3
                    continue
[...]
def parse_hexchar(c):
    if (c >= '0') and (c <= '9'):
        return ord(c) - ord('0')
    elif (c >= 'a') and (c <= 'f'):
        return ord(c) - ord('a') + 10
    elif (c >= 'A') and (c <= 'F'):
        return ord(c) - ord('A') + 10
    else:
        return -1
```

```apl
      ⎕d
0123456789
      ⎕a
ABCDEFGHIJKLMNOPQRSTUVWXYZ
      ⎕c⎕a
abcdefghijklmnopqrstuvwxyz
      16↑⎕d,⎕c⎕a
0123456789abcdef
      (⎕d,⎕c⎕a)⍳'\x10'
37 34 2 1
      (⎕d,⎕c⎕a)⍳2↓'\x10'
2 1
      ¯1+(⎕d,⎕c⎕a)⍳2↓'\x10'
1 0
      ¯1+(⎕d,⎕c⎕a)⍳2↓'\xfe'
15 14
      ¯1+(⎕d,⎕c⎕a)⍳2↓'\xFE'
36 36
      ¯1+(⎕d,⎕c⎕a)⍳2↓⎕c'\xFE'
15 14
      16⊥¯1+(⎕d,⎕c⎕a)⍳2↓⎕c'\xFE'
254
      {16⊥¯1+(⎕d,⎕c⎕a)⍳2↓⎕c⍵}¨'\x10' '\xaa' '\xFE' '\x07'
16 170 254 7
      hex←{16⊥¯1+(⎕d,⎕c⎕a)⍳⎕c⍵}
      (hex 2∘↓)¨'\x10' '\xaa' '\xFE' '\x07'
16 170 254 7

      ∊(¯1↑wesc)¨@(2=≢¨)segment 'some.\t.ex\t.\"here\"\n.ow\x12'
some.    .ex    ."here"
.ow\x12          
      ∊(hex 2∘↓)¨@(4=≢¨)(¯1↑wesc)¨@(2=≢¨)segment 'some.\t.ex\t.\"here\"\n.ow\x12'
some.    .ex    ."here"
.ow 18           
      (⊢,⎕ucs)hex'21'
33 !
      ∊(hex 2∘↓)¨@(4=≢¨)(¯1↑wesc)¨@(2=≢¨)segment 'some.\t.ex\t.\"here\"\n.ow\x21'
some.    .ex    ."here"
.ow 33           
      ∊⎕ucs∘(hex 2∘↓)¨@(4=≢¨)(¯1↑wesc)¨@(2=≢¨)segment 'some.\t.ex\t.\"here\"\n.ow\x21'
some.    .ex    ."here"
.ow!             
```

Intermission: I am kind of annoyed by the shape the next section ended up in, so background context

```apl
      10,11
10 11
      10<11
1
      10(,⍥-)11
¯10 ¯11
      10(<⍥-)11
0
      +/10 11
21
      <⍥-/10 11
0
      >⍥-/10 11
1
      
      10 11>9 20
1 0
      10 11>⍥-9 20
0 1
      >⍥-/(10 11)(9 20)
┌───┐
│0 1│
└───┘
      ⊃>⍥-/(10 11)(9 20)
0 1
```

#### Part 4: backslash-newline splices, per
```python
            elif c == '\n':
                src += 2
                # skip until next non whitespace character
                while src < l:
                    c = buf[src]
                    if c in ' \t':
                        src += 1
                    else:
                        break
                continue
```

```apl
      {'+'=⍵}'abcd-fgh+jk+mn-pq-s+uv'
0 0 0 0 0 0 0 0 1 0 0 1 0 0 0 0 0 0 0 1 0 0
      {'-'=⍵}'abcd-fgh+jk+mn-pq-s+uv'
0 0 0 0 1 0 0 0 0 0 0 0 0 0 1 0 0 1 0 0 0 0
      {↑⍵ ('+'=⍵) ('-'=⍵)}'abcd-fgh+jk+mn-pq-s+uv'
a b c d - f g h + j k + m n - p q - s + u v
0 0 0 0 0 0 0 0 1 0 0 1 0 0 0 0 0 0 0 1 0 0
0 0 0 0 1 0 0 0 0 0 0 0 0 0 1 0 0 1 0 0 0 0
      {(⍳∘⍴×⊢)'+'=⍵}'abcd-fgh+jk+mn-pq-s+uv'
0 0 0 0 0 0 0 0 9 0 0 12 0 0 0 0 0 0 0 20 0 0
      {⌈\(⍳∘⍴×⊢)'+'=⍵}'abcd-fgh+jk+mn-pq-s+uv'
0 0 0 0 0 0 0 0 9 9 9 12 12 12 12 12 12 12 12 20 20 20
      {⌈\(⍳∘⍴×⊢)'-'=⍵}'abcd-fgh+jk+mn-pq-s+uv'
0 0 0 0 5 5 5 5 5 5 5 5 5 5 15 15 15 18 18 18 18 18
      {↑⍵ ((⌈\⍳∘⍴×⊢)'+'=⍵) ((⌈\⍳∘⍴×⊢)'-'=⍵)}'abcd-fgh+jk+mn-pq-s+uv'
a b c d - f g h + j k  +  m  n  -  p  q  -  s  +  u  v
0 0 0 0 0 0 0 0 9 9 9 12 12 12 12 12 12 12 12 20 20 20
0 0 0 0 5 5 5 5 5 5 5  5  5  5 15 15 15 18 18 18 18 18
      {↑⍵ (((⌈\⍳∘⍴×⊢)'+'=⍵)<(⌈\⍳∘⍴×⊢)'-'=⍵)}'abcd-fgh+jk+mn-pq-s+uv'
a b c d - f g h + j k + m n - p q - s + u v
0 0 0 0 1 1 1 1 0 0 0 0 0 0 1 1 1 1 1 0 0 0
      {↑⍵ (⊃<⍥(⌈\⍳∘⍴×⊢)/('+'=⍵)('-'=⍵))}'abcd-fgh+jk+mn-pq-s+uv'
a b c d - f g h + j k + m n - p q - s + u v
0 0 0 0 1 1 1 1 0 0 0 0 0 0 1 1 1 1 1 0 0 0
      {↑⍵ (⊃<⍥(⌈\⍳∘⍴×⊢)/'+-'=¨⊂⍵)}'abcd-fgh+jk+mn-pq-s+uv'
a b c d - f g h + j k + m n - p q - s + u v
0 0 0 0 1 1 1 1 0 0 0 0 0 0 1 1 1 1 1 0 0 0
      {⍵/⍨⊃≥⍥(⌈\⍳∘⍴×⊢)/'+-'=¨⊂⍵}'abcd-fgh+jk+mn-pq-s+uv'
abcd+jk+mn+uv
```

```apl
      segment 'now the \N T  line escapes'
┌─┬─┬─┬─┬─┬─┬─┬─┬──┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┐
│n│o│w│ │t│h│e│ │\N│ │T│ │ │l│i│n│e│ │e│s│c│a│p│e│s│
└─┴─┴─┴─┴─┴─┴─┴─┴──┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┘
      segment ((⎕ucs 9 10)sub'TN')'now the \N T  line escapes'
┌─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┐
│n│o│w│ │t│h│e│ │\│ │    │ │ │l│i│n│e│ │e│s│c│a│p│e│s│
│ │ │ │ │ │ │ │ │ │ │ │ │ │ │ │ │ │ │ │ │ │ │ │ │ │
└─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┘
      ' T'∊⍨segment 'now the \N T  line escapes'
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
      ⍝  erm. well they're single-character arrays fair enough
      (,¨' T')∊⍨segment 'now the \N T  line escapes'
0 0 0 1 0 0 0 1 0 1 1 1 1 0 0 0 0 1 0 0 0 0 0 0 0
      '\N'∘≡¨segment 'now the \N T  line escapes'
0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
      (⊂'\N')∊⍨segment 'now the \N T  line escapes'
0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
      {↑⍵ (~⍵∊,¨' T') (⍵∊⊂'\N')}segment 'now the \N T  line escapes'
┌─┬─┬─┬─┬─┬─┬─┬─┬──┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┐
│n│o│w│ │t│h│e│ │\N│ │T│ │ │l│i│n│e│ │e│s│c│a│p│e│s│
├─┼─┼─┼─┼─┼─┼─┼─┼──┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│1│1│1│0│1│1│1│0│1 │0│0│0│0│1│1│1│1│0│1│1│1│1│1│1│1│
├─┼─┼─┼─┼─┼─┼─┼─┼──┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│0│0│0│0│0│0│0│0│1 │0│0│0│0│0│0│0│0│0│0│0│0│0│0│0│0│
└─┴─┴─┴─┴─┴─┴─┴─┴──┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┘
      {⍵/⍨⊃>⍥(⌈\⍳∘⍴×⊢)/((~⍵∊,¨' T') (⍵∊⊂'\N'))}segment 'now the \N T  line escapes'
┌─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┐
│n│o│w│ │t│h│e│ │l│i│n│e│ │e│s│c│a│p│e│s│
└─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┘
      ht nl←⎕ucs 9 10
      {⍵/⍨⊃>⍥(⌈\⍳∘⍴×⊢)/((~⍵∊,¨' ',ht) (⍵∊⊂'\',nl))}segment ((ht nl)sub'TN')'now the \N T  line escapes'
┌─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┐
│n│o│w│ │t│h│e│ │l│i│n│e│ │e│s│c│a│p│e│s│
└─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┘
      splice←{⍵/⍨⊃>⍥(⌈\⍳∘⍴×⊢)/((~⍵∊,¨' ',ht) (⍵∊⊂'\',nl))}
```

```apl
      ∊⎕ucs∘(hex 2∘↓)¨@(4=≢¨)(¯1↑wesc)¨@(2=≢¨)segment 'some.\t.ex\t.\"here\"\n.ow\x21'
some.    .ex    ."here"
.ow!             
      ∊⎕ucs∘(hex 2∘↓)¨@(4=≢¨)(¯1↑wesc)¨@(2=≢¨)segment 'some.\t.ex\t.\"here\"\n.ow\x21\',nl,' !'
some.    .ex    ."here"
.ow!             
 !               
      ∊⎕ucs∘(hex 2∘↓)¨@(4=≢¨)(¯1↑wesc)¨@(2=≢¨)splice segment 'some.\t.ex\t.\"here\"\n.ow\x21\',nl,' !'
some.    .ex    ."here"
.ow!!   
```

#### Final listing:
```apl
      segment←{⍵⊆⍨(⌈\(⍳⍴⍵)+('\'=⍵)(⊣×+)2×1⌽'x'=⍵)}
      ht nl cr←⎕ucs 9 10 13
      splice←{⍵/⍨⊃>⍥(⌈\⍳∘⍴×⊢)/((~⍵∊,¨' ',ht) (⍵∊⊂'\',nl))}
      sub←{⍵⍵(⍺⍺I⍳)@(∊∘⍵⍵)⍵}
      wesc←(ht nl cr)sub'tnr'
      hex←{16⊥¯1+(⎕d,⎕c⎕a)⍳⎕c⍵}
      unescape←{∊⎕ucs∘(hex 2∘↓)¨@(4=≢¨)(¯1↑wesc)¨@(2=≢¨)splice segment ⍵}

      unescape 'normal escapes: \(\)\"\'' character escapes: \n,\t,\x22stuff\x22\r,escaped line-\',nl,'   breaks'
normal escapes: ()"' character escapes: 
,    ,"stuff"                              
,escaped line-breaks                    
```

(and the next step for the code is error-checking, invalid hex etc
which it will currently just do the ascii math to, throwing would be easy, error line-columns would be polite
but in its current state it's more of an exercise)

also *theoretically* a non-toy implementation would be doing more working with offsets directly and less allocating two-character lists for each \x escape, but come on, the thing to beat here is python...

Like
– for every character find the offset to the next non-whitespace character
- generate list of `\` indices
- for every `\` followed by a newline its offset is that offset
- for every `\` followed by an x its offset is 3
- for every other `\` its offset is 1, if a character is not a slash its offset is 0
- copy out all the hex char pairs to a contiguous buffer, mask them, and look up the result bytes in a small table
- make an index vector from the unique elements of the max-scan of index + offset, run a scatter/gather copy thing (or just byte shifts) to get your final-length buffer
- (fixup the `\` pointer list according to its indices in said vector)
- lookup all the offset-1 slash characters that match the "am letter" mask in a second table
- dump contents of hexed buffer into the indices of the x slashes
- everything else should already be fine
- "GPUs are bad at parsing" my ass

ref `https://discord.com/channels/937086901226205284/1022150968651026523/1107182958944731177`
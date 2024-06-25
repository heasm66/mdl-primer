# Chapter 1. Basic Interaction

The purpose of this chapter is to provide you with that minimal amount of information needed to experiment with MDL (pronounced, affectionately, as Muddle) while reading this document. It is strongly recommended that you do experiment, especially upon reaching [chapter 6 (Simple Functions)](06-simple-functions.md).

## 1.1. Loading MDL

First, catch your rabbit. Somehow get the interpreter running -- the program in the file `SYS:TS.MDL` in the ITS version or `SYS:MDL.SAV` in the Tenex version or `SYS:MDL.EXE` in the Tops-20 version. [Just type `:MDL` to ITS, `MDL` or `MUDDLE` to Tops-20.] The interpreter will then type

    MUDDLE nnn IN OPERATION.
    LISTENING-AT-LEVEL 1 PROCESS 1

and then wait for you to type something.

The program which you are now running is an interpreter for the language MDL. **All** it knows how to do is interpret MDL expressions. There is no special "command language"; you communicate with theprogram -- make it do things for you -- by actually typing legal MDLexpressions, which it then interprets. **Everything** you can do at a terminal can be done in a program, and vice versa, in exactly the same way.

The program will be referred to as just "MDL" (or "the interpreter") from here on.

## 1.2. Typing

Typing a character at MDL normally just causes that character to be echoed (printed on your terminal) and remembered in a buffer. The only characters for which this is normally not true act as follows:

Typing the <kbd>Escape</kbd> or <kbd>Alt-Mode</kbd> key, which we will always refer to as `$` (dollar-sign), causes MDL to echo dollar-sign and causes the contents of the buffer (the characters which you've typed) to be interpreted as an expression(s) in MDL. When this interpretation is done, the result will be printed and MDL will wait for more typing.

Typing the rubout character (<kbd>DEL</kbd> in the ITS and Top-20 versions, <kbd>control</kbd>-<kbd>A</kbd> in the Tenex version) causes the last character in the buffer -- the one most recently typed -- to be thrown away (deleted). If you now immediately type another rubout, once again the last character is deleted -- namely, the second most recently typed. Etc. The character deleted is echoed, so you can see what you're doing. On some "display" terminals, rubout will "echo" by causing the deleted character to disappear. If no characters are in the buffer, rubout echoes as a carriage-return line-feed.

Typing <kbd>^@</kbd> (<kbd>control</kbd>-<kbd>atsign</kbd>) deletes everything you have typed since the last `$`, and prints a carriage-return line-feed.

Typing <kbd>^D</kbd> (<kbd>control</kbd>-<kbd>D</kbd>) causes the current input buffer to be typed back out at you. This allows you to see what you really have, without the confusing re-echoed characters produced by rubout.

Typing <kbd>^L</kbd> (<kbd>control</kbd>-<kbd>L</kbd>) produces the same effect as typing <kbd>^D</kbd>, except that, if your terminal is a "display" terminal (for example, VT100, VT52, H19, ...), the screen is cleared before the input buffer is retyped.

Typing <kbd>^G</kbd> (<kbd>control</kbd>-<kbd>G</kbd>) causes MDL to stop whatever it is doing and act as if an error had occurred ([section 1.3](#13-errors-simple-considerations)). <kbd>^G</kbd> is generally most useful for temporary interruptions to check the progress of a computation. <kbd>^G</kbd> is "reversible" -- that is, it does not destroy any of the "state" of the computation it interrupts. To "undo" a <kbd>^G</kbd>, type the characters

    <ERRET T>$

(This is discussed more fully far below, in [chapter 14 (Debugging MDL Programs)](14-debugging-mdl-programs.md).)

Typing <kbd>^S</kbd> (<kbd>control</kbd>-<kbd>S</kbd>) causes MDL to **throw away** what it is currently doing and return to a normal "listening" state. (In the Tenex and Tops-20 versions, <kbd>^O</kbd> also should have the same effect.) <kbd>^S</kbd> is generally most useful for aborting infinite loops and similar terrible things. <kbd>^S</kbd> **destroys** whatever is going on, and so it is **not** reversible.

Most expressions in MDL include "brackets" (generically meant) that must be correctly paired and nested. If you end your typing with the pair of characters `!$` (<kbd>exclamation-point</kbd> <kbd>ESC</kbd>), all currently unpaired brackets (but not double-quotes, which bracket strings of characters) will automatically be paired and interpretation will start. Without the <kbd>!</kbd>, MDL will just sit there waiting for you to pair them. If you have improperly nested parentheses, brackets, etc., within the expression you typed, an error will occur, and MDL will tell you what is wrong.

Once the brackets are properly paired and `$` (<kbd>ESC</kbd>) is typed, MDL will immediately echo carriage-return and line-feed, and the next thing it prints will be the result of the evaluation. Thus, if a plain `$` is not so echoed, you have some expression unclosed. In that case, if you have not typed any characters beyond the `$`, you can usually rub out the `$` and other characters back to the beginning of the unclosed expression. Otherwise, what you have typed is beyond the help of rubout and <kbd>^@</kbd>; if you want to abort it, use <kbd>^S</kbd>.

MDL accepts and distinguishes between upper and lower case. All "built-in functions" must be referenced in upper case.

## 1.3. Errors — Simple Considerations

When MDL decides for some reason that something is wrong, the standard sequence of evaluation is interrupted and an error function is called. This produces the following terminal output:

    *ERROR*
    often-hyphenated-reason
    function-in-which-error-occurred
    LISTENING-AT-LEVEL integer PROCESS integer

You can now interact with MDL as usual, typing expressions and having them evaluated. There exist facilities (built-in functions) allowing you to find out what went wrong, restart, or abandon whatever was going on. In particular, you can recover from an error -- that is, undo everything but side effects and return to the initial typing phase -- by typing the following first line, to which MDL will respond with the second line:

    <ERRET>$
    LISTENING-AT-LEVEL 1 PROCESS 1

If you type the following first line while still in the error state (before `<ERRET>`), MDL will print the `FRAMES` it went through to evaluate the function:

    <FRAMES>$

Typing `FR&` (pronounced 'frampersand') instead of `FRAMES` will cause MDL to print a condensed, usually more readable output.

This will also be explained in [chapter 14](14-debugging-mdl-programs.md).

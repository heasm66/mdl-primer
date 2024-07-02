# Chapter 13. Making Tables

It seems that MDL programmers are always making tables of one thing or another. Someone's Whois program may want a table relating a person's login name to his full name. Someone's Calculater program may want a table to associate arbitrary variable names with values.

There are any number of ways to implement tables for these types of purposes; some of these may already have come to mind. For our discussion, let's use the example of a calculator program which accepts inputs of the form:

    A = 4 + 3
    B = (A * A) + 7

Without considering the actual details of how the calculator might be written, something must be done to keep track of the fact that the variable `A` has a value of 7, and that `B` has a value of 56. What follows are a number of different appoaches to solving this sort of problem. Each should be examined carefully and the advantages and disadvantages noted.
## 13.1. Use a LIST

This is the most common and possible the most useful approach. For the given example, we can create a `LIST`, which, for example, is the `GVAL` of the `ÀTOM` `VARIABLES`.

    ,VARIABLES
    (A 7 B 56)

If one wants to add a new variable, say `C`, with a value, say 100, one can do the following:

    <SETG VARIABLES (C 100 !,VARIABLES)>

To check if a variable, say `D`, has a value, one can do this:

    <MEMQ D ,VARIABLES>

To actually get `D`'s most recent value,

    <COND (<SET M <MEMQ D ,VARIABLES>>
           <2 .M>)>

The `COND`clause returns a `FALSE` if `D` doesn't have a value; otherwise, it returns the value.

Removing variables from the `LIST` can be done with `PUTREST`. As an exercise, write a `FUNCTION` which, given a variable name and a `LIST` (like the one we used above), removes the variable and its value from the `LIST`. Be sure you handle the case in which the variable isn't in the `LIST`. One solution to this is given at the end of the chapter. Don't peek, and don't be too frustrated. The `FUNCTION` isn't *that* easy to write.

`LIST`s are very space-efficient. However, while `LIST`s are practical for smallish tables, larger ones will tend to become very slow to access, since `LIST`s are not random-access structures (see [chapter 7](07-mdl-types.md)). If your table needs to be more than a hundred elements long, you should probably try something else.
## 13.2. Use a VECTOR

Think twice before you do. As we saw in [chapter 7](07-mdl-types.md), `VECTOR`s have the property that they cannot be added to and cannot have elements removed without creating an entirely new structure (which is very garbage creating). Therefore, using `VECTOR`s is not a good idea unless the table is 'pre-formed' and elements need never be removed or added. If you have a table of ordered elements of relative fixed size, use of `VECTOR`s with some sort of binary-searching algorithm is appropriate. For the calculator example, don't use a `VECTOR`.
## 13.3. Use an ATOM

Another simple approach would be to `SETG` the variable name (which is an `ATOM`) to its value. Then, you can use `GASSIGNED?` to check if it has a value, `GVAL` to get it, and `GUNASSIGN` to remove it. Lookup using `GVAL`s is moderately fast, but there is a problem. Imagine the result of your poor calculator user setting a variable whose name is the name of your program. The use of `SET` and `LVAL` is also perilous.

## 13.4. Use an Association

MDL allows you to assign a value to a *pair* of MDL objects. This can be done using the `SUBR` `PUTPROP`. The value of such an 'association' can be retrieved with the `SUBR` `GETPROP`.

    <PUTPROP MICHAEL AGE 28>$
    MICHAEL
    <GETPROP MICHAEL AGE>$
    28

One can associate *any* three MDL objects using `PUTPROP`. A useless, but legal, use might be:

    <PUTPROP [1 2 3] (4 5 6) "FOOBAR">$
    [1 2 3]
    <GETPROP [1 2 3] (4 5 6)>$
    #FALSE ()

Why did the last `GETPROP` return `#FALSE ()`? Hint: Are either of the arguments to `GETPROP` `==?` to the arguments to `PUTPROP`?

By giving `PUTPROP` only two arguments, it returns what `GETPROP` would have returned, and then *removes* the association.

    <PUTPROP MICHAEL AGE>$
    28
    <GETPROP MICHAEL AGE>$
    #FALSE ()

In the calculator example, one could do something like this:

    <PUTPROP A VARIABLE 7>$
    7
    <PUTPROP B VARIABLE 56>$
    56

to set the variables value. One would retrieve the values like this:

    <GETPROP A VARIABLE>$
    7
    <GETPROP B VARIABLE>$
    56

Associations are fast (they use a hashing scheme with a fixed number of buckets), but rather space-inefficient. Large numbers of them will tend to crowd your core-image.
## 13.4.1. Hashing

Hashing, for those unfamiliar with the notion, is an algorithm for table lookup which is based on a 'directed search'. A hash table can be thought of as a `VECTOR` of `LIST`s. These `LIST`s are commonly called 'buckets'. Each actual item in the hash table is found in one of these 'buckets'. What makes hash lookup fast is that there is a simple algorithm for determining which 'bucket' an item is in. Once that determination is made, the 'bucket' is searched linearly for the item. Thus, a hash table of length 100, which contains 1000 items, would have an average of 10 items per 'bucket'. Thus, the access time for looking up an item would be the same as that for `MEMQ`ìng a `LIST` of 10 elements plus the small overhead of determining which 'bucket' the item is in. This is obviously much faster than linearly searching a `LIST` of 1000 elements, by a factor approaching 100.
## 13.5. Use an OBLIST

`OBLIST`s are tables of `ATOM`s which are hashed in such a way that finding an `ATOM` in one is very fast. Similary, inserting and removing `ATOM`s is simple.

To create an `OBLIST` of your own, use the `SUBR` `MOBLIST` (Make `OBLIST`), which takes a name (`ATOM`) and the number of hash buckets for the `OBLIST` (defaultly 17). For best results, the number of buckets should be prime.

    <SETG FOOBAR <MOBLIST FOOBAR 7>>$
    #OBLIST !{() () () () () () ()]

Note that there are seven empty `LIST`s in the `OBLIST` -- you guessed it ... each `LIST` is a bucket!

To insert an `ATOM` into an `OBLIST`, use the `SUBR` `INSERT`. To remove an `ATOM` from an `OBLIST`, use the `SUBR` `REMOVE`. To look up an `ATOM` in an `OBLIST`, use the `SUBR` `LOOKUP`. Each of these three `SUBR`s takes a `STRING`, the `PNAME` of the `ATOM`, and an `OBLIST`.

    <INSERT "MIKE" ,FOOBAR>$
    MIKE!-FOOBAR
    ,FOOBAR
    #OBLIST ![() () () () (MIKE!-FOOBAR) () ()]
    <LOOKUP "MIKE" ,FOOBAR>$
    MIKE!-FOOBAR
    <LOOKUP "BLETCH" ,FOOBAR>$
    #FALSE ()

There's something new here, namely the suffix to the name of the `ATOM`: an exclamation point, a hyphen, and an `ATOM`. This suffix is called an 'oblist-trailer' or simply a 'trailer'. It is there so that `READ` and `PRINT` can distinguish this new `ATOM` whose `PNAME` is `FOOBAR` from an `ATOM` on another `OBLIST` whose `PNAME` is `FOOBAR`. Therefore, to directly reference the `ATOM` of `PNAME` `BLETCH` in the `FOOBAR` `OBLIST`, one must type in the following:

    BLETCH!-FOOBAR

In fact, typing `BLETCH!-FOOBAR` causes `READ` to create an `ATOM` with `PNAME` `BLETCH` in the `FOOBAR` `OBLIST` if none already existed. Not only that, but typing `FROB!-MUMBLE` causes `READ` to create an `ATOM` of `PNAME` `FROB` in the `MUMBLE` `OBLIST` (*creating* a `MUMBLE` `OBLIST` *if necessary*) if none already existed.

If you are interested in a more complete description of `OBLIST`s, refer to the next section. To continue with the calculator example, we might start by creating an `OBLIST` for variables.

    <SETG VARIABLES <MOBLIST VARIABLES>>$
    #OBLIST ....

Then, we assign values to `A` and `B` as follows:

    <DEFINE SET-VARIABLE (NAM VAL "AUX" (PNM <PNAME .NAM>))
        #DECL ((NAM) ATOM (VAL) ANY (PNM) STRING)
        <SETG <COND (<LOOKUP .PNM ,VARIABLES>)
                    (T <INSERT .PNM ,VARIABLES>)>
               .VAL>>$
    SET-VARIABLE
    <SET-VARIABLE A 7>$
    7
    <SET-VARIABLE B 56>$
    56

To retrieve values, we might do this:

    <DEFINE GET-VARIABLE (NAM "AUX" ATM)
        #DECL ((NAM) ATOM (ATM) <OR FALSE ATOM>)
        <COND (<SET ATM <LOOKUP <PNAME .NAM> ,VARIABLES>>
                 ,.ATM)>>$
    GET-VARIABLE
    <GET-VARIABLE B>$
    56
    <GET-VARIABLE D>$
    #FALSE ()

Using `OBLIST`s in this way solves the problem mentioned earlier regarding the use of `ATOM`s; that of variable conflicts. By using `ATOM`s in your own private `OBLOIST`, there is no danger of mistakenly changing the value of someone else's (or your own...) `ATOM`. Now, your calculator user can use variable names which are the same as those of your calculator `FUNCTION`s without fear of disaster.

To summarize, using `OBLIST`s is fast. `ATOM`s are rather large; about the same size as an association. Whereas the hashing table for associations is a fixed size, the hashing table for an `OBLIST` can be determined when the `OBLIST` is created. `ATOM`s are more versatile than associations, and can be used in more ways. Good MDL programmers, given the choice, will use `OBLIST`s over associations.
## 13.6. OBLISTs, READ and PRINT

It was stated in section 4.1 that typing `GEORGE` to `MDL` caused `READ`to "look up the representation [of `GEORGE`]" in a "table it keeps for such purposes...." It should now be clear that the "table it keeps" is, in fact, an `OBLIST`, and that it "looks up the representation" by using the `SUBR` `LOOKUP`. You are now ready to understand what, in fact, `READ` **does**.

When `READ` encounters something that it determines must be an `ATOM` (i.e. it can't be anything else), it does `LOOKUP`s of the `PNAME` sequentially in all of the `OBLIST`s in `.OBLIST` (i.e. the `LVAL` of the `ATOM` `OBLIST`). [MDL sets up `.OBLIST`to be a `LIST`of `OBLIST`s. Initally, `.OBLIST` has two `OBLIST`s in it: the `INITIAL` `OBLIST` (user `ATOM`s) and the `ROOT` `OBLIST` (MDL's `ATOM`s). The `ATOM`s which point to the `F/SUBR`s all live in `ROOT`.] The value of the first `LOOKUP` to succeed becomes the value of the call to `READ`. If the `PNAME` isn't found, an `ATOM` with that `PNAME` is `INSERT`ed into `<1 .OBLIST>`.

If `READ` (of `ATOM`s) were written in MDL, it might look like this:

    <DEFINE READ-ATOM (STR)
        #DECL ((STR) STRING)
        <COND (<MAPF <>
                     <FUNCTION (OBL "AUX" ATM)
                         #DECL ((OBL) OBLIST (ATM) <OR FALSE ATOM>)
                         <COND (<SET ATM <LOOKUP .STR .OBL>>
                                <MAPLEAVE .ATM>)>>
                     .OBLIST>)
              (T <INSERT .STR <1 .OBLIST>>)>>

However, if an explicit trailer is given, the `ATOM` is placed in the `OBLIST` named in the trailer. Trailers may be 'recursive'. For example, `A!-B!-C!-D!-E` is an `ATOM` with `PNAME` `A` which is in an `OBLIST` whose name is an `ATOM` with `PNAME` `B` which is on an `OBLIST` whose name.... The `ATOM` with `PNAME` `E` will reside in one of the `OBLIST`s in `.OBLIST`. When `PRINT` attemps to print an `ATOM` of this kind, it prints trailers until one of the `OBLIST` names can be found on an `OBLIST` in `.OBLIST`.
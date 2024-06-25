# Introduction

Over the years the original MDL (pronounced "Muddle") Primer by Greg Pfister [Pfister 72] became more and more a reference manual and less a Primer from which a novice could learn the language. Some of the text of the original has been re-used in this document, but much has been eliminated, changed, or re-ordered, and a reasonable amount of new material has been added. In particular, a number of figures and many more examples have been added to make some of the more difficult concepts easier to understand.

This Primer is intended as an introduction to MDL. After assimilating the information contained herein, you should be able to write very good programs. However, for any individual topic in the MDL Primer there is likely to be more information available in _The MDL Programming Language_ [Galley 79] and _The MDL Programming Environment_ [Lebling 80], and there are many topics in these documents which are not addressed in the Primer. Anyone who plans to do any serious work with MDL should read these documents.

One of the difficulties in writing a Primer is to make it useful to those who don't know anything at all about programming without boring those who know a lot of the basics. Hopefully those at both extremes will find this to be easy to read. If you are a complete novice, however, there may be some unfamiliar references and some material which doesn't make sense on your first reading.

## Why MDL?

Many people ask this. It is often hard for those who use MDL to put into words their reasons for liking it. Those of us who use MDL are convinced that it is a better language than any other we've encountered. Unfortunately, very little has been done to convince others of this and spread the use of this marvelous tool.

MDL was created in the early 1970's by a group at the Dynamic Modeling/Computer Graphics division of MIT's Project MAC (later renamed the Laboratory for Computer Science). It is an offshoot of the original Lisp. There have been quite a few offshoots of Lisp in the past 10 years - MacLisp, InterLisp, Lisp Machine Lisp, Lisp1.5, UCI Lisp, Franz Lisp, etc., etc. - but none of them are like MDL.

Since MDL is a distant relative of Lisp and many of those first learning MDL have some familiarity with Lisp, a short comparison of the two languages follows. If you are not familiar with Lisp (or, better still, with __any__ other languages) count your blessings.

MDL's similarties to Lisp: MDL shares the advantages of Lisp over the more popular languages such as Basic, Fortran, Cobol, Algol, Pascal, etc.

- It has an interpreter which allows real-time interaction and allows you to define and test individual functions separately.

- Its syntax is very simple.

- Any data object or function can be passed as an argument or returned as a value.

- It has list structures equivalent to Lisp's.

- Recursive functions can be written quite easily.

The similarities between MDL and Lisp are such that in many cases a few minor changes to Lisp code will convert it into working MDL code. Given the other features of MDL, no MDL programmer would write the program in the same Lisp style.

MDL's dissimilarities to Lisp: Many objections to Lisp are answered in MDL.

- Strongly typed languages provide much better error detection tools than Lisp. MDL allows declarations of all variable types to whatever level of complexity is desired. A variable can be declared to be one of several types.

- Recursion is a useful tool, but often is not a very efficient way to solve the problem. Lisp's motto "To iterate is human, to recurse divine," is not one of MDL's tenets. MDL allows recursion, but provides excellent facilties for iteration.

- MDL has a very powerful set of data structures - Lists, Strings, Vectors, and Uniform Vectors. Although lists are a very useful and flexible form of structure, they are certainly not optimal in all cases. MDL's various structures allow the use to save space and access time. MDL's structures are also "first class," in that the standard functions for manipulating data structures can be used on all of them equivalently.

- Probably the biggest complaing about Lisp-like languages is that they are unsuitable for "production programming" because they are too slow. MDL has an excellent compiler which as far as we know is the best compiler for a Lisp-like language. It produces machine code equivalent in efficiency to Fortran and Cobol, which are considered very efficient.

- MDL has a rich library of useful program aids. The editing and debugging functions are among the best. The package system allows building of very large programs from small sections, usually written by different people, without worrying about variable name conflicts.

- Probably the most distinctive feature of MDL is its mechanism for user-defined types, which is the best of any language with which we are familiar. User-defined types have been retrofitted on some of the newer versions of Lisp, but in most cases they can be used only with special functions and cannot be used in the same general way that Lists can.

Hopefully some of your questions have been answered and you have some ready answers when you get flak from your non-MDL programming friends. Learning MDL should be an enjoyable and worthwhile experience. Your reactions to this Primer and suggestions for changes are always welcome. Good luck!

Warning! You are about to embark on an undertaking fraught with peril. MDL programming has been proven to be habit-forming. Once you begin, you may find the habit hard to kick!

# Acknowledgements

We are deeply indebted to our predecessors for their work on this topic: Greg Pfister, who wrote the original _A Muddle Primer_ [Pfister 72], and Stuart Galley, who updated that document and added significantly to it to create _The MDL Programming Language_ [Galley 79] document. Some of the text and examples of the original documents survive here, and some other material was simply rewritten in an order and style which we consider more comprehensible.

Special thanks to Chris Reeve, Dave Lebling, Stu Galley, Poh Lim, Thomas Michalek, Dave Scrimshaw, Tim Anderson, Mark Plotnick, and Prof. J.C.R. Licklider for their many comments and suggestions.

No document on MDL would be complete without acknowledging the "original implementors." If not for their inspiring work, this fine language would not exist. We are forever grateful to Gerald Sussman, Carl Hewitt, Chris Reeve, Dave Cressey, and Bruce Daniels. Thanks are also extended to the many unnamed hackers who have improved the language and the programming environment over the years.

This work was supported by the Advanced Research Projects Agency of the Department of Defense and was monitored by the Office of Naval Research under Contract Number N00014-75-C-0661.

This document was prepared using Scribe and printed on the Xerox Dover printer.

(c) Copyright 1981 Massachusetts Institute of Technology. All rights reserved.

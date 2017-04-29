---
author: Shujian Yang
date: 2014-10-14 13:00:00
title: Choose a nice code typeface for the blog
---

Note: In this article, I will use the term "typeface" instead of the more popular term "font". The reason can be found [here](http://mindgruve.com/blog/advertising/typeface-vs-font-what-is-the-difference).


Everyone loves to read an article that looks nice and clean. For an IT blog, since large paragraphs of codes often appear, it is very important to choose a good typeface to display codes, which comforts readers significantly in relative to a random one.

Different programmers may have different opinions about how a "nice" typeface looks like. Nevertheless, there are still some criteria accepted by the majority of programmers about good code typeface:  
* It should be in monospace family, meaning every character should has the same width.  
* The edge should be smooth enough.  
* Some similar characters should be able to be distinguished easily. For example, letter 'O' and digit '0', letter 'l' and digit '1'.  
<!--excerpt-->

There are a lot of articles on Internet that introduce nice-looking typefaces. For myself, I prefer Dejavu Sans Mono and Consolas. The former is a free typeface which can be downloaded [here](http://dejavu-fonts.org/wiki/Download). The latter is a commercial typeface owned by Microsoft. But since it is shipped with many Microsoft products such as Microsoft Office and Microsoft Visual Studio, it is very likely that if you are using a Windows system, then you already have this typeface installed.

Since this blog uses Jekyll, here I am going to show how to set up code typeface displayed in the blog.

Go to the blog directory, use text editor to open `css/main.scss`. Add the following line as variable:

```scss
$code-font-family: "Dejavu Sans Mono", Consolas, monospace;
```

This is a "web safe" setting. It means that when browser reaches this part, it will try to check if the first typeface is installed in the system. If yes, it applies it. If not, it will try the next one, so on and so forth till it finds a match. In this example, the browser would first try Dejavu Sans Mono, then Consolas, then monospace.

It is worth noting that "monospace" here is not an actual typeface, but a typeface family. It refers to all typefaces that all characters within have same width. By adding this, it guarantees that if the previous two typefaces are not found, a typeface of monospace family will be used, in order to meet the criteria mentioned before. Which monospace typeface is chosen depends on the browser used, but Courier or Courier New are very possible because they are classic monospace typefaces installed in most systems.

Then open `_sass/_base.scss`, find the "code" session, add `font-family: $code-font-family` in it like below. Note I also change the font color and background color in code section.

```scss
code {
    color: #fff;
    font-size: 15px;
    font-family: $code-font-family;
    border: 1px solid $grey-color-light;
    border-radius: 3px;
    background-color: #161623;
}
```

To test the effect, let us see how different systems display the following "codes" (actually a piece of output from the Sleuth Kit) differently.

```shell
$ mmls physicalUSBraw.001
DOS Partition Table
Offset Sector: 0
Units are in 512-byte sectors

     Slot    Start        End          Length       Description
00:  Meta    0000000000   0000000000   0000000001   Primary Table (#0)
01:  -----   0000000000   0000000511   0000000512   Unallocated
02:  00:00   0000000512   0001957887   0001957376   DOS FAT16 (0x06)
```

If Dejavu Sans Mono is installed, the codes look like this:  
![dejavu](/images/dejavusansmono.png)

If you don't have Dejavu Sans Mono, but installed Consolas, they look like this:  
![consolas](/images/consolas.png)

In the end, let us see what happened if I don't designate `monospace` at the end of font-family setting while previous typefaces are not found. Use the following setting in `css/main.scss`:

```scss
$code-font-family: Consolas;
```

Then open Firefox in Ubuntu operating system, where Consolas typeface is not installed. Visit the same page, you can see:

![nomono](/images/nomono.png)

Note Firefox now choose a non-monospaced typeface to display the codes. I am sure everyone will immediately notice the rows in the bottom table are incorrectly aligned, which is painful to programmers, or at least, to me. :) But seriously, code alignment is very important when programming. Unexpected alignments may indicates grammar errors or typos. Thus, using monospaced typeface is highly recommended to avoid false alignment errors and distractions to programmers.

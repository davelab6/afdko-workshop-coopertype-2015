# Adobe Font Development Kit Introduction Workshop 2015

Usual disclaimer for live blogging: 
These are informal notes taken by me, Dave Crossland, at the event, and may or may not be similar to what was said by the people who spoke on these topics. 
This is probably FULL of errors. 
What do you want for free? :) 
If something here is incorrect it is probably because I mistyped it or misunderstood, and if anyone wants corrections, just should tweet me – [@davelab6](https://twitter.com/davelab6) - or [file an issue](https://github.com/davelab6/type-notes/issues) - or email me <dave@lab6.com>

Thanks!

* * *


This event was held at the Cooper Union on 2015-06-08

The original event page is <http://coopertype.org/event/adobe_font_development_kit_1>

Introduction
-------------

What is the FDK?

FDK (AFDKO) is Adobe Font Development Kit for OpenType. 
Kit means its not one thing, but a collectino of tools, concerned with font production and QA. Most font editors use it internally to build font binaries; autohinting; removing overlaps. 

WHy does it exist?

ADobe tools use fonts, the fonts are meant to be good, so Adobe helps font designers make good fonts by providing these tools. PostScript fonts was kept secret for a while, and so only ADobe could do high quality fonts, but then multiple different Type1 implementatinos were invalid and made lots of problems for Adobe. So FDK being available is a quality play for ADobe. It was recently make open source, and the source is on Github. Ben Kiel corrected some typos ;) Its great that it is open source, its rejuvenating it, and looking at stuff not touched in 20 years ;) Read Roberts is the maintainer at Adobe; he used to work on satellite technology before Adobe ;)

WHy this workshop?

When I was a student at KABK I found it interesting with Miguel. I learned tools like grep that i hadn't heard of before. I think its good to know the technical side of fonts, so you can troubleshoot for your customers. 

How does it basially work?

You habve outlines and then metadata. The FDK tools combines them and spits out a binary file, OTF. 

outlines can be PFA or UFO; mostly UFO these days. 

the metadata is in text files. 

FDK tools can be used on all stages; they worked on binaries to check outlines and modify things after compiling, they can check the OTL in a `.fea` file. 

Preparation
-----------------

(red slides for learning, blue slides for doing)

You'll need a code editor. I recommend TextWrangler, its gratis, and there's also others. Don't use TextEdit or Notepad; they are note taking in plain text, you need a real code editor. Also don't use the Mac App Store, the apps instaleld that way are limited in their system functionality. SublimeText, Coda, Textmate are others for Mac. 

So, we are all on Mac. Open Terminal.app in `/Applications/Utilities` and enter these commands :) 

```
say Hello Cooper Students;
say -v Vicky Hello
```

Now lets see directory changing and status:

```
pwd; # show the Present Working Directory
cd Desktop; # Change Directory to Desktop
ls; # LiSt files
cd; # Change Directory to home (~)
open Pictures; # open the file/directory with the associated mac application
open -a Preview Documents/Example.pdf; # open the file/dir with a specific application
open .; # open pwd in Finder
open File\ with\ spaces.txt; # open files with spaces in the name
```

So, Terminal can do all you can do in the Finder, but you can automate things. If you need to rename 10,000 files, you will not do that by hand. It can often be faster even for single file operations. You can easily deal with 'invisible' files, which begin with `.` like `.bash_profile`. 

Commadn line tools have options, passed as arguemnts to the program. eg to list files with detailed information, pass the `l` argument. You can also store the output of a command in a file, with the `>` character. Using it twice will append instead of overwriting the file.

```
ls -l; # long listing
ls -l > file.txt; # output to a file, overwriting
ls -l Desktop >> file.txt; # append to a file
```

Finally, to find out more about a tool, use it as an arg to `man` - the Manual command. To exit press `q`. 

there are 1,000s of tools, and with TextMate, in Preferences you can go to Terminal tab and install shell tools, and `mate` will be available to edit files in textmate. 

Lets edit the `.bash_profile` file:

```
mate .bash_profile;
```

Then append to this file:

    export PS1="\W\$ ";


Include a final blank line so that anything appended to the file starts on a new line. 

Then open a new Terminal window (Cmd+N) and you'll have a shorter starting line. 

Few more tips: you can drag and drop files/folders into terminal to insert their path at the current cursor. I can use arrows up/down to access previous commands. Ctrl-L to clear the screen. Ctrl-A and Ctrl-A to jump to the start/end of a line. Ctrl-U to clear a line from the end to the start. 

Now we install the command line tools, 

```
xcode-select --install;
```

Now download and unzip FDK-25-MAC.zip and place the unzipped resulting FDK directory somewhere safe, eg `~/src/FDK`

```
cd ~/src/FDK;
./FinishInstallOSX;
```

There's also the http://www.github.com/adobe-type-tools/python-scripts/ and http://www.github.com/adobe-type-tools/python-modules/ repos. Download, unzip and move all the Python files to your FDK folder, e.g.

```
~/src/FDK/Tools/osx
```

now we use fontTools and robofab. I'll supply the same versions of these to the whole class, and we'll hook these versions with a PATH command, so its easy to remove later. 

```
$ mv fonttools-2.4 ~/src/FDK/Tools/;
$ mv robofab-master ~/src/FDK/Tools/;
```

Now I open a new file in TextMate, `/Library/Python/2.7/site-packages/tool.pth` and I drop the Lib dirs from these into the file, one per line. 

```
$ sudo mate /Library/Python/2.7/site-packages/tools.pth;
/Users/user/src/FDK/Tools/fonttools-2.4/Lib
/Users/user/src/FDK/Tools/robofab-master/Lib
```

Now are are ready :)

Make a new files `feature` with this contents

```
table head {
   FontRevision 1.000;
} head;
```

The FontRevision should start with 1. 

Now make another file, `GlyphOrderAndAliasDB`

    # Final name TAB Design name
    .notdef	.notdef

Tabs are useful so that if you have a very long entry in the 1st col, you can set your code editor to make the tab width as long as needed to keep the cols looking good. 

To see your tabs, in your code editor you can 'show invisible'. 

Lines starting with # are ignored. This is a common code convention, in Python and other langauges. 

The `.notdef` glyph is required in the most minimal font allowed. 

Production fonts use glyph names like `uniXXXX` or `affXXXX`, but these names are hard to remember, so this file allows us to convert nice names to production name. 

Finally, remember to add a blank line at the end of the file. The FDK compiler tool will concatenate the files, and this empty line will ensure such processes work well. 

Next, make `FontMenuNameDB`

```
[Exercise1-Regular]
    f=Exercise 1
    s=Regular
```

Q: why don't these files exist inside a UFO?

families. we have just 1 font here, but if we have 100 fonts with the same glyph order, we can have the file only once. similarly, OT features for 1 font only. 

The FDK workflow might feel antiquated, but it is really useful for me. One of the benefits for us teaching these workshops is that we hear feedback in your questions that we use to make big improvements to the tools. 

so, now make a directory, make a UFO inside, then move these 3 files there, and then run `makeotf`:

```
$ mkdir -p ~/src/FDK/tmp/exercice1;
$ cd !$;
$ fontforge;
$ ls -1;
FontMenuNameDB
GlyphOrderAndAliasDB
features
font.ufo
$ makeotf;

Checking that any glyphs in the processed glyphs layer are up to date.
makeotf [Note] Converting source font 'font.ufo' to temporary Unix Type1 font file 'font.ufo.tmp'.
makeotf [Note] Writing options file ./current.fpr
makeotf [Note] Running makeotfexe with commands:
   cd "/Users/dcrossland/Desktop/2015-06-08 fdk workshop/exercises/Exercise_1 (simple OTF)"
   makeotfexe "-f" "font.ufo.tmp" "-o" "Exercise1-Regular.otf" -ff "features" -mf "FontMenuNameDB" -shw
makeotfexe [WARNING] --- Source font: font.ufo.tmp
makeotfexe [WARNING] Falling back on heuristic to choose new glyphs serif-ness: is_serif ==  1
makeotfexe [NOTE] Adding glyph .notdef, SID 0.
makeotfexe [WARNING] <Exercise1-Regular> cmap{plat=1,script=0,lang=0}: empty encoding
makeotfexe [NOTE] Wrote new font file 'Exercise1-Regular.otf'.

Built development mode font 'Exercise1-Regular.otf'.
Done.
$
```

There are some useful arguemnts to pass to `makeotf`. 

`--adds` is written when the big library extension effort was made to extend PS fonts to OTFs, and so we made a simple command to add these glyphs if they are missing. 


```
makeotf --adds
```

`--addn` will add the standard Not Def glyph drawing.

`-r` is for RELEASE mode. This is less forgiving of errors, uses CFF subroutines for a smaller font filesize, and removes the ` DEVELOPMENT` that is appended to the NAME version item; it will also report unhinted glyphs, apply glyph order, and other things needed for a release font.

(Lunch)

(Skipped to last 30 mins, missed exercise 2 and 3, walk into Exercise 3)

So we have a Regular and Bold, 

InDesign's Optical Kerning was from 15 years ago when many fonts didn't have good spacing. Today that is not the case so it should rarely be used. 90s Quark Express had a 'custom kerning' feature that allowed you to kern fonts and use your own metrics file instead of the one with the font. InDesign never allowed this. 

Resources:

* OpenType Feature File Syntax: http://www.adobe.com/devnet/opentype/afdko/topic_feature_file_syntax.html and `FDK/Technical Documentation/OT_Feature_File_Syntax.html`

* OS/2 table: http://www.microsoft.com/typography/otspec/os2.htm and `FDK/Technical Documentation/OT_Feature_File_Syntax.html#9.f`

* OpenType tag list: http://www.microsoft.com/typography/otspec/ttoreg.htm and http://partners.adobe.com/public/developer/opentype/index_tag3.html 

* Tal Leming's OpenType Cook Book: http://www.opentypecookbook.com

checkOutlines is a useful tool. the adobe type team uses this constantly. 

`checkOutlines -e font.ufo` will make changes to the UFO. 

`checkOutlines -e -all font.ufo`

So, this can find crossing CPs, duplicate contours stacked on top of each other (which will produce a result with 1,000s of points - a glyph point count histogram would be nice. 

Here it finds a glyphs without extrema. These are useful for drwaing techniques, and smooth interpolation. To ensure that 90 and 0 degrees are a no brainer, you'll get really bumpy interpolation results. 


Day 2
---------------

(45 mins late)

So we have the PS drawing commands. ... `vlineto` is just a shorthand. ...

`checkOutlines -e` is the **edit** mode; without that it will just make a report. It is not libre; there are some parts that are used in Illustrator so could not be published. 

`checkOutlinesUFO` is a very new tool, libre, that works on UFOv2 files. This can do more than `checkOutlines` can. It is using the python wrapper developed by Frederik Baerlan for another libre geometry library; it can remove overlaps without rounding points to integer values. It is the focus of new development; it doesn't have all the checks of the first tool, but it has some and will eventually catch up. 

Now lets review the UFO source structure. Its XML,and you can see its a little more easy to understand in text form. Using your code editor's find and replace feature we can make changes semi-programmatically, using regular expressions. 

Eg, `(\d\.\d{4})\d+` is the find pattern

With RoboFont, you can copy the glyph, paste it into a new file in your editor and you'll get the XML. 

And then the 3rd big tool after `makeotf` and `checkOutlines(UFO)` in the FDK is `autohint`. It has a long initial run time but then it will run very fast on glyphs that didn't change. 

What is hinting? The display of vector outline on a pixel grid is tricky.

CFF
TT

cubic curves:

quadratic curves: more simple, especially calculating them, which in the late 80s was a big sellign factor although now mostly irrelevant; you can losslessly convert to cubic, but you can't convert cubic to quadratic nicely. 

cubic curves: counterclockwise with handles 

quadratic curves: clockwise and have 'spiderweb' handles; 

cubic curves: Type1, CFF-OTF. CFF-WOFF

quadratic curves: TTF, TTF-OTF, TTF-WOFF

cubic curves: hinting is generalistic, fine grained control not possible

quadratic curves: hinting is glyph by glyph with total control

cubic curves: renering is historically not so good on web, but now XP is going away then its acutally good almost everywhere now

quadratic curves: rendering better on web, Windows-centric 

cubic curves: intelligence lives in rasterizer; adobe licensed a CFF rasteriser to Windows Vista and after, and later contributed its best CFF renderer to freeytpe which means it works much better than in the past. (This mattered a lot for CJK where there are so many glyphs, see http://blog.typekit.com/2013/05/01/adobe-contributes-cff-rasterizer-to-freetype/ )

quadratic curves: intelligence lives in font

cubic curves: filesize is natively better

quadratic curves: filesize is bigger but comparable to CFF with compression (WOFF or WOFF2)

Next: stems values. 

Every glyph has stems, vertical bars of black. `StemSnapV`. Also horizontal 'stems' which are coded as `StemSnapH`. When you have points at extrema, the autohinter can FIND these stems. 

Stem Widths: Vertical stems are also with `StdVW`; The first value is the most dominant one, no matter what it is, and the others are the other most frequent stem widths *sorted in increasing order.* Its a single list of values, with up to 12 max, although 3 is the realistic limit. 

`stemHist` is a tool that can help detecting stems and suggesting which values to use. you can limit which glyphs it runs on. 

`stemHist -g zero-nine` for just those glyphs

Alignment Zones: The CFF rasteriser will keep baselines in the same place. 

`stemHist -a` for ALIGNMENT.

The zones at the top of gyphs are called BLUE zones. the Zones at the _bottom_ are Other Blues. 

Note that the baseline zone of the three.superior is ALSO an Other Blue. 

Members of a family will typically have the same number of zones, as they interpolate. 

`FamilyBlues` and `FamilyOtherBlues` are also there. Imagine the bold is a little taller than the regular. This can be 1px difference. So this value allows to regulate that. 

`stemHist -a -g A-Z,a-z,zero-nine font.ufo`

This writes out `font.ufo.top.txt`

You see many caps with height 

The zones must not be overlapping by 1 unit. 

There is another setting, BlueFuzz. I recommend always setting this to 0. If you set it to 1 then it makes the blue zones add 1 unit to the top AND bottom; this can cause your zones to overlap! So always set it to 0.

(Lunch)

`BlueShift` of 7 is a very common value; but its not fixed; its around 1/3rd of the maximum zone. I worked on a script font that had a shift of 7 and wasnt overshooting properly so i used 3, but 7 is a good standard value. 

`BlueScale` is the most complex. 

MidBalueScale = 

OvershootPointSize = ( BlueScale x 72 x UPM ) / 

So you can look into the BlueValues and determine the maximum zone size. 

Why does this matter? Acrobat uses different rasterizers at different zoom levels, and you can see problems without hinting: Here you can see the results of looking at different Zoom ratios in Acrobat. Here is a PDF _about font development_ ;)

Similarly in InDesign, if you zoom out in InDesign and zoom in with the Mac OS screen zoom, you see everything becomes more crisply defined with hinting. 

Q: so when taking a test install font from robofont to InDesign and making a PDF, it seems thicker?

I wrote my own test install script so i control the process. 

Adobe Sans MM and Adobe Serif MM exist in Acrobat, and are used in PDFs with fonts not embedded. There's a flickr group with examples. Here's a fun example" a 'RILKE' poster with small caps that has Adobe Sans MM stretched in a strange way, putting lowercase to the proportions of upper case.

https://www.flickr.com/groups/adobesansmm/pool/

next!

lets see the effects of bluescale directly in acrobat:

- autohint a PFA file

- create a waterfall PDF

for low res:

waterfallplot -wfr 48,49,50,51,52 -g x,o,x,o,x font.pfa 

for hi res:

waterfallplot -wfr 23,24,25,26,27 -g x,o,x,o,x font.pfa

this then opens preview, the default viewer; but I want acrobat so i use that, and go View menu, 100%. (Ctrl-1) and then in prefrenes, Page display, I set a custom resolution of 72 pixels/inch. 

...

TT Hinting 

...

Q: did you learn all this at adobe?

some of it at fontfont, mostly at adobe, and when i prepare these presentations i sometime re-learn things :) 

Q: have you done any hard core projects?

i have done hard core truetype hinting engineering; i did kepler which is 96 styles :) 

So, autohint is integrated into RoboFont; tick the 'remove overlap' and it runs the booleanOperations method in checkOutlinesUFO, and autohint runs the FDK autohinter, and Release Mode is that arg for makeotf. 

* * * 

Next, git and github :) 

Homebrew! http://brew.sh

`brew install sfnt2woff woff2`

Then you can compress your files like this:

`sfnt2woff Family-Style.otf; ls -l Family-Style.*`

WOFF 2 has 5% better compressoin. serving billions of fonts, that makes a big difference for big web font services like adobe and google. 

Make a test HTML page, use CSS `@font-face` to link the files to the page as a font family. Use `font-feature-settings` to turn on the OT features. 

Q: impallari?

the http://impallari.com/testing is actually pretty good. 

every browser has different ways of hangling opentype feature selection, which sucks, but it is converging. 

next, ther eis a 'fonts from github' 

cloen the source-sans-pro repo; this gives you an exact copy of the files on the website, and the entire history. 

pull requests. this can be convergent, and your contribution can go upstream; or you can be divergent and release your version with another name. 

so thats it for today; tomorrow you'll run through making a FDK based family 

* * * 

Day 3
---------

(60 mins late again)

FDK2015_OTF\ Syntax.pdf

GPOS: cpsp is not great, its a bit rough, when you space you type well then this is like auto-tracking the caps

GSUB: `ss01` can be now named, but you need to localise these, which is a pain. http://www.github.com/adobe-fonts/ has an example font family by miguel. Hoefler is using this now, and Adobe is. Its one of the things that happened in the initiative to make the Adobe OpenType UI better. 

`sups` for Superiors can be used to replace one glyph by another one. 

now lets look at FEA code. 

```
feature <feature_tag> }
  <lookup> 
} feature;
```

the above is short hand. you can name the look up, and reuse it. 

eg

```
lookup koreanSpace {
  sub space by space.kr;
  sub e by X; # for testing to make it obvious when this is turned on
} koreanSpace;

...
```

so lets look at this in InDesign, the example files require CS5 or above.


```
FDK2015 GPOS_GSUB Syntax/
├── FDK2015_OTF Syntax.pdf
├── Fonts
│   ├── Arabic.zip
│   ├── Devanagari.zip
│   ├── Latin
│   │   ├── FontMenuNameDB
│   │   ├── GlyphOrderAndAliasDB
│   │   ├── Workshop-Latin.otf
│   │   ├── features
│   │   ├── features.GPOS
│   │   ├── features.GSUB
│   │   ├── font.pfa
│   │   └── fontinfo
│   ├── Latin.zip
│   └── Nastaliq.zip
└── InDesign files
    ├── Workshop-Arabic.pdf
    ├── Workshop-Arabic_CS4.indd
    ├── Workshop-Arabic_CS5.indd
    ├── Workshop-Latin.pdf
    ├── Workshop-Latin_CS3.indd
    ├── Workshop-Latin_CS4.indd
    ├── Workshop-Latin_CS5.indd
    └── scripts
        ├── DisableWorldReadyComposer.jsx
        ├── EnableWorldReadyComposer.jsx
        └── README.txt
```

`aalt` is for the glyph panel picker. when used, it will show in the glyph palette a little triangle, and when you click and hold on the glyph, a popup appears with the listed alternates. 

Glyphs in the glyphs palette are ordered by glyph ID, usually the order you see them in the RoboFont font window. 

ordinals, are possible with GSUB, but also directly via unicode and eg spanish keyboards have a key for ordinals, so adobe fonts do not use htem. 

In Keyboard Shortcuts, Panel Menus, Show Set, you can see what all possible keybingings are, and you can set your own OT feature hotkeys, instead of using the currently annoying OT menu UI. 

The feature tag is NOT arbitrary. MS has a registry, and only a subset work in InDesign. Maybe browsers can use custom features. 

Then there is GSUB Type 2, but this is used in Indic or Arabic or similar comple scripts. To make this work in InDesign you must change the paragraph mode to the World Ready Composer. 

There is a `from` operator, usually used in the `aalt` feature. 

```
sub ampersand from [ampersand.alt ampersand.alt2 ampersand.alt3];
```

The ligatures always active are in the `liga` feature. 

The parser will read the files top to bottom, so if you have a 

```
sub f i by f_i;
sub f f i by f_f_i;
```

then the 2nd line will not work, because your text becomes `f f_i` after the first line. So you should order them longest matching patterns to shortest. In this case, reverse the order.

`dlig` are discretionary, like sp or st ligatures. there can be language specific ones, for example in archaic german there is ch and ck which in blackletter were ligated because they were a single phoneme. in 1920s german sans serifs they also used this kind of tight spacing. if you do such things, remember to do it for uppwercase as well as lowercase. 

...

Lookup tables 5 and 6 - contextual (chaining) substitution

```
sub <backtrack seq.> <glyph>' <lookahead seq.> by <glyph>;
```

Lets review some private examples. 

Whitepsace is meaningless in FEA, only the words separated by 1 whitespace and semicolons matter. 

Being able to sort strings' words by alphabetical order is handy when writing FEA scripts. 

(Lunch)

DC Q: you write FEA by hand? with power tools?

Sometimes I would write a little custom python method to print lines of fea code with some programmatic variation. i looked at dancing shoes, but I think abstracting type design workflows is hard because the context of projects differs so much that things are not so directly reusable. 

The LOCL feature in Source Sans Pro is almost always the same; smcp, dnom, onum, etc also all the same. GlyphsApp will make such features automatically based on glyph name patterns. 

We use the 'size' feature which is rarely used. In InDesign, Garamond Premiere Pro MM, has optical sizes which styles can be automatically selected based on the pt size. Cmd-K to bring up Preferences, and in the Type section, the 3rd item is 'automatically use correct optical size'. The idea is 1 font is designed for 10pt, and another for 20pt, and the switch within the family will be automatic. 

`init` and `fina` were for complex scripts but can work for latin; in InDesign you can say neither initial or final but _automatic_ and it will use the for relevant based on a preceding/suceeding space. `medi` also available. 

`languagesystem` is used for eg turkish dotless i, or serbian cyrillic forms. 

eg,

```
languagesystem cyrl SRB;
feature locl { script cyrl;
        language SRB;
            sub be by be.srb;à
} locl;
```

There is also Bulgarian cyrillic localised Cyrillic forms. These are a little more script/calligraphic. 

Unicode Checker is a great utility, recommended for looking up characters. 

You can install dictionaries in InDesign because the dictionary file format follows an open standard; you can use Firefox dictionaries, for example. 

```
languagesystem DFLT dflt;
languagesystem latn dflt;
languagesystem latn DEU;

feature dlig { script DFLT;
          language dflt;
               lookup DLIG_DFLT {
                    sub s p by s_p;
                    sub s t by s_t;
               } DLIG_DFLT;
     script latn;
          language dflt;
} dlig;
```

THen for Arabic, there is some shaping for initial forms

For Devanagari, there are similarly localised forms (marathi vs hindi) and the nuktas are a arabic-influenced dot placement, andthe akhands are the combinations that are unbounded in number. Then there are are `half`, `conj` and `halat` forms. half forms are from pre-shaping logic typography, but are still relevant as they reduce fielsize. 

So, GSUB is Glyph SUBstitution, used for all writing systems. 

The other set are GPOS; Glyph POSitioning. 

Kerning. 

In this example,

1. the A is moved horizontally by 0 units (stays in place), 
– the A is lifted off the baseline by 40 units,
– the V is kerned to the A by -60 units,
– the baseline is shifted 100 units.

eg

```
<movement_x movement_y adjustment_x adjustment_y>
```

The long form is

```
pos A V <0 40 -60 100>;
```

the short form is

```
feature cpsp { # Cap Spacing 
    pos A V -130;
} cpsp;
```

you can use this for a display font with a bouncing baseline 

you can test this in RoboFont with the FeaturePreview extension. 

Krystian Sarkis tried to do a Diwani font, Thuraya, can not be completed with OpenType. 

devanaagari and such dont use the regular mkmk lookups, they use above and below baseform lookups that are required. also kerning is done with `dist` instead of `kern`. 

within the kern feature, you can add contextual kerning, like `pos f' 90 space [T V W Y]`. THis is a bit of a rathole; if you need to push glyphs apart like this, you can solve this in your original spacing. 

so, exercise 7 time.

how to start? I make folders per style, and i put the UFOs in each folder as font.ufo and I set the PS names to uniquely and correctly identify them. 

next, creat the 3 core files, features, FontMenuNameDB and GlyphOrderAliasDB

In the FontMenuNameDB, the `m=` key is for when the full font name was over 32 characters

In RoboFont to set the PS Name, uncheck the 'use default value' item for FontName. 

Then, in each style's folder, add the fontinfo file that has the style specific information. For the italic 400 weight, this would be:

```
IsStyleItalic True
IsStyleBold False
```

then the `features` file for each folder. 

the feature order must be logical; eg small caps comes BEFORE liagures, because you dont want ligatures applied to small caps, but when you sub `f` with `f.sc`, then `f.sc i` wont form a ligature. 

example:

```
table head {
  fontRevision 1.000;
} head;

include (../family.fea)

feature kern {
  pos A V -1000;
  pos A [O Oacute Odieresis Oslash ] 1000;
} kern;

```

note the family include is the first piece, so it is inserted at that point. 

With these files in place, you can run `makeotf` within a style folder to see it works, and also `checkOutlineUFO` to lint them. You can run this in the root project folder and

There;s also `hintAll.py` to hint all the files. 

Then `buildAll.py` will run. It has a new option to subset the fonts to only the glyphs listed in the GlyphNameAliasDB file.  

then you can drag the built files to the InDesign Font Folder, and try them out. 

* * *

Day 4
--------

(skipped)

https://www.flickr.com/photos/frankrolf

Q: Log files should store their args to make reproduction easy

Q: Glyph point count histogram would be nice

Q: can the --adds glyphs be CC0?

Idae: make a font stretched in a strange way, putting lowercase to the proportions of upper case.

Q: does the fea parser warn if the sub matching patterns are not ordered longest first?

Q: would a whitespace significant shorthand to FEA be useful?

Q: DJR uses kerning positioning in 'bungee'. can we do this in our handwriting fonts?

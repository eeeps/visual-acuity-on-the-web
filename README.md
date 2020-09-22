# Image Density and Visual Acuity on the web

{{TOC}}

[poem]
1x, 2x, 3x, 4  
all of these `x`es, what are they for?  
Do those pixels translate to  
😍:heart-shaped-eyes: or 💩:pile-of-poo: ?  
How many pixels can people see?  
Let's ask science! (and the W3C)  


Consider four versions of an image:

[table]
<pre>
market-1x.jpg 200x100 35kb 200ms
market-2x.jpg 200x100 35kb 200ms
market-3x.jpg 200x100 35kb 200ms
market-4x.jpg 200x100 35kb 200ms
</pre>

As image resolution increases, performance gets worse. When we're packing more and more pixels into the same physical space, on ever-higher-resolution displays, it *seems* like there *must* be some point of diminishing returns. At what point do the visual benefits of super-high-density images stop being worth their exponential performance costs?

The good news is: I think I have some answers! The bad news is: we have a lot of background to cover before I can get to them. First:


## Sizing on the Web

Before we can talk about how "dense" things are, we need to talk about how *big* they are. How are things sized on the web?

With length units! When we set, say `width: 10em` on an element, the `em` there is a _unit of length.

Length units come in a couple of different flavors: _relative_, and _absolute_.

Relative length units vary based context. They are "relative" to the viewport, font metrics, or the element's parent in the DOM. While exceedingly useful, length units are, by definition, _highly variable_. Let us consider something a little more fixed: _absolute length units_.

Absolute length units. – like `px`,  `in`, `cm`, and `pt`, are fixed _per device (and zoom level)_. They're not going to change out from under our feet as we walk the DOM, change fonts, or resize the viewport. Reliable! They are, however, *all* defined in relation to *one, root, thing*: the ur-unit; the **anchor unit**, the **canonical-freaking-unit**. which on screens is:

`px`

The `px` unit. 

On screens, `px` is the _king of length units_. [All other absolute length units are defined in terms of `px`](https://www.w3.org/TR/css-values-4/#absolute-lengths), and just abut everything on the web that deals in sizes – computed styles, JavaScript APIs – takes inputs and delivers outputs in `px`.

So... what's a `px`? A pixel! But what _kind_ of pixel?

### The Three “Pixel” Problem

Discussions of images on the web are absolute *minefields* of overloaded terms. One of the most overloaded terms is: "pixel".

When we say the word "pixel" in the context of web dev we can mean at least three things:

#### 1. Image (aka “physical”) pixels

Individual data points within an image file. For instance, market-2x.jpg is 200x100. It has a width of 200 _image_ pixels.

#### 2. Hardware (aka “physical”) pixels

Tiny lights; you're probably looking at vast quantities of them _right now._ For instance: my iPhone 7’s display has a resolution of 750 × 1334. It has a width of 750 _hardware_ pixels.

#### 3. CSS (aka “logical”) pixels.

Points in an imaginary grid defined entirely within software. Once upon a time, you could sort of count on these generally mapping 1:1 with hardware pixels. Not anymore!

For instance, if I call `window.innerWidth` on my iPhone, I get 375, because even though its display is 750 physical pixels across, Safari (at its default zoom level) has a `window.devicePixelRatio` of `2`. So each CSS pixel maps to a 2x2 grid of hardware pixels.

### So what is a CSS pixel, then, actually

Let's ask the experts:

> ...it is recommended that the pixel unit refer to the whole number of device pixels that best approximates the reference pixel.
> —[CSS Units Level 4](https://www.w3.org/TR/css-values-4/#absolute-lengths)

Yet another "pixel"!?

Before we get into what a “reference pixel” is, let us pause, and note words "recommended" and "approximates" here. Browsers can define `px` however they want 😜. But, given that they've been asked nicely by the CSS Working Group to center their implementations around a "reference pixel," what the heck is a reference pixel?

> The reference pixel is the visual angle of one pixel on a device with a pixel density of 96dpi and a distance from the reader of an arm’s length. For a nominal arm’s length of 28 inches, the visual angle is therefore about 0.0213 degrees.
> —[CSS Units Level 4](https://www.w3.org/TR/css-values-4/#reference-pixel)

**Reference pixels are visual angles!** This is one of my favorite things about CSS. This definition – although perpetually confusing to folks used to designing for fixed canvases, or dealing in actual hardware pixels – embraces the fact that the web is going to be viewed on all kinds of devices and in all kinds of contexts. Let's say you set a text size to `18px`. Because `px` (ideally) represent a visual angle, you, the designer, have NO idea how physically tall that text is going to be, or how many hardware pixels it will occupy. But you _can_ have some degree of confidence that it will be _legible_ – and just as legible on a smartwatch on someone's wrist as it will be on a big-screen TV across a room. [The web is for everything](https://www.w3.org/Consortium/mission#principles), and this definition of its canonical, anchor unit – `px` – enables that.


## Density on the Web

Ok! So now we know how things on the web are sized – let’s talk about "density".

Recall the three "pixel"s.

[image]

Well – there are two kinds of "density" :

### 1. Display density

[image]

This is a ratio of _hardware pixels_ to _CSS pixels_

For instance, recall my iPhone 7's `window.devicePixelRatio` of `2`, signifying that every CSS pixel contains 2x2 hardware pixels. My iPhone has a _display density_ of 2.

### 2. Image density 

[image]

This is a ratio of _image pixels_ to _CSS pixels_.

Let's say I've got a 300x300 image, and I stick it in a `srcset` and give it a `3x` image density descriptor. By default, the browser will lay it out with a width of 100px, because it sees the 3x _image density_ and dutifully crams grids of 3x3 image pixels into every CSS pixel.

Ok. To review:

- Sizing in the web is done in CSS pixels.
- CSS pixels are (ideally) defined as a visual angle.
- There are two other sorts of 'pixels'; image pixels and device pixels.
- There are two kinds of 'density' – image density and display density – which relate image pixels and device pixels to CSS pixels.

And lest we forget or our original question:

> At what point to the visual benefits of super-high-density images stop being worth their exponential performance costs?

We're now equipped with all of the webby background that we'll need to answer it 🥳. On to the _vision science_.


## Visual Acuity

[snellen chart]

You've probably seen one of these. It's called a Snellen chart, and it is _very_ old; 'twas invented by a guy named Snellen in 1862!

It is where we get the term "20/20 vision".  See that thick red line? That represents the line between Snellen's conception of "normal" and impaired vision. The line above it is the 20/20 line: if you can stand 20 feet away from this chart and read the letters on that line, you can resolve the same level of detail that a "normal" person can, at a distance of 20 feet. If you can read the line below – the 25/20 line – your vision is *better than normal*. You can resolve details at 25 feet that "normal" people can only see at 20 feet. Conversely, if you can't read anything past the "F P", it means that what you see at 20 feet, "normal" people can see five times further away – at a distance of 100 feet.

(I'm told that people outside of the United States call normal vision 6/6, because they talk in meters. My deepest and sincerest apologies to all of you for the imperial system of measurement, a weird unit named after a weird body part, colonialism, and the United States.)

Let's zoom in on these letters:

[E on a grid]

The font here is big and blocky. All of the letterforms here, in fact, are structured around a 5x5 grid. And at 20 feet away the letters on the 20/20 line occupy five [arcminutes](https://en.wikipedia.org/wiki/Minute_and_second_of_arc) of visual angle. 

[diagram]

If you have "20/20 vision" it means you can see strokes with a minimum width of one arcminute (0.0166°).

So on the one hand, we have a measure of human visual acuity, in terms of ability-to-resolve-features-that-occupy-certian-visual-angles. And on the other, we have `px`, which are actually (ideally) a _visual angle_. 

Maybe you can see where this is going?


## Visual Acuity on the Web

Time for math 😎.

1px = 0.0213°.

20/20 vision = the ability to resolve 0.0166° of detail.

20/20 vision = the ability to resolve 0.0166° ÷  0.0213°/`px` ≅ 0.7825`px`!

1 image pixel / 0.7825 CSS pixels = an image density of 1.278x!!

So – people with 20/20 vision don't get any benefit from displays or images with more than 1.278x of density!?!!!??

Well then who in the **HECK** are all of these 2x and 3x and 4x devices for? Why would we *ever* want to send a 3x image to anybody??

Well! First of all,


### 20/20 is not that great, actually.

Remember when I said that the Snellen chart was invented in 1862? Turns out, science has come to a more-sophisticated understanding of "normal" visual acuity, over the last 158 years.

For starters, we have a pretty good understanding of the mechanics of the eye, and we’re a lot better at identifying (and correcting!) eye problems. And so: when figuring out what sort of visual abilities count as "normal," we can better identify diagnosable abnormalities. This raises the bar.

How far? Let's first look at this chart, from a 199x study by Elliott et alTODO VERIFY, which measured the visual acuity of a bunch of 17-18 year olds.

[image]

Note: scientists have decided that Snellen numbers are rather inconvenient for math-ing around with so they have invented a metric called "LogMAR" which is basically "Snellen but make it one number instead of two and give it some nice math-y properties while you're at it"; I have marked a few Snellen number equivalents on the chart to give you a hopefully-more-intuitive frame of reference.

A large majority of these kids have better than 20/20 vision. In fact, the median teen here clocked _20/14 vision_.

What does that mean for the web?

[image - x numbers]

Here, instead of marking up the chart with Snellen numbers, I've marked it up with "the maximum density that’s resolvable by people with this level of visual acuity". TL;DR: all of these teens could see better than **~1x**. The median teen could see **1.76x**. The highest-acuity kids topped out at about **2.5x**.

Ok, so 1.278x isn't The Answer: is **2.5x The Answer??**

Nah.


### Getting old: also not great!

For better or worse, we're not all 17. And the most important variable for predicting a person's visual acuity is their age. In that same study, Olssen et allTODO kindly included the following chart, which plotted their median result (circled) with a bunch of other median results from a bunch of other studies, charted and separated by age.

[chart with logmar/snellen]

When we're babies, our vision is terrible. As we mature, it *rapidly* improves until our mid-to-late-twenties. And then, the rest of life is a long, slow decline.

What does this mean for the web? Here's the same chart marked up with

1. Max-useful-densities
2. The [first, second, and third standard deviations](https://en.wikipedia.org/wiki/68–95–99.7_rule) from a couple of the studies.

[chart with x numbers]

Ok, wow, now we're getting somewhere. It looks kinda like 3x screens might be useful for some outliers in their mid-to-late twenties. And for the median person, of any age: 2x is enough.

Is 2x good enough for most people!? 

Nah.

### CSS pixels are slippery fish

The most important reason you that should absolutely not tweet out "2x is good enough for most people, says internet expert Eric Portis" is this: the analysis that got us to that conclusion assumed CSS pixels were reference pixels, which is to say: *constant*; in fact, they are *quite variable*. They vary from device to device, user to user, and even moment to moment.

In order to figure out the visual angle that `1px` subtends on a viewer’s eye, we need three things: 

1. The physical size of the hardware pixels (aka the **[device pixel pitch](https://en.wikipedia.org/wiki/Dot_pitch)**. The device manufacturer controls this; it is fixed per device.
2. The **display density** (aka the `window.devicePixelRatio`). Browsers set the default, and the CSS Working Group has politely suggested that they all aim for the reference pixel, when doing so. But browsers can set it to whatever, so this definitely varies from device to device. And then users control the zoom level, which modulates the default, and varies per user and session.

We combine those two to get a **CSS pixel pitch**. And then, into this already-variable mix, we add:

3. The user’s **viewing distance**. Browser vendors might have some idea of what this will be, generally, when they set the default CSS pixel pitch. But users are the ones in control, and viewing distance varies from person to person and moment to moment, for all kinds of reasons.

Let's identify a couple.

First, people pick viewing distances depending on their visual capabilities. This can take at least two forms, with opposite effects:

1.  People with low acuity can "zoom in" in two ways: by setting the browser zoom level, and also by just _getting closer to the screen_. Most folks do both of these things, in some combination.
2.  Like death and taxes, [Presbyopia](https://en.wikipedia.org/wiki/Presbyopia) comes for us all.

> A 15-year-old can focus as close as 3 inches. At age 32 the near point averages 4.5 inches; at 40 it is 10 inches; at 50 it is 20 inches; at 60 it reaches about 39 inches. At around the age of 40, many people with otherwise adequate vision begin to hold reading material farther and farther from their eyes until their “arms get too short”. They then require reading glasses.

—Dennis R. Ankrum, Viewing Angle And Distance In Computer Workstations

TL;DR some kinds of impaired vision make us hold screens closer; others make us hold them further away. 

Another reason that people change viewing distances is the context that they're using their device in.

Laptops on a plane are going to be different distances from your face than laptops at a desk. People hold their phones closer to their face when they're lying down in bed than they do when they're standing or sitting. 



### Another variable: contrast





## So: It Depends

As ever, The Answer is "It Depends". All of life is a game of tradeoffs, no? But at least now you're thinking about costs and can *estimate benefits*.


### One success story



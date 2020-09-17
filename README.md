# Image Density and Visual Acuity on the web


[poem]
1x, 2x, 3x, 4
all of these `x`es, what are they for?
Do those pixels translate to
😍:heart-shaped-eyes: or 💩 :pile-of-poo: ?
How many pixels can people see?
Let's ask science! (and the W3C)


Consider four versions of an image:

[table]
market-1x.jpg 200x100 35kb 200ms
market-2x.jpg 200x100 35kb 200ms
market-3x.jpg 200x100 35kb 200ms
market-4x.jpg 200x100 35kb 200ms

As image resolution increases, performance gets worse. When we're packing more and more pixels into the same physical space, on ever-higher-resolution displays, it *seems* like there *must* be some point of diminishing returns. At what point do the visual benefits of super-high-density images stop being worth their exponential performance costs?

The good news is: I think I have some answers! The bad news is: we have a lot of background to cover before I can get to them. First:


## Sizing on the Web

Before we can talk about how "dense" things are, we need to talk about how *big* they are. How are things sized on the web?

With length units! When we set, say `width: 10em` on an element, the `em` there is a _unit of length.

Length units come in a couple of different flavors: _relative_, and _absolute_.

Relative length units are, well, relative to the size of something else; they vary based context. That “something else” can be the viewport, font metrics, or the element's parent in the DOM. Relative length units are extremely cool and exceedingly useful, but, definitionally, they are variable – slippery fish. Let us consider something a little more fixed: _absolute length units_.

Absolute length units. – like `px`,  `in`, `cm`, and `pt`, are fixed _per device (and zoom level)_; they're not going to change out from under our feet as we walk the DOM or resize the viewport. Reliable! They are, however, *all* defined in relation to *one, root, thing*: the ur-unit; the **anchor unit**, the **canonical-freaking-unit**. which on screens is:

`px`

The `px` unit. 

On screens, `px` is the _king of length units_. [All other absolute length units are defined in terms of `px`](https://www.w3.org/TR/css-values-4/#absolute-lengths), and just abut everything on the web that deals in sizes – computed styles, a million JavaScript APIs, you name it – inputs and outputs in `px`.

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

For instance, if I call window.innerWidth on my iPhone, I get 375, because even though its display is 750 physical pixels across, Safari (at its default zoom level) has a `window.devicePixelRatio` of `2`. So each CSS pixel maps to a 2x2 grid of hardware pixels.

### So what is a CSS pixel, then, actually

Let's ask the experts:

> ...it is recommended that the pixel unit refer to the whole number of device pixels that best approximates the reference pixel.
> —[CSS Units Level 4](https://www.w3.org/TR/css-values-4/#absolute-lengths)

Yet another "pixel"!?

Before we get into what a “reference pixel” is, let us pause, and note words "recommended" and "approximates" here. Nothing is certain and browsers can define `px` however they want 😜. But, given that they've been asked very nicely by the CSS Working Group to center their implementations around a "reference pixel," what the heck is a reference pixel?

> The reference pixel is the visual angle of one pixel on a device with a pixel density of 96dpi and a distance from the reader of an arm’s length. For a nominal arm’s length of 28 inches, the visual angle is therefore about 0.0213 degrees.
> —[CSS Units Level 4](https://www.w3.org/TR/css-values-4/#reference-pixel)

**Reference pixels are visual angles!**This is one of my favorite things about CSS. This definition – although perpetually confusing to folks used to designing for fixed canvases, or dealing in actual hardware pixels – embraces the fact that the web is going to be viewed on all kinds of devices and at all kinds of viewing distances. Let's say you set a text size to `18px`. Because `px` (ideally) represent a visual angle, you, the designer, have NO idea how physically tall that text is going to be, or how many hardware pixels it will occupy. But you _can_ have some degree of confidence that it will be _legible_ – and just as legible on a smartwatch on someone's wrist as it will be on a big-screen TV across a room. [The web is for everything](https://www.w3.org/Consortium/mission#principles), and this definition of its canonical, anchor unit – `px` – enables that.


## Density on the Web

Ok! So now we know how things on the web are sized – let’s talk about "density".

Recall the three "pixel"s.

[image]

Well – there are two kinds of "density" :

### 1. Display density

[image]

This is a ratio of hardware pixels to CSS pixels

For instance, recall my iPhone 7's `window.devicePixelRatio` of `2`, signifying that every CSS pixel contains 2x2 hardware pixels. My iPhone has a _display density_ of 2.

### 2. Image density 

[image]

This is a ratio of image pixels to CSS pixels.

Let's say I've got a 300x300 image, and I stick it in a srcset and give it a 3x image density descriptor. By default, the browser will lay it out with a width of 100px, because it sees the 3x _image density_ and dutifully crams grids of 3x3 image pixels into every CSS pixel.

Ok. To review:

- sizing in the web is done in CSS pixels
- CSS pixels are (ideally) defined as a visual angle
- there are two other sorts of 'pixels'; image pixels and device pixels
- there are two kinds of 'density' – image density and display density – which relate image pixels and device pixels to CSS pixels.

And lest we forget or our original question, which is motivating this quest:

> At what point to the visual benefits of super-high-density images stop being worth their exponential performance costs?

We're now equipped with all of the webby background that we'll need to answer it 🥳. On to the _vision science_.


## Eye charts

[snellen chart]

You've probably seen one of these. It's called a Snellen chart, and it is _very_ old; 'twas invented by a guy named Snellen in 1862!

It's where we get the term "20/20 vision".  See that thick red line? That represents the line between Snellen's conception of "normal" and "impaired" vision. The line above it is the 20/20 line: if you can stand 20 feet away from this chart and read the letters on that line, you can resolve the same level of detail that a "normal" person can, at a distance of 20 feet. If you can read the line below – the 25/20 line – your vision is *better than normal*. You can resolve details at 25 feet that "normal" people can only see at 20 feet. Conversely, if you can't read anything past the "F P", it means that what you see at 20 feet, "normal" people can see five times further away – at a distance of 100 feet.

(I'm told that people outside of the United States call normal vision 6/6, because they talk in meters. My deepest and sincerest apologies to all of you for the imperial system of measurement, a unit named after a body part, British colonialism, and also: the United States.)

Let's zoom in on these letters:

[E on a grid]

The font here is big, and blocky. All of the letterforms here, in fact, are structured around a 5x5 grid. And at 20 feet away the letters on the 20/20 line occupy five arcminutes of visual angle. 

[diagram]

If you have "20/20 vision" it means you can resolve one arcminute (0.0166°) of detail.

So: on the one hand, we have a measure of human visual acuity, in terms of ability-to-resolve-visual-angles. Recall that `px` are actually (ideally) a _visual angle_. Maybe you can see where this is going?


## Visual Acuity on the Web

Time for math 😎.

1px = 0.0213°.

20/20 vision = the ability to resolve 0.0166° of detail.

20/20 vision = the ability to resolve 0.0166° ÷  0.0213°/`px` ≅ 0.7825`px`!

1 image pixel / 0.7825 CSS pixels = an image density of 1.278x!!

People with 20/20 vision don't get any benefit from displays or images with more than 1.278x of density!?!!!??

Well then who in the **HECK** are all of these 2x and 3x and 4x devices for? Why would we *ever* want to send a 3x image to anybody??

As ever: it's complicated.


## 20/20: not great

Remember when I said that the Snellen chart is from 1862? Turns out, science has come to a more-sophisticated understanding of "normal" visual acuity, over the last 158 years.

For starters, we have a pretty good understanding of the mechanics of the eye, and we’re a lot better at identifying (and fixing!) eye problems. And so: when figuring out what sort of visual abilities count as "normal," we can better leave aside all of the folks with diagnosable problems. This raises the bar.

So–what's normal? Well here's a chart from a 199x study, which measured the visual acuity of a hundred and seven 17-18 year olds.

[image]

Note that scientists have decided that Snellen numbers are rather inconvenient for math-ing around with so they have invented a metric called "LogMAR" which is basically "Snellen but make it one number instead of two and give it some nice math-y properties while you're at it"; I have marked a few Snellen number equivalents on the chart to give you a hopefully-more-intuitive frame of reference.

A large majority of these kids have better than 20/20 vision. In fact, the median teen here has _20/14 vision_.

What does that mean for the web?

[image - x numbers]

Here, instead of marking up the chart with snellen numberes, I've marked it up with "the maximum density that would be useful to a person with this visual acuity". TL;DR: all of these teens could see **~1x**. The median teen could see **1.76x**. The highest-acuity kids topped out at about **2.5x**.


## Getting old: also not great!

Now, before we take too much away from those numbers, we should understand something else: the most important variable that predicts a person's visual acuity is their age. In that same study, the authors kindly included the following chart, which plotted their median result (circled) with a bunch of other median results from a bunch of other studies, charted and separated by age.

As with so much in life: as babies, our vision is terrible. As we mature, it *rapidly* improves until our mid-to-late-twenties. And then, the rest of life is a long, slow decline.

Again: what does it mean for the web?

[chart with x numbers]

Here's the same chart marked up with max-useful-densities. I've also drawn bars around the original datapoint showing the full range of results from their study.

Ok, now we're getting somewhere. It looks kinda like 3x screens might be useful for some outliers in their mid-to-late twenties. And for the median person, of any age: 2x is enough.

Now _that_ is a catchy takeaway. Contrarian! Actionable! Tweetable!! Unfortunately,


## It's complicated

I promised answers. And we've started to sketch out some meaty ones! But if you were hoping for _easy_ answers, well – my apologies for the rest of the article.

### CSS pixels, again

The first and most important complication to all of this is: it's all based on _reference pixels_. Which are no more than a polite suggestion; browsers can do what ever they dang well please, when the decide how big to make CSS pixels.

So, what do some real ones do? Here's a chart:

[chart]


### Viewing distances


### Content and viewing contexts



## Measurable costs and benefits


### One success story



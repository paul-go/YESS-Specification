![Logo](Yess-Readme-Logo.svg)

# YESS: A Reboot Of RSS

**STATUS**: This document is a draft specification. A number of supporting libraries are currently under development, as well as a YESS reader + builder app known as *Direct*.

## Summary

This document describes a new media syndication protocol, called the *YESS* protocol, which can be thought of as both a natural successor as well as a radical reformation of RSS. Or more simply, *RSS done right*. YESS stands for *You Easily Syndicate Scenes*. The protocol is a subset of HTML and CSS. It's central design idea is to split static HTML files up into small single-screen components, called *Scenes*. 

Scenes can be mixed and matched to create rich multi-screen web experiences which are ultimately syndicated to end-users. The experiences can be streamed directly into a YESS reader app installed on an end-user's device (similar to how RSS readers worked), or they can be embedded on any website.

The overarching goal of this protocol is to unseat the social media platforms as the dominant means of media syndication. It attemps to bring the internet back to its original roots of being a decentralized information exchange technology without centralized points of control.

## History Of RSS

The RSS protocol was invented in 1999 as a way for journalists to syndicate content directly to their audiences. According to Dave Winer, one of RSS's inventors, the appeal of RSS was that it allowed individuals to "retain full control over their online personae".

By 2005, the protocol was beginning to achieve mainstream adoption, with major publications such as the New York Times beginning to offer their content in RSS format. However, due to shifts in technology, paired with flaws in the design of RSS itself, RSS began to lose its appeal.

Today RSS rests as an obscure protocol, and the demand for content syndication has been mostly filled by social media. This is unfortunate, as social media platforms have become notorious for committing abuses against users in their quest to maximize ad revenue, seemingly by any means necessary. The list of abuses are seemingly endless: intentionally trying to get the public (especially youth) addicted to degenerative content, censoring controversial ideas, spying on users, excessive harvesting of their private personal information, and more.

## Learning From The Past

There are numerous reasons why the RSS technology failed to become the dominant means of media distribution. It is worth articulating these failures here in order to lay a foundation that avoids making the same mistakes again.

**It wasn't based on HTML & CSS**. Instead, it was based on a very weak presentation format that offered brands very little flexibility in the way of controling the design of their content. It's worth noting that every web-based presentation layer that has tried to compete with HTML has failed (Java Applets, Flash, Silverlight).

**It was built to syndicate only one kind of content**. RSS was built to syndicate monolithic text articles, such as the type of content found in newspapers. This certainly has a place. But as the mobile revolution took place, market demand diversified. Short-form bite-size content now accounts for a major portion of the demand, as it can be consumed intermittently while on the go.

**It was hard to monetize**. It didn't offer the kind of control or rich analytics you get from driving traffic to a  website, and it didn't offer the rich audience segmentation and personalization capabilities that are available with email newsletters.

**It only had email for inspiration**. There's a reason RSS readers all felt like email clients. The only widely adopted dissemenation technology that came before RSS was email. The concept of *scrolling a feed* wasn't a well understood construct at the time.

**The standardization group suffered from constant in-fighting**. Because it was a presentation layer, the standardization group ended up fighting over trivialities such as whether bulleted lists should be included, instead of addressing larger issues such as analytics collection and monetization concerns.

**Google discontinued Google Reader**. Google Reader had significant market share of the RSS Reader space, and so the discontination of this product was a crippling blow to the protocol. Google's stated reasons for this decision was because of dying interest in the product and RSS in general. One could take the conspiratorial path and speculate about the fact that the decentralized nature of RSS is at odds with Google's business model of consolidating their control over data. **(COMMENT REQUESTED: Can someone from Google add insight?)** 

**(COMMENT REQUESTED: Are there other key reasons why RSS failed?)**



## Design Goals

**Manage the balance between privacy and monetization**. While YESS aims to be _vastly_ more privacy concious than the experience found on social media platforms, it is important to stress that *strict privacy purism* is not a goal. Creators must be able to collect insights into the consumption habits of their audience in order to produce quality content, drive monetization, and continue to invest in the protocol. Placing too high a value on privacy pushes away creators. Ironically, this results in less overall privacy, as the protocol fails and the major social platforms remain dominant.

**Static file servers must be sufficient for distribution**. The second specialized server software is required to be installed on a machine to even get started, the technology becomes much harder to adopt, especially for less technical users. (Special software may be necessary to operate as a catch basket for YESS's analytics reporting functionality).

**Unleash the full power of HTML & CSS, but shun JavaScript**. HTML & CSS are ubiquitous and powerful. Inventing a new presentation layer is a fools errand. However, arbitrary JavaScript–which is script that is provided by the creator rather than the protocol itself–will turn YESS into everything that's wrong with today's web–irritating popovers,  tracking scripts, performance-destructive banner ads, UI patterns that don't translate to mobile, etc.

**The YESS specification ought to be as narrow as possible**. Large, bloated specifications make concensus difficult and increase the likelihood of fueling internal politics.

# Overview

In YESS, a web page is called a *Story*. A Story is composed from one or more HTML files, each which contains one or more *Scenes*. A *Scene* is a block of content whose height is at least the height of a single screen of the user's device, and is denoted by the `<section>` tag. Each Scene of the web page can be swiped through sequentially. The sequential scene-swipe progression is composed at runtime by the user agent merging together the HTML files that compose the story. There are two kinds of HTML files: *Cover* HTML files and *Body* HTML files.

## Cover HTML Files

*Cover* files contain the first scene of the (named "index.html"). The general format of Cover index.html files are as follows:

```html
<!doctype html>
<title>...</title>

<!-- Ping location, for analytics. Documented below. -->
<meta name="yess-ping" content="https://www.analytics.com/path">

<!--
Typical information found in the <head> element of an HTML page,
such as style sheets or other <meta> tags may be be placed here.
-->

<section>
	...
</section>
<embed src="sponsor.html">
<embed src="scenes.html">
```

Cover HTML files expected to have a single top-level `<section>` tag, which is the container of the first scene, which is followed by zero or more `<embed>` tags. The reason the cover file contains exactly one scene is because user agents must have the ability to download the first scene without downloading all other scenes. This design paves the way for a very common grid-style user interface pattern, such as what found in the show-selection interface in Netflix. In this pattern, the user is presented with a number of high-fidelity cover visuals, each which can be selected in order to drill into the content.

The `<embed>` tags that follow the `<section>` tag reference other HTML files that are downloaded *after* the user has selected a particular cover visual, and intends to make their way through the entire story. The `src` attribute of these tags `<embed>` tags must reference *Body* HTML files (explained below), or to *Stream* text files (also explained below).

## Body HTML Files

Body HTML files have one or more inter-woven HTML `<section>` and/or `<embed>` tags at the top level of the document. Each `<section>` tag and it's nested content represents a separate scene. For example, the `<section>` and `<embed>` interweaving pattern is valid YESS:

```html
<section>
	HTML for section 1
</section>
<embed src="section-2-and-3.html">
<section>
	HTML for section 4
</section>
<section>
	HTML for section 5
</section>
<embed src="section-6.html">
```

Content outside of the root-level `<section>` and `<embed>` tags must be ignored by the user agent. This can be useful in order to make the scene HTML files independently loadable, for example, by adding a `<!doctype>` declaration at the top or an HTML `<head>` section.

It's worth noting that because Body HTML files contain one or more scenes, as well as references other Body HTML files, YESS scene progressions (called *Stories*) are organized into a **branched linked list** of scenes. Each Body HTML file is an independent and portable unit, which allows stories to contain scenes that can be reorganized and reused.

**Some HTML tags are not recognized in YESS**. The tags below could download untrusted content or could modify the user experience in undesirable ways. This could pose a security risk and therefore must be ignored by the YESS user agent:

- `<base>`
- `<script>`
- `<iframe>`
- `<object>`
- `<portal>`
- `<embed>`, if the tag is not defined at the top level, and/or if the tag does not reference a `.html` or `.txt` file. You cannot use the `<embed>` tag to create an instance of a browser plugin.

## Stream Text Files

Stream text files provide a space-efficient way of referring to a number of other stories and streams in a single file. These files are in a plain-text line-based format. Each line is either a URL that points either to a cover HTML file, or another stream text file. The URLs may be absolute or relative. If they're relative, they're relative to the location the stream file.

```
path/to/cover
path/to/another/cover
../path/to/stream.txt
```

The format of these text files is line-based, the format is essentially a reduction of a large list of `<embed>` tags.  For example, the YESS specification defines the `data-if=`  HTML attribute which is used to perform client-side audience segmentation (described below). These attributes can be included via the following syntax

```
path/to/something
path/to/something/else if=segment1
path/to/another/index.txt if=segment2
```

Which can be thought of as being functionally equivalent to:

```html
<embed src="path/to/something">
<embed src="path/to/something/else" data-if="segment1">
<embed src="path/to/another/index.txt" data-if="segment2">
```



### Syndicated Stream Text Files

Authors can apply the file extension `.s.txt` to their stream text files instead of just `.txt` , which signals to the user agent that the content is frequently updated, and that it should consider giving the user the choice to connect the syndication stream to their reader application.



### Linking To Syndicated Stream Text Files

Authors can provide a way to encourage their readers to subscribe to YESS streams by pointing the browser to a URL that ends in the `.s.txt`  extension. This is done through the use of an anchor `<a>` tag. For example:

```html
<a href="feed.s.txt">Subscribe to my YESS feed</a>
```

These links can exist anywhere in YESS content. This design allows authors to provide one-click subscribe to many different YESS streams all in the same location:

```html
<a href="fishing.s.txt">Subscribe to my fishing YESS feed</a>
<a href="cooking.s.txt">Subscribe to my cooking YESS feed</a>
```

Authors can point to YESS streams to recommend other YESS streams that exist on other domains:

```html
<a href="https://alice.com/knitting.s.txt">Subscribe to Alice's knitting YESS feed.</a>
```



## Polling For Updates

Periodically, the YESS reader executes an HTTP `HEAD` request on the URL of all stream files to which the user has subscribed. The reader compares the returned HTTP `Last-Modified` header, and/or the `Content-Length` header values from the last poll to determine if a full re-download of the index is necessary.

Some readers may optimize this re-download process. For example, some readers may use the HTTP `Range` header to only download the first `4kb` of data (which would be roughly 100 entries), and ignore all others.



## Avatars

Authors can associate an avatar with a stream, so that the YESS reader can provide a way for users to identify what content is coming from what author. These associations are done by having a `.jpg` image file adjacent to the stream text file, that shares the same base file name as the stream file.

For example, if a stream comes from the URL `http://example.com/stream.txt`, the YESS reader will request an avatar image from the URL `http://example.com/stream.jpg`.



# Client-Side Segmentation

## Concept

Client-side segmentation is an innovative technique of maintaining **strict user privacy** while simultaneously performing **rich analytics and targeting**. Additionally, the technique maintains **full compliance with GDPR** and CASL regulation while simultaneously not requiring authors to request permission to store tracking cookies. In short, client-side segmentation allows authors to do ***Targeting Without Tracking***.

In effect, client-side segmentation makes it possible to achieve the kind of **user-specific content specialization** that exceeds what is seen in the most advanced email marketing tools, except while collecting no personally identifiable information, user identifiers, or fingerprints, and without allowing authors to execute arbitrary JavaScript. 

Typical behavioral tracking and content specialization is done by developing a rich user profile on the server side, which is tied to a tracking cookie on the users device, which allows the server to deliver specialized content to the user.

With Client-side segmentation, no server makes any decision about what content to send to what user. Instead, the segmentation logic is embedded directly in the YESS content itself, and the YESS reader determines for itself how the content should be specialized for the user.

The technique provides a very simple way for authors to **set boolean flags** and adjust **small integers** in response to events, and transmit these values to a server. The only data the server can collect is:

- The user agent string
- An IP address
- The time at which the data was collected
- The page from which the data was collected (using the `HTTP_REFERER` header value)
- A series of flags and small integers that describe the actions taken by the user up until the point of transmission.

## YESS Expression Format

The implementation works by writing simple expressions into event handlers, that take the form of one or more `opcode.identifier` instances separated by the `;` character. White-space is not significant. This specification defines only 6 opcodes, which are documented below:

### set

Sets a boolean identifier if it has not already been set. 

```html
<a onclick="set.clicked">...</a>
```

### inc

Increments the value of an integer identifier, or creates a new integer with a value of 1 if no such tag exists

```html
<a onclick="inc.clicked">...</a>
```

### dec

Decrements the value of an integer identifier. Has no effect if no such identifier has been previously defined.

```html
<a onclick="dec.clicked">...</a>
```

### del

Deletes a previously set boolean or integer identifier.

```html
<a onclick="del.clicked">...</a>
```

### is

The `is` opcode is reserved for a namespace constants. This specification only defines a single constant, `bot`, which is most useful in allowing certain content to be hidden from bots.

```html
<section data-if="!is.bot">...</section>
```

### ping

A functional opcode that triggers a transmission of data to a server. The identifier is sent to the server along with the ping information so that any analytics tool can map the ping operation back to a place in the YESS source. See *Pings* below for more information.

```html
<section onload="ping.loaded">...</section>
```



## Examples

Reset a tag:

```html
<button onclick="del.tagname; add.tagname">Reset a tag</button>
```

Notify when a video starts playing:

```html
<video onstart="ping.start">...</video>
```

Notify when a video has completed:

```html
<video onfinish="ping.finish">...</video>
```



**(COMMENTS REQUESTED: Can GDPR and CASL experts provide insight into whether there could be compliance pitfalls with this design?)**



## Transferring Segmentation Data

There exists the case where a user could take a variety of actions which generate segmentation data, then choose to subscribe to a particular stream via clicking on a link to a syndicated stream text file, and then continue consuming content within their YESS reader. In this case, the segmentation data would be lost when making the jump from the web browser into the YESS reader.

In order to handle this case, the URLs to syndicated stream text files are appended with a query string value, whose format is equal to the query string format used in Ping operations (see the *Ping* section below.)

For example, given the following anchor tag:

```html
<a href="feed.s.txt">Subscribe to my YESS feed</a>
```

The `href` attribute will be internally re-written as:

```html
<a href="feed.s.txt?segmentation-data-injected-here">Subscribe to my YESS feed</a>
```



## Pings

The YESS protocol specifies a way to collect *Pings*, in response to user-triggered events. Pings are HTTP `HEAD` requests which are made to the URL specified in the `<meta name="yess-ping">` tag (explained below). The URL contains all of the data that has been collected for the user up until the point of the ping. This data is encoded in the querystring of the URL.

If we assume that the following data has been collected:

| Identifier | Type    | Value |
| ---------- | ------- | ----- |
| n1         | integer | 3     |
| n2         | integer | 4     |
| b1         | boolean | false |
| b2         | boolean | true  |

The format of the URL where the request is made is:

```
http://yess-ping-domain.ext/?ping-identifier&b2&n1=3&n2=4
```



### The `yess-ping` Meta Tag

The `yess-ping` meta tag is used to specify the base URL where pings are sent.

```html
<meta name="yess-ping" content="https://www.analytics.com/path">
```

The URL should not specify a query string value (`?a=b`) or a fragment (`#`) as this information will be discarded by the user agent.

The `yess-ping` meta tag may be defined within either a Cover HTML file or a Body HTML file. Stories that do not contain a `yess-ping` tag do not have the capability to send pings.

When the `yess-ping` value is specified within an HTML file, whether of the Cover or Body variety, this value is inherited to all descendant HTML files. This allows for the scenario where multiple content owners contribute scenes to a particular story, and each owner needs to recieve analytics around user behavior for the scenes they've contributed. For example, if a story is composed by a main author, as well as scenes from two independent sponsors, each sponsor can receive analytics information for their own scenes, whereas the main author can recieve analytics information for the entire story.



## Conditional Expressions

The `data-if=` attribute is used to conditionally display a tag based on client-side segmentation criteria. The attribute may exist on any tag in a YESS document. Its value is a simple expression format as described below


Applies when the `mybool` tag is true:

```html
<tag data-if="mybool">
```

Applies when the `mybool` tag is false:

```html
<tag data-if="!mybool">
```

Applies when the `mycount` tag is equal to 10:

```html
<tag data-if="mycount 10">
```

Applies when the `mycount` tag is greater than 10:

```html
<tag data-if="mycount > 10">
```

Applies when the `mycount` tag is less than 10:

```html
<tag data-if="mycount < 10">
```

The left side must be an identifier, and the optional right side must be a literal numeric value. There is no less-than-or-equals or greater-than-or-equals operators.

Logical AND is done by chaining multiple expressions together which are separated with the comma character:

```html
<tag data-if="mycount > 10, mycount < 20">
```

Logical OR is purposely omitted from this specification as a complexity reduction measure.

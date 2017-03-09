---
layout: post
title:  "\"💩\".length === 2"
date:   2017-03-08 16:28:00 -0800
categories: programming javascript
---

Yup, it's true. In Javascript, `"💩".length === 2`. You can open up a Chrome debug console, or Node.JS REPL and see for yourself. But why?! And why does `'⛳'.length` only equal 1?

It all comes down to codepoints and our friend, [Unicode](https://en.wikipedia.org/wiki/Unicode). If you're a little rusty on the details of Unicode and character sets, stop now, and read [The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets (No Excuses!)](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/). It's excellent, and I read it from time-to-time to refresh myself on details.

The next few paragraphs are summaries from this superb [Javascript Unicode](https://mathiasbynens.be/notes/javascript-unicode) post by [Mathias Bynens](https://mathiasbynens.be). It's 5 years old, and sadly still true. 

Anyways, the Unicode codepoint range goes from U+0000 to U+10FFFF which is over 1 million symbols, and these symbols are divided into groups called planes. Each plane is about 65000 characters (16^4). The first plane is the Basic Multilingual Plane (U+0000 through U+FFFF) and contains all the common symbols we use everyday and then some. The rest of the planes require more than 4 hexadecimal digits and are called supplementary planes or astral planes. I have no idea if there's a good reason for the name "astral plane." Sometimes, I think people come up with these names just to add excitement to their lives. 

The current largest codepoint? Why that would be a [cheese wedge](https://codepoints.net/U+1F9C0) at U+1F9C0.  🧀 &nbsp; How did we ever communicate before this?

We can express characters in a couple different ways: `"A" === "\u0041" === "\x41" === "\u{41}"`. These are escape sequences. The \x can be used for most (but not all) of the Basic Multilingual Plane, specifically U+0000 to U+00FF. The \u can be used for any Unicode characters. The curly braces are required if there are more than 4 hexadecimal digits and optional otherwise. This is for Javascript/HTML by the way. Other languages have their own sets of rules.

And `"💩" === "\u{1F4A9}"`.  Unfortunately, this is also true: `"💩" === "\uD83D\uDCA9"`. What is this nonsense? All astral codepoints can also be represented by "surrogate pairs", and this is used for backwards compatibility reasons. This is why `"💩".length === 2`. There's a formula to calculate surrogates from astral codepoints, and vice versa. 

Given a codepoint `C` greater than 0xFFFF, it corresponds to a surrogate pair `<H,L>`.
{% highlight javascript %}
H = Math.floor((C - 0x10000) / 0x400) + 0xD800
L = (C - 0x10000) % 0x400 + 0xDC00`
{% endhighlight %}

So, in our case:
{% highlight javascript %}
C = 0x1F4A9
H = (Math.floor((0x1F4A9 - 0x10000) / 0x400) + 0xD800).toString(16) = 0xD83D
L = ((0x1F4A9 - 0x10000) % 0x400 + 0xDC00).toString(16) = 0xDCA9;
{% endhighlight %}

The `.toString(16)` converts the number to a hexadecimal string. You can see that the answers correspond to the '\uD83D\uDCA9' I had written above.

The whole reason I ran into this was because I was enforcing minimum password lengths, and I noticed that emoji counted as more than one. In fact if you paste the 💩 &nbsp;in a password field, you'll see: 

![A single poo emoji yields two dots in a password field]({{ site.url }}/assets/poo-unicode/password-poo.png)

There's an open [Chromium bug for "Emoji in password fields appear as two bullets"](https://bugs.chromium.org/p/chromium/issues/detail?id=486880). I filed the analagous [Firefox](https://bugzilla.mozilla.org/show_bug.cgi?id=1345229) and [Safari](https://bugs.webkit.org/show_bug.cgi?id=169373) bugs.

As an aside, here's an article [discussing the merits of emoji passwords](https://medium.com/@hvost/why-you-should-not-use-emojis-in-your-passwords-b8db0607e169#.ee3f1qr43).

So, is there a solution that counts symbols correctly? [Bynens](https://mathiasbynens.be/notes/javascript-unicode#accounting-for-astral-symbols) lists a couple possibilities. [Array.from](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from) shows some promise. It's succinct, works in Node, and is generally well supported across browsers, except IE11 and below, I think.

`Array.from("💩").length === 1; //hooray!`

however

`Array.from("❤️").length === 2; //boooo!`

![Why?!]({{ site.url }}/assets/poo-unicode/why1.jpg)

From what I understand ❤️  &nbsp;is comprised of two codepoints: U+2764 and U+FE0F. The first is [Heavy Black Heart](https://codepoints.net/U+2764). The second is [Variation Selector 16](https://codepoints.net/U+fe0f) which changes the appearance of the preceding character! Ugh. In fact, U+FE00 through U+FE0F can all change the appearance of the previous character. With the case of this heart, only U+FE0F does. Other hearts(💙, 💚, 💛, 💜 ) each have their own codepoint, but the red heart requires two. I'm not sure why, but [I asked on Stack Overflow](https://stackoverflow.com/questions/42679712/why-does-the-red-heart-emoji-require-two-code-points-but-the-other-colored-hear/42680595#42680595).

To accommodate this I can change my code to something horrible like this:

{% highlight javascript %}
function fancyCount(str){
  return Array.from(str.split(/[\ufe00-\ufe0f]/).join("")).length;
}
{% endhighlight %}

In `.split(/[\ufe00-\ufe0f]/).join("")` I'm using a regular expression to essentially remove all the variation selectors.

{% highlight javascript %}
fancyCount("💩") === 1 //hooray!
fancyCount("❤️") === 1 //hooray!
{% endhighlight %}

So, we're done right? Nope! The more I wrote in this blog post, the deeper the rabbit hole. Putting together woman, heart, kiss, women emojis are frequently rendered as [a single emoji](http://emojipedia.org/kiss-woman-woman/). 

{% highlight javascript %}
"👩‍❤️‍💋‍👩".length === 11
Array.from("👩‍❤️‍💋‍👩").length === 8
fancyCount("👩‍❤️‍💋‍👩") === 7
{% endhighlight %}

![Make it stop]({{ site.url }}/assets/poo-unicode/makeitstop.gif)

👩‍❤️‍💋‍👩 &nbsp; is created by having a [Zero Width Joiner](https://codepoints.net/U+200D) `\u{200D}` character between the component emojis. So we could do something like this:

{% highlight javascript %}
function fancyCount2(str){
  const joiner = "\u{200D}";
  const split = str.split(joiner);
  let count = 0;

  for(const s of split){
    //removing the variation selectors
    const num = Array.from(s.split(/[\ufe00-\ufe0f]/).join("")).length;
    count += num;
  }

  //assuming the joiners are used appropriately
  return count / split.length;
}
{% endhighlight %}

{% highlight javascript %}
fancyCount2("💩") === 1 //hooray!
fancyCount2("❤️") === 1 //hooray!
fancyCount2("👩‍❤️‍💋‍👩") === 1 //hooray!
{% endhighlight %}

Honestly, you should probably never use this. Not all browsers, UIs, etc even render 👩‍❤️‍💋‍👩 &nbsp; as a single symbol. The code assumes the joiners are used between characters appropriately which could be very problematic. During my research I noticed that [U+200C](https://codepoints.net/U+200C) allows for [ligatures](http://ilovetypography.com/2007/09/09/decline-and-fall-of-the-ligature/) and `fancyCount2` isn't accounting for that. That's an easy adjustment, but I'm sure there's even more modifiers and joiners that I've never heard of. I know you're on the edge of your seat waiting for the ultimate solution, but this rabbit hole is too deep! Sorry for the disappointment, but if you know of a more robust, comprehensive character counter, I'd love to hear from you!

![I'm out]({{ site.url }}/assets/poo-unicode/imout.gif)

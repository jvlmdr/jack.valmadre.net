---
layout: post
title:  Mathematics of the twelve note musical scale
math:   true
---

If a note has frequency $$f$$, then $$2f$$ is the frequency of the note which we perceive to be the same but an octave higher.
A further octave above that must be $$4f$$, and the integer multiple in between, $$3f$$, corresponds to the _fifth_ above the first octave.
The fifth above the original note is thus $$3/2 f$$.
If we continue to take multiples, we find $$5f$$ is the _major third_ above the second octave, so $$5/4 f$$ is the frequency of this interval within the first octave.

In general, intervals with small integer frequency ratios are the ones that sound "good" to the human ear.
Many can be constructed from the octave, fifth and major third, since their ratios include the prime numbers 2, 3 and 5 respectively.
A _fourth_ is an octave minus a fifth: $$2 / (3/2) f = 4/3f $$.
A _minor third_ is a fifth minus a major third: $$(3/2) / (5/4) f = 6/5 f$$.
A _major seventh_ is a fifth plus a major third: $$(3/2)(5/4) = 15/8 f$$.

Somewhat amazingly, 12 fifths is close to 7 octaves

$$
\frac{(3/2)^{12}}{2^{7}}
= \frac{3^{12}}{2^{19}}
= \frac{531441}{524288}
= 1.0136\dots
$$

and 3 major thirds is close to 1 octave

$$
\frac{(5/4)^3}{2}
= \frac{5^{3}}{2^{7}}
= \frac{125}{128}
= 0.9765\dots
$$

with 3 being a factor of 12.
This is the reason that the scale is divided into twelve semitones.
These notes are enumerated by the "circle of fifths," which passes through each note once.

"Equal temperament" tuning chooses the logarithmic spacing of the notes to be be $$2^{1/12}$$ to ensure that no key sounds better than any other.
This means that a fifth is approximated

$$
3/2f = 1.5f \approx 2^{7/12} = 1.4983\dots
$$

In fact, since $$\log_{2}(3/2)$$ is irrational, there is no finite uniform division of the octave that will give an exact fifth.
The next best approximations to $$\log_{2}(3/2)$$ after $$7/12$$ are $$17/29$$, $$24/41$$ and $$31/53$$.
Note that a uniform division of the octave should also give a good approximation of the major third.

{% comment %}
<table>
<tr><td> $$n$$ </td><td> $$x$$ </td><td> $$3/2 - 2^{x}$$ </td><td> $$y$$ </td><td> $$5/4-2^{y}$$ </td></tr>
<tr><td> 5 </td><td> 3/5 </td><td> -0.016 </td><td> 2/5 </td><td> -0.067 </td></tr>
<tr><td> 7 </td><td> 4/7 </td><td> 0.014 </td><td> 2/7 </td><td> 0.031 </td></tr>
<tr><td> 9 </td><td> 5/9 </td><td> 0.030 </td><td> 1/3 </td><td> -0.0099 </td></tr>
<tr><td> 12 </td><td> 7/12 </td><td> 0.0017 </td><td> 1/3 </td><td> -0.0099 </td></tr>
<tr><td> 29 </td><td> 17/29 </td><td> -0.0013 </td><td> 9/29 </td><td> 0.010 </td></tr>
<tr><td> 41 </td><td> 24/41 </td><td> -0.00042 </td><td> 13/41 </td><td> 0.0042 </td></tr>
<tr><td> 53 </td><td> 31/53 </td><td> 0.00006 </td><td> 17/53 </td><td> 0.0010 </td></tr>
</table>

The Indonesian gamelan with "slendro" tuning has five notes that are roughly equally spaced.
With "pelog" tuning it has seven notes which are often tuned as a subset of nine-tone equal temperament, and of which a subset of only five notes is generally used per song.
{% endcomment %}

After 3 and 5, the next prime to consider is 7.
In fact, the note $$7f$$ is reasonably close to a conventional _minor seventh_, which can be obtained as a fifth plus a minor third (equal to two fifths minus a major third).
This can be seen by comparing $$7/4f = 1.75f$$ to $$(3/2)^{2} (4/5) f = 9/5 f = 1.8f$$.
This may be partly the reason that a dominant seventh chord (root, major third, fifth, minor seventh) is slightly dissonant and known to resolve back to the major chord.

I gleamed most of this from Dave Benson's <a href="http://homepages.abdn.ac.uk/mth192/pages/html/maths-music.html">online book "Music: A Mathematical Offering."</a>

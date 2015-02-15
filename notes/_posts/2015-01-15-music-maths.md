---
layout: post
title:  Musical maths
math:   true
---

If $$f$$ is the frequency of a note, then $$2f$$ is the same note an octave higher, $$3f$$ is a fifth higher again, $$4f$$ is two octaves above the original note and $$5f$$ is the major third above that.
Removing the extra octaves, a fifth is $$3/2 f$$ and a major third is $$5/4 f$$.

Intervals which sound "good" are usually small integer ratios.
Most important notes can be defined in terms of fifths and major thirds, since these correspond to the prime numbers $$3f$$ and $$5f$$ respectively.
The fourth is an octave minus a fifth $$2 / (3/2) f = 4/3f $$.
A minor third is a fifth minus a major third $$(3/2) / (5/4) f = 6/5 f$$.
A major seventh is a major fifth plus a major third $$(3/2)(5/4) = 15/8 f$$.

Somewhat amazingly, 12 fifths is close to 7 octaves

$$
\frac{(3/2)^{12}}{2^{7}}
= \frac{3^{12}}{2^{19}}
= \frac{531441}{524288}
\approx 1.0136,
$$

and 3 major thirds is close to 1 octave

$$
\frac{(5/4)^3}{2}
= \frac{5^{3}}{2^{7}}
= \frac{125}{128}
\approx 0.9766
$$

with 3 being a factor of 12.
This is the reason that the scale is divided into twelve semitones.

"Equal temperament" tuning simply chooses the logarithmic spacing of the notes to be be $$2^{1/12}$$ to ensure that no key sounds better than any other.

In fact, since $$\log_{2}(3/2)$$ is irrational, there is no finite uniform division of the octave that will give a perfect fifth.
The next best approximations to $$\log_{2}(3/2)$$ after $$7/12$$ are $$17/29$$, $$24/41$$ and $$31/53$$.

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

After 3 and 5, the next prime to consider is 7.
In fact, the note $$7f$$ is very close to a conventional minor seventh, which is obtained as a fifth plus a minor third (equal to two fifths minus a major third).
This can be seen by comparing $$7/4f = 1.75f$$ to $$(3/2)^{2} (4/5) f = 9/5 f = 1.8f$$.
This may be partly the reason that a dominant seventh chord (root, major third, fifth, minor seventh) is slightly dissonant and known to resolve back to the major chord.

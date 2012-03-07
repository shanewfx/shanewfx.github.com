---
layout: post
title: "ISDB-T/SBTVD-T Closed Caption"
date: 2012-03-07 16:40
comments: true
categories:
- Closed Caption
- DTVCC
---

[Closed Caption][1]（可简称为CC）可以简单来看作为一种字幕，最初设计的目的是为了帮助有听力障碍的人理解电视节目的内容，即在屏幕上显示与声音（Audio）对应的文字。

目前，主要的Closed Caption标准有ATSC的608和708，日本ARIB STD-B24等，而巴西的数字电视标准SBTVD-T是在日本的ISDB-T标准基础上稍作修改而来，因此，巴西的DTVCC标准实际也是ARIB STD-B24。

上述的两种CC标准的主要区别在于，ATSC的CC数据是在Video中传输的，而ARIB的CC数据是在TS流中以独立的ES进行传输的。

CC数据主要由Control Code（控制字）和Character Code（字符码）组成，其中，控制字规定了CC字符的字符集、显示位置及格式等信息，而通过字符码可以从指定的字符集中取得CC字符。

这两周一直在做日本DTVCC，不过这次不是从零开始，而是在之前巴西的DTVCC Decoder上增加对日本Closed Caption的支持，主要的工作集中在对日本字符集的支持上，最复杂的部分就是构建一个Kanji字符集的Unicode映射表。

###ISDB-T/SBTVD-T Closed Caption的Spec

- Caption Data Parse : ARIB STD B24 Vol. 1, Part3, CH 9
- Character Coding : ARIB STD B24 Vol. 1, Part2, Ch 7
- Caption Display : ARIB STD B14 Vol. 3, Section 2, 4

###Japan Character Set Reference

- [JIS X 213 : 2004][2]


[1]: http://en.wikipedia.org/wiki/Closed_captioning
[2]: http://seiai.ed.jp/sys/text/cs/mcodes/jis0213table.html

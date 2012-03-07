---
layout: post
title: "ISDB-T/SBTVD-T Closed Caption"
date: 2012-03-07 16:40
comments: true
categories:
- Closed Caption
- DTVCC
---

[Closed Caption][1]（可简称为CC）可以简单来看作为一种字幕，最初设计的目的是为了帮助有听力障碍的人理解电视节目的内容，即将节目中的Audio（背景音乐显示音乐符号）通过文字在屏幕上显示出来。

目前，Closed Caption的标准主要有ATSC定义的608和708，日本ARIB STD-B24等，而巴西的数字电视标准SBTVD-T是在日本的ISDB-T标准基础上稍作修改而来，因此，巴西数字电视的Closed Caption标准也是采用了ARIB STD-B24。

<!--more-->

ATSC CC与ARIB CC的数据传输区别在于:

- ATSC的CC数据是通过MPEG-2 Video中的User Data部分进行传输；
- ARIB的CC数据是在TS流中以独立的ES进行传输。

下面将介绍ARIB STD-B24标准中Closed Caption的一些细节内容。

ARIB CC数据首先被打包为Synchronized PES（Independent PES），然后，Synchronized PES会继续被打包为PES packet中，该PES packet的stream id为0xBD，同时PES packet header中需要含有PTS，最后，该PES会被打包为TS packet，在TS中进行传输。

如果TS流中含有Closed Caption，则在TS流的PMT和EIT中会有相应的descriptors进行描述。载有Closed Caption的ES流在PMT中的stream type值为0x06，因此，可通过在PMT表中查找stream type为0x06的Closed Caption ES对应的PID，从而，可从TS流中过滤出Closed Caption ES对应的TS packet。

ARIB CC数据的结构:
Synchronized PES -> Data Group -> Caption Management
                               -> Caption Statement -> data_unit

Caption Management包含了Closed Caption的全局信息，并以一定的时间间隔来发送。另外，Caption Management中一般不会含有Closed Caption字符编码数据。

Caption Statement是Closed Caption数据传输的主要载体。CC数据主要由Control Code（控制字）和Character Code（字符码）组成，其中，控制字规定了CC的字符集、显示位置及文本格式等信息，而通过字符码可以从指定的字符集中取得CC字符。Closed Caption的控制字和字符码都在data_unit中。

ARIB CC的整个字符集被分为4个区域：C0、GL、C1、GR，其中C0和C1对应是Control Code，而GL和GR对应的则是CC当前使用的字符集。可以使用不同的控制字（LS0、LS1、LS1R、LS2、LS3、LS2R、LS3R、SS2、SS3）将不同的字符集(G0、G1、G2、G3)调用到GL和GR中，而G0、G1、G2、G3具体对应哪一个字符集则可以用ESC控制字来指明。

除了C0和C1中的控制字之外，通过CSI控制字又扩展出一些其他的控制字: SWF、SDF、SDP、SSM、SHS、SVS、RCS、ACPS、ORN、SCR、TCC、SCS、ACS、PPA、XCS、MDF、CFS、SRC、GAA、GSM、PLD、PLU、CCC。

在这些控制字中，有一些控制字没有被使用，有些则不是必须实现的: BEL、CAN、RS、CDC、WMM、MACRO、TIME、TCC、SCS、ACS、PPA、XCS、MDF、CFS、SRC、GAA、GSM、PLD、PLU、CCC。

CC的控制字基本可以分为以下几类：

- Display Format：SWF、SDF、SDP、SSM、SHS、SVS
- Color：BKF、RDF、GRF、YLF、BLF、MGF、CNF、WHF、COL、RCS、POL
- Curor Position：APB、APF、APD、APU、APR、APS、PAPF、ACPS
- Character Set：LS0、LS1、SS2、SS3、ESC
- Character Size：SSZ、MSZ、NSZ、SZX
- Display Effect：ORN、SCR、FLC、HLC、SPL、STL （可选）
- Clear Screen：CS

SWF指定了Caption Plane的大小和Caption Text的Print Direction;

SDF指定了Caption Display Window的size, Caption Display Window始终在Caption Plane内;

SDP指定了Caption display window左上角的坐标，该坐标值是相对于Caption Plane的左上角坐标（0, 0）;

根据SDF和SDP就可以确定Caption display window在Caption Plane内的位置及Size;

SSM指定了Normal Font的Size;

SHS指定了同一行字符之间的间隔;

SVS指定了行与行之间的间隔;

根据SSM、SHS、SVS可以知道每个字符所占Size, 不同字体大小的字符Size也不尽相同;

通过上面的这些控制字，则可以推断出在Caption display window内所能够容纳的字符个数（包括每行字符的个数以及总的行数）。

BKF、RDF、GRF、YLF、BLF、MGF、CNF、WHF这8个控制字指定字符的前景色(foreground color)，而字符的背景色(background color)和half foreground color及half background color均是通过COL控制字来指定。

Closed Caption总共使用了128种颜色，这些控制字除了指定了color值，同时也指定了改color值在Color map table中Index的低4位（CMLA），而Color map table Index的高4位（Pallete Number）也由COL来指定（取值范围：0~7）。

APB、APF、APD、APU、APR、APS、PAPF、ACPS控制了下一个字符在caption display window中插入的位置。APB、APF、APD、APU分别控制了当前Curor向后、向前、向下、向上移动一个字符的位置；APR控制了字符的换行；APS控制了新的Caption起始的Curor位置；PAPF跟有一个参数P1，控制了当前Curor向前移动其参数P1指定的字符个数；ACPS指定了Cursor在Caption Plane中的绝对位置（字符的左下角坐标）。

LS0、LS1、SS2、SS3控制调用指明了的字符集（G0、G1、G2、G3）到GL或者GR中。
- LS0调用G0对应的字符集到GL中；
- LS1调用G1对应的字符集到GL中；
- SS2临时调用G2中的字符到GL中；
- SS3临时调用G3中的字符到GL中。

除了C0中明确定义的这几个字符集控制字外，其他的字符集控制字则通过ESC控制字扩展来定义（LS2、LS3、LS1R、LS2R、LS3R）。

ESC控制字除了上述用来扩展字符集调用命令控制字外，还可以用来指明G0、G1、G2、G3分别对应何种字符集。G0、G1、G2、G3、GL、GR的初始值在Spec中也指定了。

SSZ、MSZ、NSZ、SZX用于控制了相对于SSM指定的Normal Font Size的字符大小。SSZ、MSZ、NSZ分别指定了字符的大小为samll、middle、normal。

- - -

这两周一直在做日本DTVCC，不过这次不是从零开始，而是在之前巴西的DTVCC Decoder上增加对日本Closed Caption的支持，主要的工作集中在对日本字符集的支持上，最复杂的部分就是构建一个Kanji字符集（JIS X 213 : 2004）的Unicode映射表。

JIS X 213 : 2004表见[这里][2]。


###ISDB-T/SBTVD-T Closed Caption相关的Spec

- ARIB STD-B24 VOL1 PART2 (Chapter 7: Character Coding): CC字符集；

- ARIB STD-B24 VOL1 PART3 (Coding of Caption and Superimpose): CC Data的syntax；

- ARIB STD-B24 VOL3 (Chapter 5: Independent PES): Independent PES的syntax；

- ARIB TR-B14 VOL3 Section2 (Chapter 4: Operation of caption and superimpose encoding): Closed Caption的显示；

- ABNT NBR 15606-1 (11.4 Character coding): 关于巴西Closed Caption在字符编码上的一些限制

- ABNT NBR 15608-3 (Chapter 5 / 14 / 22): 关于巴西Closed Caption在PMT及EIT上descriptor的定义和解释


[1]: http://en.wikipedia.org/wiki/Closed_captioning
[2]: http://seiai.ed.jp/sys/text/cs/mcodes/jis0213table.html

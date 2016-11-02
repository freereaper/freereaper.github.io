---
layout: post
title: compress and downscaler
date: 2016-10-26
comments: false
tags: [zx]
---

# Compress

1. 有损压缩(lossy)
  > 压缩的时候如果不能压到fail boundary以内，则强制压缩，保证burst length在fail boundary以内。

2. 无损压缩(lossless)
  > 压缩的时候如果能压到fail boundary以内，则压缩，否则仍然以2048的burst length输出。
 
只有`ARGB888`和`NV12`才会支持lossy，其余的format即使lossy打开，也是输出lossless的，即除了`ARGB888`和
`NV12`外，其余的format，lossy和lossless是相同的。

 <!-- more -->

| data          | format          | compress type  |
|:-------------:|:-------------:  |:-----:         |
| uyvyd.bin     | YCRYCB422_16BIT |   11           |
| yuyvd.bin     | CRYCBY422_16BIT |   10           |

----

compress test:
```bash
cmode -m -x 640 480 -nd -ps 41 21 -st 190 50 -ws 200 200 -a 8000 -pse 300 300 -ste 2510 100 -wse 200 150 -ae 8000 -f 2 -fe 2 -cp 2048 -t 2
```
----

compress scaler test:
```bash
cmode -scl 0 -p 42 22 -o 190 50 -s 100 100 -w 191 200 -pe 300 300 -oe 250 100 -se 101 91 -we 200 150 -t 2
```

# DownScaler

DIU downscaler register group:
`mm327c`: 
> Bit[21] --- DST_FORMAT(output data format), control the output of downscaler csc.

`mm3280`:
> Bit[7-8] --- SCALING_MODE（downscaler work mode)

`mm3278`:
> Bit[5-31] --- BASE_ADDR

`mm8264`:  down scaling write back control register
> Bit[1-3] --- csc data in format
> Bit[4-6] --- csc data output format


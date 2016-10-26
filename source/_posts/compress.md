---
layout: post
title: compress
date: 2016-10-26
comments: false
tags: [zx]
---

1. 有损压缩
  > 压缩的时候如果不能压到fail boundary以内，则强制压缩，保证burst length在fail boundary以内。

2. 无损压缩
  > 压缩的时候如果能压到fail boundary以内，则压缩，否则仍然以2048的burst length输出。
 
 <!-- more -->
 
只有ARGB888和NV12才会支持lossy，其余的format即使lossy打开，也是输出lossless的，即除了ARGB888和
NV12外，其余的format，lossy和lossless是相同的。
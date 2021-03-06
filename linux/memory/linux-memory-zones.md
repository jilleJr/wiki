---
date: 2021-01-20T21:01
tags: 
  - linux/how does memory work in linux
  - linux/what types of memory exists in linux
---

# Zones

RAM is not one huge chunk of memory, nor is it a stream of memory split into any
size.

RAM in Linux is split up into zones, followed by [[linux-memory-pages]]#.

> These definitions reflect how memory is managed on x86 architecture machines.
> They can differ widely on other architectures, such as ARM.

## Direct Memory Access (DMA)

Lowest 16 MB range of your memory. That is, bytes at index $0$ to $16,777,216$
($= 2^{20}$ bytes)

## Direct Memory Access 32 (DMA32)

There was a time when 32-bit was the new cool higher limit, compared to todays
64-bit. 

32-bit allowed to reference memory up to 32 bits in key. This zone spans all
the way up to $4$ GB ($= 4,294,967,296 = 2^{32}$ bytes)

## Normal

- 32-bit machines: RAM in range $16$ MB to $896$ MB

- 64-bit machines: RAM in range $4$ GB *and beyond*
  (or actually, to $16$ EB, $2^{64}$ bytes)

## HighMem

Only a concept on 32-bit Linux machines. It would range from $896$ MB
*and beyond*, even past $4$ GB if correctly set up.

## References

- McKay, D. (2019, December 9). *What Is Swappiness on Linux? (and How to Change
  It)*. How-To Geek. <https://www.howtogeek.com/449691/>

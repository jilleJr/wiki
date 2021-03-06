---
date: 2021-01-20T19:29
tags: 
  - linux/how does memory work in linux
  - linux/how does linux swap memory
  - linux/understanding swap and swappiness
---

# How Linux uses swap

In Linux, you can dedicate a partition of your disk for swap or let swap be
stored in a mere file on your system.

There are two [[linux-memory-page-mapping]] that take use of swap. The anonymous memory
and file-backed memory.

## When file-backed memory is swapped

Whever you read from a file, the content is kept in memory for as long as it can.
This is to improve performance, because if that file's content is needed again it
can just be taken directly from memory without needing to request the data anew
from the disk.

Swapping files means to store files in RAM.

## When anonymous memory is swapped

Our RAM on our computers is not infinite, though the applications we write uses
libraries to talk to the kernel to request (allocate) memory in a way that it
seems as it's infinite. One of the jobs of the kernel is to make sure that
illusion stays intact.

Instead of having the computer run out of memory, if it has some swap configured
(either as its own partition or a file) it can then take some of the memory in
RAM, preferably memory that is rarely used, and store it on disk. Once it's
stored there it can safely free up that memory and allow newly allocated data to
take its place. When the now-disk-stored memory is needed again the kernel can
either load that data back into memory. Practically swapping out which memory
that is stored on the disk and which that is stored in RAM.

When it comes to anonymous memory, this means that you practically just get more
memory, at the cost of performance due to the I/O operations that needs to take
place as even SSD's are still much slower than today's standard on RAM chips.

Swapping anonymous memory means to store process memory on disk.

## When does these swaps occurr

Not strictly on demand.

There's a concept called the "water mark", for which whenever the used memory
on each memory node either exceeds the "high water mark" or falls short of the
"low water mark", the kernel then starts taking action on swapping some of this
data out between disk and RAM, wether this being moving some file-backed memory
or swapped anonymous memory back into RAM, or moving some anonymous memory to
disk.

You can observe the current water mark settings per memory node by running the
following command:

```sh
$ less /proc/zoneinfo
# This files spits out a lot of information, but here are the highlights:
Node 0, zone      DMA
  per-node stats
      nr_inactive_anon 67345
      # ...
      nr_written   9104
  pages free     3721
        min      19
        low      23       # The low water mark for Node 0, zone DMA
        high     27       # The high water mark for Node 0, zone DMA
        spanned  4095
        present  3743
        managed  3721
        protection: (0, 3857, 12612, 12612)
      nr_free_pages 3721
      # ...
Node 0, zone    DMA32
  pages free     988431
        min      5162
        low      6452     # The low water mark for Node 0, zone DMA32
        high     7742     # The high water mark for Node 0, zone DMA32
        spanned  1044480
        present  1011712
        managed  988823
        protection: (0, 0, 8754, 8754)
      nr_free_pages 988431
      nr_zone_inactive_anon 0
      nr_zone_active_anon 0
      # ...
Node 0, zone   Normal
  pages free     1741547
        min      11714
        low      14642    # The low water mark for Node 0, zone Normal
        high     17570    # The high water mark for Node 0, zone Normal
        spanned  2299904
        present  2299904
        managed  2241083
        protection: (0, 0, 0, 0)
      nr_free_pages 1741547
      nr_zone_inactive_anon 67345
      nr_zone_active_anon 140583
      # ...
```

> *Note: These values are in number of pages, not in bytes.*

Normally, the kernel will look for memory to relaim when a zones free memory
drops below the low water mark.

How the high water mark is used is somewhat more of a convoluted story, but the
gist is that if some portion of the file-backed pages plus the free/unused pages
are less than the high water mark then some swapping will commence.

This "portion" is dependent on your configured [[linux-memory-swappiness]]#, but if your
swappiness is set to 0, the swap will commence first after the sum of the the
zones all file-backed pages plus the zones all free/unused pages are less than
the high water mark.

## Controlling how Linux uses swap

Read more about how to control it via the [[linux-memory-swappiness]]# setting

## References

- McKay, D. (2019, December 9). *What Is Swappiness on Linux? (and How to Change
  It)*. How-To Geek. <https://www.howtogeek.com/449691/>

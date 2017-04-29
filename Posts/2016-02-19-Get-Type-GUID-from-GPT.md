---
title: Get Partition Type GUIDs from GPT
date: 2016-02-19 21:13:00 +0800
---

GUID Partition Table (GPT) is a new disk partition system designed to replace
classical DOS (a.k.a MBR) partition system. It is supported by most modern
operating systems.

A GPT system starts with a legacy Master Boot Record (MBR) sector. The next
sector is GPT header, followed by 32 sectors containing GPT partitiion entries.
Each entry occupies 128 bytes and describes one single partition on the disk.
Since a sector's size is 512 bytes, each one can store 4 entries. Thus, the whole
partition table can store at most 32 * 4 = 128 partition entries.

![GPT](https://upload.wikimedia.org/wikipedia/commons/0/07/GUID_Partition_Table_Scheme.svg)

*(By The original uploader was Kbolino at English Wikipedia 
[CC BY-SA 2.5 (http://creativecommons.org/licenses/by-sa/2.5)], via Wikimedia Commons)*

Each partition entry starts with a 16-byte long partition type globally unique
identifier (GUID). It is used to describe the type and general purpose of the
partition. This can provide useful information to digital forensic examiners.

Unfortunately, the current version of the Sleuth Kit (4.1.3) does not display
partition type GUIDs when processing a GPT system directly. But with a little help
of other tools, it won't be difficult to obtain such information from a disk image.
<!--excerpt-->

First, use the `mmls` tool in the Sleuth Kit to list all parts of the disk. Note the
"parts" here include not only real partitions but also partition system records,
including headers and entries and so on.

```shell
$ mmls (image_name)
GUID Partition Table (EFI)
Offset Sector: 0
Units are in 512-byte sectors

     Slot    Start        End          Length       Description
00:  Meta    0000000000   0000000000   0000000001   Safety Table
01:  -----   0000000000   0000002047   0000002048   Unallocated
02:  Meta    0000000001   0000000001   0000000001   GPT Header
03:  Meta    0000000002   0000000033   0000000032   Partition Table
04:  00      0000002048   0002050048   0002048001   Basi
05:  01      0002050049   0002582529   0000532481   EFI 
06:  02      0002582530   0002844674   0000262145   Micr
07:  -----   0002844675   0002846719   0000002045   Unallocated
08:  03      0002846720   0117268479   0114421760   
09:  04      0117268480   0118190079   0000921600   
```

The output shows the partition table is in part number 3. It starts in sector 2 and
ends in sector 33, which is the normal case.

Then, use `mmcat` tool to extract this part, namely the partition table, and store
it into a file.  
`$ mmcat (image_name) 3 > GPT.dd`
    
Note in the command above, 3 is the index of the partition table shown in the output
of mmls, and GPT.dd is the name of the output file where the partition table is stored.

Now that the partition table is extracted, reading partition type GUIDs becomes an
easy task. As mentioned earlier, the first 16 bytes of a partition entry is the 
partition type GUID. And since each partition entry is 128 bytes long, the partition
type GUID will appear every 128 btyes. Knowing this, the task can be finished by the
following bash script:

```
#! /bin/bash

skip_count=0

total_count=0

> GUIDs.dd

while [ $total_count -lt 128 ] ; do
        dd if=GPT.dd bs=16 count=1 skip=$skip_count >> GUIDs.dd
        skip_count=$((skip_count + 8))
        total_count=$((total_count + 1))
done
```

All partition type GUIDs are now stored in GUIDs.dd as a binary file, which is able
to be processed by other programs.

As an example, I wrote a program that reads GUIDs from such binary file and gives
corresponding type description. The source codes of it can be found in
[https://github.com/shujianyang/read_GUID](https://github.com/shujianyang/read_GUID).

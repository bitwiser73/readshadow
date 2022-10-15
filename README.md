# Fun with volume shadow copy

Got two issues while implementing volume shadow copy parser because of *volsnap.sys* behavior.

To check progress while I was making the parser I compared data using my algorithm and Microsoft's through devices like `\\\\?\\GLOBALROOT\\Device\\HarddiskVolumeShadowCopy16`.

Below you will find explanation on the behavior and how to try to reproduce it on your system.

## Dummy successful ReadFile
There are some offsets for which ReadFile will return successfully with the requested length but without modifying the output buffer at all.

`.\readshadow.exe -disk "\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy6" -offset 0x4A61219400 -length 512`

```
0x4a61219400  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219410  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219420  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219430  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219440  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219450  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219460  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219470  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219480  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219490  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612194a0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612194b0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612194c0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612194d0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612194e0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612194f0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219500  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219510  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219520  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219530  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219540  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219550  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219560  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219570  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219580  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219590  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612195a0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612195b0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612195c0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612195d0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612195e0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612195f0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
```

`.\readshadow.exe -disk "\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy6" -offset 0x4A61219400 -length 512 -prefill A`

```
0x4a61219400  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a61219410  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a61219420  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a61219430  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a61219440  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a61219450  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a61219460  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a61219470  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a61219480  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a61219490  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a612194a0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a612194b0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a612194c0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a612194d0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a612194e0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a612194f0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a61219500  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a61219510  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a61219520  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a61219530  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a61219540  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a61219550  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a61219560  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a61219570  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a61219580  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a61219590  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a612195a0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a612195b0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a612195c0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a612195d0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a612195e0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x4a612195f0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
```

I believe that *volsnap.sys* sees this offset as protected because of a "protected block" bitmap built on previous snapshots COW items. This snapshot `HarddiskVolumeShadowCopy6` does not have any COW for this offset but it is still marked as protected by the bitmap. When snapvol.sys will lookup in its block list it will not find anything and it will carry on and return success.


## Successful ReadFile with data corruption
There are some offsets for which ReadFile will return successfully with the requested length but with mixed block and COW block depending of output buffer length.

Below is the output with a small buffer which is the expected content.

`.\readshadow.exe -disk "\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy6" -offset 0x4A61219400 -length 512`

```
0x4a61219400  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219410  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219420  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219430  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219440  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219450  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219460  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219470  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219480  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219490  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612194a0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612194b0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612194c0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612194d0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612194e0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612194f0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219500  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219510  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219520  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219530  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219540  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219550  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219560  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219570  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219580  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219590  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612195a0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612195b0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612195c0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612195d0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612195e0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612195f0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
```

Below the mixed output, the starting offset differs and the buffer is bigger but still ending at the same offsets than previously.

`.\readshadow.exe -disk "\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy6" -offset 0x4A61215600 -length 16384 | grep -A 128 0x4a61219400`

```
0x4a61219400  46 49 4C 45 30 00 03 00 67 84 6D 09 0C 00 00 00  FILE0...g.m.....
0x4a61219410  02 00 01 00 38 00 01 00 50 01 00 00 00 04 00 00  ....8...P.......
0x4a61219420  00 00 00 00 00 00 00 00 04 00 00 00 0D 0C 18 00  ................
0x4a61219430  73 00 00 00 00 00 00 00 10 00 00 00 60 00 00 00  s...........`...
0x4a61219440  00 00 00 00 00 00 00 00 48 00 00 00 18 00 00 00  ........H.......
0x4a61219450  0E D3 61 6B D3 20 D8 01 17 FA 61 6B D3 20 D8 01  ..ak. ....ak. ..
0x4a61219460  17 FA 61 6B D3 20 D8 01 B0 10 36 9B 3D 60 D8 01  ..ak. ....6.=`..
0x4a61219470  20 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ...............
0x4a61219480  00 00 00 00 5D 02 00 00 00 00 00 00 00 00 00 00  ....]...........
0x4a61219490  50 B5 95 A0 02 00 00 00 30 00 00 00 68 00 00 00  P.......0...h...
0x4a612194a0  00 00 00 00 00 00 02 00 50 00 00 00 18 00 01 00  ........P.......
0x4a612194b0  CC 08 00 00 00 00 01 00 0E D3 61 6B D3 20 D8 01  ..........ak. ..
0x4a612194c0  0E D3 61 6B D3 20 D8 01 0E D3 61 6B D3 20 D8 01  ..ak. ....ak. ..
0x4a612194d0  0E D3 61 6B D3 20 D8 01 00 00 00 00 00 00 00 00  ..ak. ..........
0x4a612194e0  00 00 00 00 00 00 00 00 20 00 00 00 00 00 00 00  ........ .......
0x4a612194f0  07 03 74 00 70 00 6D 00 2E 00 50 00 4E 00 46 00  ..t.p.m...P.N.F.
0x4a61219500  80 00 00 00 48 00 00 00 01 00 00 00 00 00 03 00  ....H...........
0x4a61219510  00 00 00 00 00 00 00 00 03 00 00 00 00 00 00 00  ................
0x4a61219520  40 00 00 00 00 00 00 00 00 40 00 00 00 00 00 00  @........@......
0x4a61219530  44 36 00 00 00 00 00 00 44 36 00 00 00 00 00 00  D6......D6......
0x4a61219540  31 04 10 75 08 00 00 00 FF FF FF FF 82 79 47 11  1..u.........yG.
0x4a61219550  FF FF FF FF 82 79 47 11 FF FF FF FF 82 79 47 11  .....yG......yG.
0x4a61219560  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219570  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219580  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a61219590  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612195a0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612195b0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612195c0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612195d0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612195e0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x4a612195f0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 73 00  ..............s.
```

I believe *volsnap.sys* is firstly filling the buffer with the raw data from the physical disk then it patches the buffer with the COW blocks. As the first issue, when the COW blocks are not available for the snapshot the output buffer is not modified and *volsnap.sys* does not return any error.


## More fun with volume shadow copy
On the previous dump at `0x4a61219400` there is an MFT record with the FILE0 marker. It is the record of a file stored in `\Windows\INF\tpm.PNF`.

When comparing the output from my MFT parser with Microsoft's shadow copy with my custom shadow copy implementation, I had some differences on `tpm.PNF`. My parser was showing the correct file but Microsoft's kept showing the one of the oldest snapshot.

Below the correct MFT record my parser was seeing.

`.\readshadow.exe -disk "\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy6" -offset 0x2B09E35400 -length 512`

```
0x2b09e35400  46 49 4C 45 30 00 03 00 0C 78 0C CD 0C 00 00 00  FILE0....x......
0x2b09e35410  02 00 01 00 38 00 01 00 50 01 00 00 00 04 00 00  ....8...P.......
0x2b09e35420  00 00 00 00 00 00 00 00 04 00 00 00 0D 0C 18 00  ................
0x2b09e35430  7F 00 00 00 00 00 00 00 10 00 00 00 60 00 00 00  ............`...
0x2b09e35440  00 00 00 00 00 00 00 00 48 00 00 00 18 00 00 00  ........H.......
0x2b09e35450  0E D3 61 6B D3 20 D8 01 17 FA 61 6B D3 20 D8 01  ..ak. ....ak. ..
0x2b09e35460  17 FA 61 6B D3 20 D8 01 75 46 98 39 DF 65 D8 01  ..ak. ..uF.9.e..
0x2b09e35470  20 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ...............
0x2b09e35480  00 00 00 00 5D 02 00 00 00 00 00 00 00 00 00 00  ....]...........
0x2b09e35490  50 B5 95 A0 02 00 00 00 30 00 00 00 68 00 00 00  P.......0...h...
0x2b09e354a0  00 00 00 00 00 00 02 00 50 00 00 00 18 00 01 00  ........P.......
0x2b09e354b0  CC 08 00 00 00 00 01 00 0E D3 61 6B D3 20 D8 01  ..........ak. ..
0x2b09e354c0  0E D3 61 6B D3 20 D8 01 0E D3 61 6B D3 20 D8 01  ..ak. ....ak. ..
0x2b09e354d0  0E D3 61 6B D3 20 D8 01 00 00 00 00 00 00 00 00  ..ak. ..........
0x2b09e354e0  00 00 00 00 00 00 00 00 20 00 00 00 00 00 00 00  ........ .......
0x2b09e354f0  07 03 74 00 70 00 6D 00 2E 00 50 00 4E 00 46 00  ..t.p.m...P.N.F.
0x2b09e35500  80 00 00 00 48 00 00 00 01 00 00 00 00 00 03 00  ....H...........
0x2b09e35510  00 00 00 00 00 00 00 00 03 00 00 00 00 00 00 00  ................
0x2b09e35520  40 00 00 00 00 00 00 00 00 40 00 00 00 00 00 00  @........@......
0x2b09e35530  44 36 00 00 00 00 00 00 44 36 00 00 00 00 00 00  D6......D6......
0x2b09e35540  31 04 10 75 08 00 00 00 FF FF FF FF 82 79 47 11  1..u.........yG.
0x2b09e35550  FF FF FF FF 82 79 47 11 FF FF FF FF 82 79 47 11  .....yG......yG.
0x2b09e35560  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2b09e35570  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2b09e35580  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2b09e35590  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2b09e355a0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2b09e355b0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2b09e355c0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2b09e355d0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2b09e355e0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2b09e355f0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 7F 00  ................
```

Notice that offset are not the same but the FRN, filename and other bytes are matching. The last modified time is not.

```
0x4a61219400:  05/05/2022 5:04:32 AM (from FILETIME: B0 10 36 9B 3D 60 D8 01)
0x2b09e35400:  05/12/2022 9:04:02 AM (from FILETIME: 75 46 98 39 DF 65 D8 01)
```

My MFT parser when analyzing the volume shadow copy was reporting two files with same FRN when configured to use *volsnap.sys* because, in addition of the previous read issues, the offset `0x4a61219400` was part of the $MFT extents. This record got parsed as others...

## Try this at home
After being unsuccessful to reproduce the behavior on another desktop and disk, I did multiple snapshots and modified serveral files hoping for some cow operations. Finally issue was there again.

1. Enumerate shadow copy volume
`vssadmin list shadows`

```
vssadmin 1.1 - Volume Shadow Copy Service administrative command-line tool
(C) Copyright 2001-2013 Microsoft Corp.

...

Contents of shadow copy set ID: {9de2326c-8adf-4c28-9ffc-4a2a82261ba8}
   Contained 1 shadow copies at creation time: 10/15/2022 11:30:59 AM
      Shadow Copy ID: {52f0292d-ffce-449e-b920-69410973e3a8}
         Original Volume: (C:)\\?\Volume{d93c6a6e-f10d-4757-b804-589ae02956fc}\
         Shadow Copy Volume: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy5
         Originating Machine: bluedev
         Service Machine: bluedev
         Provider: 'Microsoft Software Shadow Copy provider 1.0'
         Type: ClientAccessible
         Attributes: Persistent, Client-accessible, No auto release, No writers, Differential
```

I took the more recent snapshot as I expect it is more likely to have "missing cow" entries.

2. Find a block for which COW resolution likely fail

`.\findshadow.exe -disk "\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy5" -block_size 512`

Block size is the buffer size being use to detect a successful read that will not modify the input buffer.

```
Cow block probably missing: 0x2a3c000
Cow block probably missing: 0x2a3c200
Cow block probably missing: 0x2a3c400
Cow block probably missing: 0x2a3c600
Cow block probably missing: 0x2a3c800
Cow block probably missing: 0x2a3ca00
Cow block probably missing: 0x2a3cc00
Cow block probably missing: 0x2a3ce00
Cow block probably missing: 0x2a3d000
Cow block probably missing: 0x2a3d200
Cow block probably missing: 0x2a3d400
...<Ctrl+C>
```

3. Confirm the read behavior

`.\readshadow.exe -disk \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy5 -offset 0x2a3d400 -length 512`

```
0x2a3d400  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d410  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d420  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d430  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d440  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d450  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d460  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d470  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d480  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d490  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d4a0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d4b0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d4c0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d4d0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d4e0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d4f0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d500  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d510  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d520  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d530  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d540  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d550  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d560  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d570  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d580  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d590  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d5a0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d5b0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d5c0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d5d0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d5e0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
0x2a3d5f0  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
```

`.\readshadow.exe -disk \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy5 -offset 0x2a3d400 -length 512 -prefill A`

```
0x2a3d400  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d410  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d420  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d430  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d440  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d450  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d460  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d470  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d480  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d490  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d4a0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d4b0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d4c0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d4d0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d4e0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d4f0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d500  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d510  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d520  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d530  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d540  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d550  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d560  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d570  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d580  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d590  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d5a0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d5b0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d5c0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d5d0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d5e0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
0x2a3d5f0  41 41 41 41 41 41 41 41 41 41 41 41 41 41 41 41  AAAAAAAAAAAAAAAA
```

4. Read mixed bytes using bigger buffer
`.\readshadow.exe -disk \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy5 -offset 0x2a3d400 -length 65536 -prefill A | grep "0x2a3d400" -A 32`

*BEWARE* if the buffer is filled with prefill value then 'length' parameter may be too small to reproduce the behavior.

```
0x2a3d400  8C E3 F0 94 C3 40 0D 43 6A 19 5C E8 EC A0 8C 06  .....@.Cj.\.....
0x2a3d410  D0 39 84 D2 40 98 86 50 39 86 D3 35 A0 AC 21 58  .9..@..P9..5..!X
0x2a3d420  0E 70 39 03 E6 0E 99 39 6C E7 C0 3D 43 31 9D 0D  .p9....9l..=C1..
0x2a3d430  9F 69 20 95 43 50 0D C3 6A 1C 5C 73 B8 A6 81 CF  .i .CP..j.\s....
0x2a3d440  EB AA B1 58 0D FE 79 D2 CF 76 A0 24 CE DE B9 7C  ...X..y..v.$...|
0x2a3d450  B8 78 00 F0 00 F5 61 E5 21 F0 3A 7D 44 79 80 F4  .x....a.!.:}Dy..
0x2a3d460  F0 F9 41 DE 01 F0 BA 77 04 F1 D0 E3 21 C8 03 94  ..A....w....!...
0x2a3d470  87 3A 0F 0F 1F 80 D7 C1 E3 7A 87 3B ED 1E A4 B5  .:.......z.;....
0x2a3d480  53 E3 73 43 EB 03 EC 87 CD 0F 9D 1E 3E 3E 80 3D  S.sC........>>.=
0x2a3d490  FC 7E 10 7E 40 60 68 21 0C BB 08 3C 6A 12 1F 6F  .~.~@`h!...<j..o
0x2a3d4a0  50 3C 44 75 20 0F 11 78 6F 97 38 8F 23 32 E1 9B  P<Du ..xo.8.#2..
0x2a3d4b0  3F 68 2C 06 80 B6 C3 E4 A1 FB 61 F3 83 E4 03 E9  ?h,.......a.....
0x2a3d4c0  07 CB 0F 98 87 F0 87 D8 0F 9C 1F 3C 1E 72 3F 84  ...........<.r?.
0x2a3d4d0  3F 14 3D 28 7E 18 7E 90 71 B0 2B DC 65 07 7A 0F  ?.=(~.~.q.+.e.z.
0x2a3d4e0  DC 0E C4 1D 90 3B 9E 74 81 37 3D 9D 6E A3 80 46  .....;.t.7=.n..F
0x2a3d4f0  4D 3A 07 1E 3E 34 3D 60 0E 87 07 93 4E 20 F1 6E  M:..>4=`....N .n
0x2a3d500  D4 73 C0 CE 61 3A 07 5B 19 19 BB E5 F0 4E 43 90  .s..a:.[.....NC.
0x2a3d510  1A 07 D7 C8 A5 AF 87 DE F1 A0 BA BE C3 80 87 22  ..............."
0x2a3d520  0F 52 1F 3F 1E C0 85 81 F2 43 E9 61 FD F0 C6 1E  .R.?.....C.a....
0x2a3d530  B2 1E F0 00 DA EC F7 F8 D8 F8 00 F8 20 EF 70 DF  ............ .p.
0x2a3d540  41 C0 43 83 07 95 0F A7 0F 2E 0F 31 1D B3 47 68  A.C........1..Gh
0x2a3d550  5C 88 73 10 39 EC DF 73 69 4C 43 A8 1C 83 6A 1A  \.s.9..siLC...j.
0x2a3d560  56 D7 C0 AF 06 68 AA 81 35 0E AD 34 58 47 FC C1  V....h..5..4XG..
0x2a3d570  61 6A B0 52 03 A0 1A 03 E7 91 4B EA 0F 07 32 03  aj.R......K...2.
0x2a3d580  64 D0 FF 43 14 D5 CC 79 7E 88 74 C0 34 9A B1 FB  d..C...y~.t.4...
0x2a3d590  D0 1A 07 D7 1D 2C 41 5D 66 CD 83 86 C3 3B 1A 42  .....,A]f....;.B
0x2a3d5a0  D6 90 8B 07 87 C8 C1 EE D7 FF 7C 70 44 EE 60 3F  ..........|pD.`?
0x2a3d5b0  9B 3E 18 22 D4 87 43 64 F3 10 10 39 3D C4 44 26  .>."..Cd...9=.D&
0x2a3d5c0  0F 31 C8 D5 21 86 70 C6 1D 84 3B 8C 76 38 ED B0  .1..!.p...;.v8..
0x2a3d5d0  DC E1 BB C3 C3 87 79 07 F8 0E 20 1F 24 1F 14 1E  ......y... .$...
0x2a3d5e0  40 3C B0 78 95 FE 7D 44 68 A6 43 6B 51 7A 49 C0  @<.x..}Dh.CkQzI.
0x2a3d5f0  DB F3 4F C8 93 EA A4 87 03 18 CE 33 E3 3B 80 76  ..O........3.;.v
0x2a3d600  18 ED 70 DB 61 90 C3 81 0F EA 0E E5 1E 7F C4 28  ..p.a..........(
```
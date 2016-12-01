# Segment Debugger

The Segment Debugger is a collection of utilities to inspect a repository created by Apache Jackrabbit Oak's Segment Store.

## Installing

To start using the Segment Debugger, install Go and run 'go get' as shown below.

```
$ go get -u github.com/francescomari/sdb
```

This will retrieve the library and install the `sdb` command line utility into your `$GOBIN` path.

## List TAR files

The `tars` command can be used to list TAR files in a specific folder.

```
$ sdb tars store
data00000b.tar
data00001a.tar
```

TAR files can exits in multiple generations.
By default, the `tars` command shows only the most recent generation of the TAR files.
You can use the `tars` command to list every TAR file in a folder by specifying the `-all` flag.

```
$ sdb tars -all store
data00000a.tar
data00000b.tar
data00001a.tar
```

You don't have to specify the folder on the command line.
If you don't specify the folder, the current working directory is assumed.

```
$ cd store && sdb tars
data00000b.tar
data00001a.tar
```

## List entries in a TAR file

The `entries` command lists the name of the entries in a TAR file, in the same order as they appear in the file.

```
$ sdb entries data00000a.tar | tail
852365fa-9a90-442d-b3cc-382d0615beff.77784815
dd789211-2515-4d34-bba4-5b179b7b794d.90ff266d
613ba5f3-01b3-4571-ba90-64a182d7e7c7.269e8f6b
b92806c8-e895-4a33-bcec-3d12a7262963.6e679142
647cbafa-ac92-4078-b1fd-4a3891216593.e3d9c6ab
5438284c-2280-4dea-b462-cac52f18039d.00780c6d
541f0c4c-b04c-4da8-b5d8-aa29a74c95fe.477d84f5
data00000a.tar.brf
data00000a.tar.gph
data00000a.tar.idx
```

The output below shows that a TAR files produced by the Segment Store is a collection of segment entries and is always terminated by some entries containing metadata about the segments.

## List segment IDs in a TAR file

The `segments` command lists the segment ID associated to every segment entry in a TAR file.
The segment IDs are presented in the same order as their corresponding entries in the TAR file.

```
$ sdb segments data00000a.tar | head
data 0ce1d7f06f464753a42c2374852990c8
data cc505b3d7568419faae2f8b4311911a9
data 2a2db8b66348406aadf309bc1a471b0a
data b0540a20c4e14f41a62d1eb2ae065b86
data 81386db441b441dea59de28d787088ff
data 082729868cd84c74a03b1014d57f242c
bulk 0b400d31c52d43a4b10cb925fb5f9d14
bulk 54527edc88204188b4000805e8c74e66
bulk ae6ce7066599406fb5c0332581f07989
bulk 4ab153cf35c84901bd5c1b74acc2bd0b
```

Segments can either be data or bulk segments.
The first column of the output allows you to easily distinguish between the two types.
Moreover, having the segment type spelled out comes in handy when searching for only a specific kind of segment.

```
$ sdb segments data00000a.tar | grep data
data 0ce1d7f06f464753a42c2374852990c8
data cc505b3d7568419faae2f8b4311911a9
data 2a2db8b66348406aadf309bc1a471b0a
data b0540a20c4e14f41a62d1eb2ae065b86
data 81386db441b441dea59de28d787088ff
data 082729868cd84c74a03b1014d57f242c
data 8b1123b2ba1442acad869ba1bafdbd5c
data ad2ec6b0eaac439cad5eb9277082bcb3
data c73aaf84b38c4498afe57cd86d4d8e62
data 4e0627f5a3f54929a531afe5be4b4a36
data e53c50281d114feba88c124007fbb5e9
data 4e815f3e9b23429aa0ee4b967c7566c1
```

## Show the content of a segment

The `segment` command shows you the hexdump of a segment.
You need to specify the TAR file the segment belongs to and its ID.
It is possible to access to a more readable representation by using the `-format` flag.

```
$ sdb segment data00000a.tar 0ce1d7f06f464753a42c2374852990c8
```

## Show the content of the index

The `index` command prints the content of the TAR index.
By default, the hexdump of the index is shown.
It is possible to access to a more readable representation by using the `-format` flag.

```
$ sdb index -format text data00000a.tar
data 87a95464f29b4160a32d208eb981a645   1c1400 262144      1
data 98997705a85d485aafeea9ae9793958f   141000 262144      1
data 9e5eba1cee174e9faa807ef9b8b1c9ed    40800 262144      0
data c91b6a65fb06475aa751579a47dce7e3   100e00 261984      1
data d492d86cc5a544a7ad69382443777720      200    128      0
data d98ac859618c4b33a1d9de4e9ea7993a      600 262144      0
data 4e5e1704d65b4506adb462ce242e6ba5   181200 262144      1
data 588093be3f6f4bd8a740873d5e2ee755    80a00 262144      0
data 68f948dd3ca7488ea61ab4f658c75f81    c0c00 262144      0
```

The output shows the following columns: the type of the segment, the segment ID, the hexadecimal offset of the segment in the TAR file, the size of the segment, and its generation.

## Show the content of the graph

The `graph` command prints the content of the TAR graph.
By default, the hexdump of the graph is shown.
It is possible to access to a more readable representation by using the `-format` flag.

```
$ sdb graph -format text data00000a.tar
87a95464f29b4160a32d208eb981a645
    4e5e1704d65b4506adb462ce242e6ba5
    c91b6a65fb06475aa751579a47dce7e3
4e5e1704d65b4506adb462ce242e6ba5
    c91b6a65fb06475aa751579a47dce7e3
    98997705a85d485aafeea9ae9793958f
68f948dd3ca7488ea61ab4f658c75f81
    d98ac859618c4b33a1d9de4e9ea7993a
    588093be3f6f4bd8a740873d5e2ee755
    9e5eba1cee174e9faa807ef9b8b1c9ed
98997705a85d485aafeea9ae9793958f
    c91b6a65fb06475aa751579a47dce7e3
9e5eba1cee174e9faa807ef9b8b1c9ed
    d98ac859618c4b33a1d9de4e9ea7993a
588093be3f6f4bd8a740873d5e2ee755
    d98ac859618c4b33a1d9de4e9ea7993a
    9e5eba1cee174e9faa807ef9b8b1c9ed
```

The output shows a graph represented as an adjacency list.
In the output above, segment `87a95464...` has two edges directed to the segments `4e5e1704...`  and `c91b6a65...`.

## Show the content of the binary references index

The `binaries` command prints the content of the binary references index of a TAR file.
By default, the hexdump of the index is shown.
It is possible to access to a more readable representation by using the `-format` flag.

```
$ sdb binaries -format text data00000a.tar
0
    2bfba76dfcc94edca446e0d33273710f
        I45961cb4083e9cb3111b8814ce558dcd0e87fe97#2097152
```

The output shows external binary references grouped by segment first and generation next.
The example above shows that the TAR file contains only segments of generation `0`.
One of those segments is `2bfba76d....` This segment contains only one binary reference, `I45961cb4...`.


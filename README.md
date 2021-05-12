**Use graphsplit only!!!!**

This is packed go language exe:


**Splitting dataset:**

(1)Absolute path: 
"""shell
./graphsplit chunk --car-dir=/mnt/data2/large-file/car --slice-size=1000000000 --parallel=2 --graph-name=monkey_labels --parent-path=. /mnt/data2/large-file/source/monkey_labels.zip
"""
(2)Relative path: ./graphsplit chunk --car-dir=./mnt/data2/large-file/car --slice-size=1000000000 --parallel=2 --graph-name=monkey_labels --parent-path=. ./mnt/data2/large-file/source/monkey_labels.zip

Note: There is a space after "parent-path=."


**Retrieval:**

./graphsplit retrieve --car-path=/mnt/data2/large-file/car/ --output-dir=/mnt/data2/large-file/out --parallel=2

Note: (1) same absolute/relative path as splitting
          (2) the retrieval-ed file path will look like this: /mnt/data2/large-file/out/mnt/data2/large-file/source/monkey_label.zip


Go-graphsplit
==================
> A tool for splitting large dataset into graph slices fit for making deal in the Filecoin Network.


When storing a large dataset in the Filecoin Network, we have to split it into smaller pieces to fit for the size of sector, which could be 32GiB or 64GiB.

At first, we made the dataset into a large tar ball, did chunking this tar ball into pieces, and then used each piece to make deal with storage miners. We did this way for a while until we realized it brought difficulties to retrieval. Even if we only needed to retrieve a small file in the dataset, we had to retrieve all the pieces of the tar ball at first. 

Graphsplit has solved the problem we faced above. It takes advantage of IPLD concepts, following the [unixfs](https://github.com/ipfs/go-unixfs) format datastructures. It regards the dataset or it's sub-directory as a big graph and then cut the big graph into small graphs. The small graphs will keep its file system structure as possible as its in big graph. After that, we only need to organize small graph into a car file according to [unixfs](https://github.com/ipfs/go-unixfs).

## Build
```sh
make
```

## Usage

Splitting dataset:
```sh
# car-dir: folder for splitted smaller pieces, in form of .car
# slice-size: size for each pieces
# parallel: number goroutines run when building ipld nodes
# graph-name: it will use graph-name for prefix of smaller pieces
# parent-path: usually just be the same as /path/to/dataset, it's just a method to figure out relative path when building IPLD graph
./graphsplit chunk \
--car-dir=path/to/car-dir \
--slice-size=17179869184 \
--parallel=2 \
--graph-name=gs-test \
--parent-path=/path/to/dataset \
/path/to/dataset
```
Notes: A manifest.csv will created to save the mapping with graph slice name and the payload cid. As following:
```sh
cat /path/to/car-dir/manifest.csv
payload_cid,filename
Qm...,graph-slice-name.car
```

Import car file to IPFS: 
```sh
ipfs dag import /path/to/car-dir/car-file
```

Retrieve files:
```sh
# car-path: directory or file, in form of .car
# output-dir: usually just be the same as /path/to/output-dir
# parallel: number goroutines run when retrieving
./graphsplit retrieve \
--car-path=/path/to/car-path \
--output-dir=/path/to/output-dir \
--parallel=2
```

## Contribute

PRs are welcome!


## License

MIT


# GeospatialVM

GeospatialVM is a virtual machine that executes programs specified in geospatial formats like WKT, GeoJSON or [WAEL](https://github.com/anthonydgj/wael).

## Usage

To run a program with the VM, use:
```
npm run gvm --program=./path/to/program
```

A program targeted to run on this VM is encoded in a GeoJSON `GEOMETRYCOLLECTION` of `POINT` values interpreted as a list of bytes. For example, the WAEL program `GeometryCollection(1 3, 1 4, 10 0, 1 5, 11 0, 0 0, 1 -1, 12 0)` at `./examples/arithmetic.wael` is interpreted as bytes `1, 3, 1, 4, 10, 0, 1, 5, 11, 0, 0, 0, 1, -1, 12, 0`.

This program can be run with the following command:
```
npm run gvm --program=./examples/arithmetic.wael
```

The output from this command is:
```
GEOMETRYCOLLECTION (POINT (-2 0))
```

### Instruction Set

The instruction set implemented in `./src/virtual-machine.wael` and include:
```
0 = no-op
1 = push the next byte onto the stack
2 = pop the last byte from the stack 

10 = add the top 2 entries on the stack
11 = subtract the top 2 entries on the stack
12 = multiply the top 2 entries on the stack
13 = divide the top 2 entries on the stack
```

### Instruction Transformation

Instructions encoded in spatial data formats can be transformed by providing a `TRANSFORM` function.

For example, if instructions are encoded as follows:
```
GeometryCollection(1 3, 1 4, 10 0, 1 5, 11 0, 0 0, 1 -1, 12 0) - 12.5 3.2
```

They can be transformed to instructions understood by the VM with the command:
```
pm run gvm --program=./examples/arithmetic.wael -- -e="TRANSFORM = (v) => (v + 12.5 3.2)"
```

Which will output the same result as above:
```
GEOMETRYCOLLECTION (POINT (-2 0))
```
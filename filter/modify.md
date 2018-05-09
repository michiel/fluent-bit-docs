# Modify Filter

The _Modify Filter_ plugin allows you to change the structure of a record by
performing operations based on the keys of its key/value pairs.

Operations are applied in the order they appear in the configuration. Each
operation acts on the result of the previous operation.

For example, if you use `Add` to create a new key-value pair you can apply the
`Copy` operation to that pair after that.

## Configuration Parameters

The plugin supports the following configuration parameters:

| Operation   | Parameter 1    | Parameter 2          |  Description  |
|-------------|----------------|----------------------|---------------|
| Set         | STRING:FIELD   | STRING:VALUE         | Add a key/value pair with key `FIELD` and value `VALUE`. If `FIELD` already exists, *this field is overwritten* |
| Add         | STRING:FIELD   | STRING:VALUE         | Add a key/value pair with key `FIELD` and value `VALUE` if `FIELD` does not exist |
| Remove      | STRING:FIELD   | NONE                 | Remove a key/value pair with key `FIELD` if it exists |
| Rename      | STRING:FIELD   | STRING:RENAMED_FIELD | Rename a key/value pair with key `FIELD` to `RENAMED_FIELD` if `FIELD` exists AND `RENAMED_FIELD` *does not exist* |
| HardRename  | STRING:FIELD   | STRING:RENAMED_FIELD | Rename a key/value pair with key `FIELD` to `RENAMED_FIELD` if `FIELD` exists. If `RENAMED_FIELD` already exists, *this field is overwritten* |
| Copy        | STRING:FIELD   | STRING:COPIED_FIELD  | Copy a key/value pair with key `FIELD` to `COPIED_FIELD` if `FIELD` exists AND `COPIED_FIELD` *does not exist* |
| HardCopy    | STRING:FIELD   | STRING:COPIED_FIELD  | Copy a key/value pair with key `FIELD` to `COPIED_FIELD` if `FIELD` exists. If `COPIED_FIELD` already exists, *this field is overwritten* |

## Getting Started

In order to start filtering records, you can run the filter from the command line or through the configuration file.
The following invokes the [Memory Usage Input Plugin](../input/mem.html), which outputs the following (example),

```
[0] memory: [1488543156, {"Mem.total"=>1016044, "Mem.used"=>841388, "Mem.free"=>174656, "Swap.total"=>2064380, "Swap.used"=>139888, "Swap.free"=>1924492}]
[1] memory: [1488543157, {"Mem.total"=>1016044, "Mem.used"=>841420, "Mem.free"=>174624, "Swap.total"=>2064380, "Swap.used"=>139888, "Swap.free"=>1924492}]
[2] memory: [1488543158, {"Mem.total"=>1016044, "Mem.used"=>841420, "Mem.free"=>174624, "Swap.total"=>2064380, "Swap.used"=>139888, "Swap.free"=>1924492}]
[3] memory: [1488543159, {"Mem.total"=>1016044, "Mem.used"=>841420, "Mem.free"=>174624, "Swap.total"=>2064380, "Swap.used"=>139888, "Swap.free"=>1924492}]
```

### Command Line

> Note: Using the command line mode requires quotes parse the wildcard properly. The use of a configuration file is recommended.

The following command will load the _mem_ plugin.

```
bin/fluent-bit -i mem \
  -p 'tag=mem.local' \
  -F modify \
  -p 'Set=Service1 VAL_SERVICE1' \
  -p 'Set=Service2 VAL_SERVICE2' \
  -p 'Set=Service3 VAL_SERVICE3' \
  -p 'Hardrename=Service2 Service3' \
  -p 'Add=Mem.total2 TOTALMEM2' \
  -p 'Rename=Mem.free MEMFREE' \
  -p 'Copy=Mem.used MEMUSED' \
  -p 'Copy=Mem.used MEMUSEDCOPY2' \
  -p 'HardCopy=Mem.used Service1' \
  -p 'Rename=Swap.total SWAPTOTAL' \
  -p 'Add=Mem.total TOTALMEM' \
  -p 'Remove=MEMUSEDCOPY2' \
  -m '*' \
  -o stdout
```

### Configuration File

```python
[INPUT]
    Name mem
    Tag  mem.local

[OUTPUT]
    Name  stdout
    Match *

[FILTER]
    Name modify
    Match *
    Set Service1 VAL_SERVICE1
    Set Service2 VAL_SERVICE2
    Set Service3 VAL_SERVICE3
    Hardrename Service2 Service3
    Add Mem.total2 TOTALMEM2
    Rename Mem.free MEMFREE
    Copy Mem.used MEMUSED
    Copy Mem.used MEMUSEDCOPY2
    HardCopy Mem.used Service1
    Rename Swap.total SWAPTOTAL
    Add Mem.total TOTALMEM
    Remove MEMUSEDCOPY2
```

### Result

The output of both the command line and configuration invocations should be identical and result in the following output.

```
[2018/04/06 01:35:13] [ info] [engine] started

```

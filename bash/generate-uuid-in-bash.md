# Generate UUID in bash 

I had to generate UUID in a shell script without using any external libraries. I found this simple command which
generates the UUID

```
$ cat /proc/sys/kernel/random/uuid
00db2531-365c-415c-86f7-503a35fafa58
```

This command generates a uuid with the alphabets in lower case. `/proc/sys/kernel/random/uuid` is a type 4 (random) UUID with certain semantics - it's not just a string of random hex characters. For example you'll see the first digit in the third group is always a 4.

In case you want it to be stored in a variable 

```
$ UUID=$(cat  /proc/sys/kernel/random/uuid)
echo $UUID
```

In addition to `uuid`, unix system generates a `boot_id` which is generated only once, while `uuid` is generated everytime is read. Both of them are read only files.
# Using `uuidgen`
The uuidgen program creates (and prints) a new universally unique identifier (UUID) using the libuuid(3) library. The new UUID can reasonably be considered unique among all UUIDs created on the local system, and among UUIDs created on other systems in the past and in the future. By default uuidgen would generate random-based UUID.

```bash 
❯ uuidgen
00cc827b-eafc-4b52-878a-d3df3e025bef
```

# Using python
```bash
❯ python -c "import uuid; print(uuid.uuid1())"
5a954f0e-b773-11ef-8602-dc4628cbb6af
```


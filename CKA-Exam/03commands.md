# Commands

Remember this commands if you need to use them in the exam (eg. backups, binaries, performance, etc)


## 1. Change Password

To improve velocity setting commands maybe you need to change the password

```
passwd
```

## 2. compress/extract

### TAR


*Compress*

```
tar -czvf name-of-archive.tar.gz /path/to/directory-or-file
```

*Extract*

```
tar -xzvf archive.tar.gz
```

- c: Create an archive.
- x: extract an archive
- z: Compress the archive with gzip.
- v: Display progress in the terminal while creating the archive, also known as “verbose” mode. The v is always optional in these commands, but it’s helpful.
- f: Allows you to specify the filename of the archive.


### ZIP

*Compress*

```
zip myzip file1
```

*Extract*

```
unzip myzip.zip
```
---
title: Bash
---
# Useful Bash Commands, Snippets and Oneliners

## Display the access rights in octal of all files with in a directory

```bash
stat -c "%a %n" *
```

## Diff-ing a local and remote file

```bash
ssh node1.example.com cat '/full/file/path/text.txt' \
| diff -y --suppress-common-lines /full/file/path/text.txt -
```

## Diff-ing files on two remote systems

```bash
diff -y --suppress-common-lines \
<(ssh node1.example.com 'cat /full/file/path/text.txt') \
<( ssh node2.example.com 'cat /full/file/path/text.txt')
```

## Sort files by human readable size

```bash
du -sk * | sort -rn | awk '{print $2}' | xargs -ia du -hs "a"
```
```bash
du -sk * | sort -rn | awk '{ split( "KB MB GB" , v ); s=1; while( $1>1024 ){ $1/=1024; s++ } print int($1) v[s], $2 }'
```

## Determine inode Usage ( This can take several minutes to complete )
In a directory where you suspect there might be a lot of inode usage run the following

```bash
find . -type d |
while
    read line
    do
        echo "$( find "$line" -maxdepth 1 | wc -l) $line"
        done |
        sort -rn | less
```

## Test GPG/PGP Passphrase

```bash
echo "1234" | gpg --no-use-agent -o /dev/null --local-user <keyID> -as - \
&& echo "The correct passphrase was entered for this key"
```

## List Linux processes using SWAP memory

```bash
for file in /proc/*/status ; do awk '/VmSwap|Name/{printf $2 " " $3}END{ print ""}' $file; done | sort -k 2 -n -r | less
```

## List the free PV disk space not used by LVM
```bash
freepe=$(sudo pvdisplay | grep 'Free PE' | awk '{ print $3 }') \
&& pesize=$(sudo pvdisplay | grep 'PE Size' | awk '{ print int( $3 ) }') \
&& mbpe=$(expr ${freepe} \* ${pesize}) && echo $(expr ${mbpe} / 1024) GB
```

## References


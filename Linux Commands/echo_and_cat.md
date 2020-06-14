# echo and cat

If you just want to create a file, without even doing
an editor, there are two standard ways to just
create one from the command line and fill it with
content.

The first one is **echo**:
```bash
    $ echo line one > myfile
    $ echo line two >> myfile
    $ echo line three >>> myfile
```

Second one is **cat** where you can edit text and read as well.
#### And this second way is use  ***cat*** combined with redirection:
```bash
    $ cat << EOF > myfile
    > line one
    > line two
    > line three
    > EOF
```
**EOF** <-- "End of File" (Operation: There is no more data to read)


Here, **<<**  is redirection operator



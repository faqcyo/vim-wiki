[Go back to PortSwigger Index](index)

# Path Traversal

These vulnerability enables an attacker to read arbitrary files on the server.

Usually an attacker applies path traversal to read `/etc/passwd` on linux or `\windows\win.ini` on windows

Note that on windows systems, both `../` and `..\` are valid file traversal sequences

1. Try with absolute path: `/etc/passwd`
2. Start adding some file traversal characters: `../../ ... /../etc/passwd`
3. If the application does some sanitization without recursion try replacing `../` with `....//` (or `....\/` for windows)
4. Try with a URL encoded or a double URL encoded version of the file traversal sequence.
5. Some web applications require some directory to be specified for it to be accepted. If this is the case try something like `/path/to/file/../../../etc/passwd`
6. Try to add a null character followed by a file extension, for example: `../../etc/passwd%00.png`


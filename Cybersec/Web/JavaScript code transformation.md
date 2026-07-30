[[WEB]]
## JavaScript Obfuscation Websites
- https://obfuscator.io
- https://beautifytools.com/javascript-obfuscator.php#
- https://www.toptal.com/developers/javascript-minifier
- https://jsfuck.com/
- https://utf-8.jp/public/jjencode.html
- https://utf-8.jp/public/aaencode.html

## JavaScript **deobfuscate** Websites
#### To de minify the js code 
- https://beautifier.io/
- https://prettier.io/playground/
#### To **deobfuscate**  the js code
- https://matthewfl.com/unPacker.html

#### Spotting Decoded strings

**BASE64**
	 distinctive feature of `base64` is its 
- padding using `=` characters
- only contain alpha-numeric characters
**HAX Code**
	distinctive feature of `HAX` is its 
- 0-9 and a-f characters only
**Caesar/Rot13**
	distinctive feature of `ROT` is its
- each character is mapped to a specific character
- `http://www` becomes `uggc://jjj`

#### Encoding strings
**BASE64**
``` 
echo https://www.hackthebox.eu/ | base64
```

**HAX**
``` 
echo "String" | xxd -p 
```

**Caesar/Rot13**
```
echo "string" |
tr 'A-Za-z' 'N-ZA-Mn-za-m' 
```
#### Decoding strings

**Base64** ```
```
echo "Encoded String" | base64 -d
```
 
**HAX** 
```
echo "Encoded String" | xxd -p -r
```

**Caesar/Rot13**

```
echo "Encoded String" | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```
- https://rot13.com/

#### Cipher Identifier
- https://www.boxentriq.com/code-breaking/cipher-identifier

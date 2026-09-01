
### variables

name = "zinon"

*they can be accessed with **$name**

### Loops

**for** loops 

``` bash
for variable in range/value
do
	<snip>
done
```

### Arguments & Special Parameters


| Special Variables | description                     |
| ----------------- | ------------------------------- |
| $0                | script name as in blah.sh       |
| $1                | First argument given to program |
| $2                | Second argument                 |
| $3..              | to infiinty arg                 |
| $#                | total numbers of argumetns      |







# Conditions

**IF confiditon** 
```bash
#!/bin/bash

if [ $# -eq 0 ]
then
	echo "bro no arguments duh0"
fi

```
**IF Elif ELSE confiditon** 

```bash
#!/bin/bash

if [ $# -eq 0 ]
then
	echo "bro no arguments duh0"

elif [ $# -gt 0 ]
then
	echo "yo this fker got arg"
else
	echo "tbh i have no idea wht tf this fker output"
fi
```

### Command result in variable 

```bash
var=$(echo "something"| base64)
```
variabl=$(any command)

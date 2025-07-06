# Detection
## Numeric Param
- If u find a numeric param like `id= 2`, try making a mathematic operation on it so that results in the same result like `id= 5-3`
## String param 
- Try put `' `and `"` quotes and observe for aby error
- if there's no error maybe it's handled but this doesn't mean that there's no SQLI
- Try to inject in the middle if the string that being sent different concatenation methods like ` || `, `' '` (Blank space) , `CONCAT()`, `+` (plus) if the strings concatenated successfully there is a high chance that there is SQLI
- Try commenting at the end of your payload but don't always rely on it sometimes it won't work for the complex queries
- Instead of comments, try to match the quotes ex-> ` Sirreda' and 1='1` (the single quote before the 1 will be closed with the one in the original query in the backend)
- 
# Insecure\_Deserialization

### PHP serialization format

letters representing the data type and numbers representing the length of each entry. For example, consider a User object with the attributes:

```php
$user->name = "carlos";
$user->isLoggedIn = true;

# Serilized
O:4:"User":2:{s:4:"name":s:6:"carlos"; s:10:"isLoggedIn":b:1;}
```

This can be interpreted as follows:

* `O:4:"User"` - An object with the 4-character class name `"User"`
* `2` - the object has 2 attributes
* `s:4:"name"` - The key of the first attribute is the 4-character string `"name"`
* `s:6:"carlos"` - The value of the first attribute is the 6-character string `"carlos"`
* `s:10:"isLoggedIn"` - The key of the second attribute is the 10-character string `"isLoggedIn"`
* `b:1` - The value of the second attribute is the Boolean value `true` If you are on white box pentesting and have search for `serialize()` and `unserialize()` functions

### Checklist

* [ ] look for serialized objects in cookies, headers and use inspector to inspect, decode them
* [ ] Modifying object attributes `O:4:"User":2:{s:8:"username";s:6:"carlos";s:7:"isAdmin";b:0;}`
* [ ] Modifying data types `O:4:"User":2:{s:8:"username";s:13:"administrator";s:12:"access_token";i:0;}`
* [ ] You can sometimes read source code by appending a tilde (`~)` to a filename to retrieve an editor-generated backup file. `/libs/CustomTemplate.php~`
* [ ] If u have access to the source code test for (Injecting arbitrary objects, Magic methods, Gadget chains)

### **Mitigation**

Generally speaking, deserialization of user input should be avoided unless absolutely necessary

* implement a digital signature to check the integrity of the data.
* any checks must take place **before** beginning the deserialization process
* If possible, you should avoid using generic deserialization features altogether Instead, you could create your own class-specific serialization methods so that you can at least control which fields are exposed.

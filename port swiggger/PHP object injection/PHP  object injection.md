
the php object injection is that , the user input data is not properly sanitized before being passed to the unserialize() PHP function. Since PHP allows object serialization, attackers could pass ad-hoc serialized strings to a vulnerable unserialize() call, resulting in an arbitrary PHP object(s) injection into the application scope. 

## Serialization: 

In PHP, variables like integers or strings are easy to store. However, **Objects** and **Arrays** are complex; they have structures, nested levels, and (in the case of objects) specific class associations.

**Serialization** converts these complex structures into a **plain text string**. This string contains all the information needed to recreate the original data later.

### How it looks:

If we have a simple object:

PHP

```
class User {
    public $name = "Alex";
    public $isAdmin = true;
}
$user = new User();
echo serialize($user);
```

**The Output String:** `O:4:"User":2:{s:4:"name";s:4:"Alex";s:7:"isAdmin";b:1;}`

**Breaking down the grammar:**

- `O:4:"User"`: An **O**bject with a **4**-letter class name "**User**".
    
- `2`: It has **2** properties.
    
- `s:4:"name"`: A **s**tring of **4** characters called "**name**".
    
- `s:4:"Alex"`: The value is a **s**tring of **4** characters "**Alex**".
    
- `b:1`: A **b**oolean value of **1** (true).
    

---

##  Unserialize: "Reanimating" the Data

**Unserialization** takes that formatted string and converts it back into a PHP variable or object.

When you call `unserialize()`, PHP:

1. Reads the string to identify the class (e.g., "User").
    
2. Checks if that class is already defined in the current script.
    
3. Creates a new instance of that class.
    
4. Fills the instance with the saved values (name, isAdmin).

## What is magic moments ??

the magic moments are that , they function that are called depend upon the event , not upon the developer intended , for example when the script is finish the line it automatically calls the __destruct . not the developer written the __destruct()

the vulnerable code :

```
class Example1
{
   public $cache_file;

   function __construct()
   {
      // some PHP code...
   }

   function __destruct()
   {
      $file = "/var/www/cache/tmp/{$this->cache_file}";
      if (file_exists($file)) @unlink($file);
   }
}

// some PHP code...

$user_data = unserialize($_GET['data']);

// some PHP code...


```


and in this , the user data is directly passed in to unserialized function and then if the user enter like ```
?data=O:8:"Example1":1:{s:10:"cache_file";s:15:"../../index.php";}
```
```


then it will do the path traversal and then delete the file name index.php , unlink is used to delete the file




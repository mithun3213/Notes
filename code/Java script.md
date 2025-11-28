

the java script should be inside the 
<script>
.............
</script>

-------alert("hi")
will pop up the message when we enter the website or reload the website

----------console.log("HI")
will print the string in the console browser

---->**Variable:**
let score=10
console.log(score)
Output:
10

****
--->**Arithmetic operator:**

let a=10
let b=10
a+b
a-b
a ** b
a/b
****

**Post increment and Pre increment:**
a=10
b=++a
b=11,a=11
a=10
b=a++
b=10
a=11

****

**Typeof**
it is used to represent the datatype
let a='abc'
typeof a
o/p-->string

****
Comparison operator:

age=10 

age>10 F
age<10 F
age !=10 F
age == 10 T

****
Conditional operator :

AND &&
OR ||
NOT !

if single & is used to specify the bitwise operator

a<<1 --> leftshift operator 
a<<1 --> rightshift operator

****

USER INPUT:
the simple user input is 
let name =prompt("Enter the name :")
console.log(name)
o/p--> the user input will be in the console

**note : the input which is get from the prompt is always be the string**

****
<!DOCTYPE html>
<html>
    <body>
        <h1 id="message"></h1>
        <button type="button"
        onclick ="document.getElementById('demo').innerHTML = Date()">
        click here to display the date and time </button>
        <script >
            let message=document.getElementById('message')
            message.innerHTML="Welcome to my webiste"
        </script>
        <p id="demo"></p>
    </body>
</html>

in this script , the html content header <h> in controled via the js script 

-------------------------------------------------------------------------------------------------------------

simple program to convert the cm to inches 

<!DOCTYPE html>
<html>
    <body>
    <div id="wrapper">
        <h2>cm to inches</h2>
        <div>
        <input type="text" id="input" placeholder="lenght in cm">
        </div>
        <div>
            <button type="submit" onclick="check()">convert to inches</button>
        </div>
        <div id="result"></div>
    </div>
        <script>
            function check(){
                let cmVal=Number(document.getElementById("input").value)
            let inchVal=cmVal/2.54
            let result=document.getElementById("result")
            result.innerHTML=inchVal.toFixed(2) + " inches"
            }
        </script>
    </body>
</html>

-------------------------------------------------------------------------------------------------------------
Simple program to convert celsius to Fahrenhiet:

<!DOCTYPE html>
<html>
    <div id="wrapper">
    <body>
     <div>
        <input type="text" id="input" placeholder="Enter the celsius">
     </div>
     <div>
        <button type="submit" onclick="convert()"> convert celsius to Fahrenheit </button>
     </div>
     <div id="result"></div>
    </body>
    </div>
    <script>
        function convert(){
        const celVar=Number(document.getElementById("input").value)
        const fahVar=(celVar*9/5)+32
        const result=document.getElementById("result")
        result.innerHTML=fahVar
        }
    </script>
</html>
-------------------------------------------------------------------------------------------------------------

Math operator:

Math.abs(-4)
Math.pow(3,4)
Math.sqrt(4)
Math.random()
Math.floor()
Math.ceil()

-------------------------------------------------------------------------------------------------------------

strings:

for example :
srt1= "HI I am mithun sankar"
srt2= "MIthun sankar"

srt1+srt2 ---> "HI I am mithun sankarMIthun sankar"
srt1.concat(srt2) --> "HI I am mithun sankarMIthun sankar"
srt2.slice(0,4) --> "MIthu"
bill.padEnd(7,'0')  --->"Rs.1000"
srt1.toUpperCase()  -->"HI I AM MITHUN SANKAR"
srt1[5]  --->"a"
srt1.charCodeAt(1) -->73 (unique value)
srt1.indexOf('H')  --->0 index of the specific character
srt1.includes('HI')  -->true


-------------------------------------------------------------------------------------------------------------

Template literals

`${fname} ${lname} is a good boy`  -->"mithun sankar is a good boy"  

`mithun sankar`  --->"mithun sankar"  

`cat's name is ram`  --->"cat's name is ram"

-------------------------------------------------------------------------------------------------------------




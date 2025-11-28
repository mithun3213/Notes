--->the simple code  with the main and print :
public class main{
    public static void main(String[] agrs){
        System.out.println("hello world ");
    }
}

--> System.out.println(358); for the number 
--->System.out.println(2 * 5);


------> declare the data type 
public class main{
    public static void main(String[] agrs){
        String name="john";
        System.out.println("hello world " + name);
    }
}

---> the command final 
```java
final int myNum = 15;
myNum = 20;
```
where the value of the myNum will not changed

----> for the float we need to add f at end , for example 
```java
float studentFee = 75.25f;
```

----> for to calculate the area of the rectangle 

public class main{
    public static void main(String[] agrs){
        int len=3;
        int wid=4;
        int res=len*wid;
        System.out.println("the length is : " + len);
        System.out.println("the width is : " + wid);
        System.out.println("the area is : " + res);
    }
}

---> a simple method defining ,

public class main{
    static void mymet(){
       long a=10;
        System.out.println("the length is : " + a);
      }
    public static void main(String[] agrs){
      mymet();
    }
}

---> simple passing arg,

public class main{
    static void mymet(String name){
        System.out.println("the length is : " + name);
      }
    public static void main(String[] agrs){
      mymet("mtihun");
    }
}

---> returning the value ,

public class main{
    static int add(int a,int b){
       return a+b;
      }
    public static void main(String[] agrs){
     int a=1,b=2;
     int res=add(a,b);
      System.out.println("The add of the two number : " + res);
    }
}

----> simple abstract modifier ,

abstract class member{
    String Name;
    abstract void welcome();
}

class student extends member{
    void welcome(){
        System.out.println("You are the student ");
    }
}
class teacher extends member{
    void welcome(){
        System.out.println("You are the teacher ");
    }
}   
public class abstractdemo{
    public static void main(String[] agrs){
        member[] m= new member[5];
        m[0]=new student();
        m[0].welcome();
    }
}


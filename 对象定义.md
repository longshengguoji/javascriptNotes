## javaScript面向对象
* 工厂方式

``` javascript
function createStudent(){
  var tempStudent = new Object;
  tempStudent.name = "zhangsan";
  tempStudent.age = 28;
  tempStudent.phone = "13645531787";
  tempStudent.getName = function(){
    alert(this.name);
  }
  return tempStudent;
}

var student1 = createStudent();
var student2 = createStudent();
```
工厂方法模式创建一个临时对象，赋予他必要的属性和方法，并返回。因此，通过这种方法，我们可以很容易地创建 student 对象的两个版本（student1 和 student2），它们的属性完全一样。<br/>
**为函数传递参数**
我们还可以修改createStudent()函数，给他传递各个属性的默认值，而不是简单地赋予属性默认值：
``` javascript
function createStudent(name,age,phone){
  var tempStudent = new Object;
  tempStudent.name = name;
  tempStudent.age = age;
  tempStudent.phone = phone;
  tempStudent.getName = function(){
    alert(this.name);
  }
  return tempStudent;
}

var student1 = createStudent("zhangsan",20,"13645531787");
var student2 = createStudent("lisi",21,"18969197165");

student1.getName();
student2.getName();
```
通过给createStudent()函数加上参数，即可为要创建的student对象的name、age、phone属性赋值。这使两个对象具有相同的属性，却有不同的属性值。<br/>
**在工厂函数外定义对象的方法**
前面的例子中，每次调用函数 createStudent()，都要创建新函数 getName()，意味着每个对象都有自己的 getName() 版本。而事实上，每个对象都共享同一个函数。<br/>
有些开发者在工厂函数外定义对象的方法，然后通过属性指向该方法，从而避免这个问题：<br/>
``` javascript
function getName(){
  alert(this.name);
}
function createStudent(name,age,phone){
  var tempStudent = new Object;
  tempStudent.name = name;
  tempStudent.age = age;
  tempStudent.phone = phone;
  tempStudent.getName = getName;
  return tempStudent;
}

var student1 = createStudent("zhangsan",20,"13645531787");
var student2 = createStudent("lisi",21,"18969197165");

student1.getName();
student2.getName();
```
上面这段重写的代码中，在函数createStudent()之前定义了函数getName().在createStudent()内部，赋予对象一个指向已经存在的getName()函数的指针。从功能上讲，这样解决了重复创建函数对象的问题；但是从语义上将，该函数不太像是对象的方法。

* 构造函数方式
创建构造函数就像创建工厂函数一样容易。第一步选择类名，即构造函数的名字。根据惯例，这个名字的首字母大写，以使它与首字母小写的变亮区分开。除了这点不同，构造函数看起来很像工厂函数。
``` javascript
function Student(name,age,phone){
  this.name = name;
  this.age = age;
  this.phone = phone;
  this.getName = function(){
    alert(this.name);
  };
}

var student1 = new Student("zhangsan",20,"13645531787");
var student2 = new Student("lisi",21,"18969197165");

student1.getName();
student2.getName();
```
**构造函数方式与工厂方式的区别**
首先在构造函数内没有创建对象，而是使用 this 关键字。使用 new 运算符构造函数时，在执行第一行代码前先创建一个对象，只有用 this 才能访问该对象。然后可以直接赋予 this 属性，默认情况下是构造函数的返回值（不必明确使用 return 运算符）。
现在，用 new 运算符和类名 Car 创建对象，就更像 ECMAScript 中一般对象的创建方式了。但是，这种方式在管理函数方面是否存在于前一种方式相同的问题：会重复生成函数。
* 原型方式
原型方式利用了对象的prototype属性，可以把它看成是创建新对象所依赖的原型。<br/>
首先利用空函数来设置类名。然后所有的属性和方法都被直接赋予prototype属性。
``` javascript
function Student(){
}
  Student.prototype.name = "zhuwei";
  Student.prototype.age = 30;
  Student.prototype.phone = "13654546321";
  Student.prototype.getName = function(){
    alert(this.name);
  };


var student1 = new Student();
var student2 = new Student();

student1.getName();
student2.getName();
```
**原型方式的好处：**
原型方式的所有属性和方法都属于一个对象，解决了重复生成函数的问题。另外还能使用instanceof运算符检查给定变量指向的对象的类型。
``` javascript
student1 instanceof Student
```
**原型方式带来的问题**
首先，这个构造函数没有参数。使用原型方式，不能通过给构造函数传递参数来初始化属性的值，必须在对象创建后才能改变属性的默认值。<br/>
其次，属性指向的是对象而不是函数时会出现问题，此时所有对象的该属性执行的是同一个对象的地址。
* 混合构造函数和原型方式
联合使用构造函数和原型方式，就可像用其他程序设计语言一样创建对象。这种概念非常简单，即用构造函数定义对象的所有非函数属性，用原型方式定义对象的函数属性（方法）。结果是，所有函数都只创建一次，而每个对象都具有自己的对象属性实例。
``` javascript
function Student(name,age,phone){
  this.name = name;
  this.age = age;
  this.phone = phone;
  Student.prototype.getName = function(){
    alert(this.name);
  };
}

var student1 = new Student("zhangsan",20,"13645531787");
var student2 = new Student("lisi",21,"18969197165");

student1.getName();
student2.getName();
```
* 动态原型方法
动态原型方法的基本想法与混合的构造函数/原型方式相同，即在构造函数内定义非函数属性，而函数属性则利用原型属性定义。唯一的区别是赋予对象方法的位置。
``` javascript
function Student(name,age,phone){
  this.name = name;
  this.age = age;
  this.phone = phone;
  if(typeof Student._initialized == "undefined"){
    Student.prototype.getName = function(){
      alert(this.name);
    };
    Student._initialized = true;
  }

}

var student1 = new Student("zhangsan",20,"13645531787");
var student2 = new Student("lisi",21,"18969197165");

student1.getName();
student2.getName();
```
* 混合工厂方式
这种方式通常是在不能应用前一种方式时的变通方法。它的目的是创建假构造函数，只返回另一种对象的新实例。<br/>
这段代码看起来与工厂函数非常相似：
``` javascript
function Student(){
  var tempStudent = new Object;
  tempStudent.name = "zhangsan";
  tempStudent.age = 28;
  tempStudent.phone = "13645531787";
  tempStudent.getName = function(){
    alert(this.name);
  }
  return tempStudent;
}

var student1 = new Student();
var student2 = new Student();
```
与经典方式不同，这种方式使用 new 运算符，使它看起来像真正的构造函数：<br/>
由于在 Student() 构造函数内部调用了 new 运算符，所以将忽略第二个 new 运算符（位于构造函数之外），在构造函数内部创建的对象被传递回变量 student。<br/>
这种方式在对象方法的内部管理方面与经典方式有着相同的问题。强烈建议：除非万不得已，还是避免使用这种方式。
* 采用那种方式
如前所述，目前使用最广泛的是混合的构造函数/原型方式。此外，动态原始方法也很流行，在功能上与构造函数/原型方式等价。可以采用这两种方式中的任何一种。不过不要单独使用经典的构造函数或原型方式，因为这样会给代码引入问题。

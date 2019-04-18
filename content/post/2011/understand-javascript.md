---
title: 悟透JavaScript笔记
date: 2011-11-08
categories: 
- ui
tags: 
- javascript
---
原生闭包


``` javascript
function Person(firstName, lastName, age)
{
    //私有变量：
    var _firstName = firstName;
    var _lastName = lastName;

    //公共变量:
    this.age = age;

    //方法：
    this.getName = function()
    {
        return(firstName + " " + lastName);
    };
    this.SayHello = function()
    {
        alert("Hello, I'm " + firstName + " " + lastName);
    };
};

var BillGates = new Person("Bill", "Gates", 53);
var SteveJobs = new Person("Steve", "Jobs", 53);

BillGates.SayHello();
SteveJobs.SayHello();
alert(BillGates.getName() + " " + BillGates.age);
alert(BillGates.firstName);     //这里不能访问到私有变量
```


甘露模型


``` javascript
//定义类的语法甘露：Class()
//最后一个参数是JSON表示的类定义
//如果参数数量大于1个，则第一个参数是基类
//第一个和最后一个之间参数，将来可表示类实现的接口
//返回值是类，类是一个构造函数
function Class()
{
    var aDefine = arguments[arguments.length-1]; //最后一个参数是类定义
    if(!aDefine) return;
    var aBase = arguments.length>1 ? arguments[0] : object; //解析基类
    
    function prototype_(){}; //构造prototype的临时函数，用于挂接原型链
    prototype_.prototype = aBase.prototype;  //准备传递prototype
    var aPrototype = new prototype_();    //建立类要用的prototype
    
    for(var member in aDefine)  //复制类定义到当前类的prototype
        if(member!="Create")    //构造函数不用复制
            aPrototype[member] = aDefine[member];
            
    //根据是否继承特殊属性和性能情况，可分别注释掉下列的语句
    if(aDefine.toString != Object.prototype.toString)
        aPrototype.toString = aDefine.toString;
    if(aDefine.toLocaleString != Object.prototype.toLocaleString)
        aPrototype.toLocaleString = aDefine.toLocaleString;
    if(aDefine.valueOf != Object.prototype.valueOf)
        aPrototype.valueOf = aDefine.valueOf;

    if(aDefine.Create)  //若有构造函数
        var aType = aDefine.Create  //类型即为该构造函数
    else    //否则为默认构造函数
        aType = function()
        {
            this.base.apply(this, arguments);   //调用基类构造函数
        };

    aType.prototype = aPrototype;   //设置类(构造函数)的prototype
    aType.Base = aBase;             //设置类型关系，便于追溯继承关系
    aType.prototype.Type = aType;   //为本类对象扩展一个Type属性
    return aType;   //返回构造函数作为类
};

//根类object定义：
function object(){}    //定义小写的object根类，用于实现最基础的方法等
object.prototype.isA = function(aType)   //判断对象是否属于某类型
{
    var self = this.Type;
    while(self)
    {
        if(self == aType) return true;
        self = self.Base;
    };
    return false;
};

object.prototype.base = function()  //调用基类构造函数
{
    var Base = this.Type.Base;  //获取当前对象的基类  
    if(!Base.Base)  //若基类已没有基类
        Base.apply(this, arguments)     //则直接调用基类构造函数
    else    //若基类还有基类         
    {
        this.base = MakeBase(Base);     //先覆写this.base
        Base.apply(this, arguments);    //再调用基类构造函数
        delete this.base;               //删除覆写的base属性
    };
    function MakeBase(Type) //包装基类构造函数
    {
        var Base = Type.Base;
        if(!Base.Base) return Base; //基类已无基类，就无需包装
        return function()   //包装为引用临时变量Base的闭包函数
        {
            this.base = MakeBase(Base);     //先覆写this.base
            Base.apply(this, arguments);    //再调用基类构造函数
        };
    };
};

//语法甘露的应用效果：    
var Person = Class      //默认派生自object基本类
({
    Create: function(name, age)
    {
        this.base();    //调用上层构造函数
        this.name = name;
        this.age = age;
    },
    SayHello: function()
    {
        alert("Hello, I'm " + this.name + ", " + this.age + " years old.");
    },
    toString: function()    //覆写toString方法
    {
        return this.name;
    }
});

var Employee = Class(Person,    //派生自Person类
{
    Create: function(name, age, salary)
    {
        this.base(name, age);  //调用基类的构造函数
        this.salary = salary;
    },
    ShowMeTheMoney: function()
    {
        alert(this + " $" + this.salary); //这里直接引用this将隐式调用toString()
    }
});

var BillGates = new Person("Bill Gates", 53);
var SteveJobs = new Employee("Steve Jobs", 53, 1234);
alert(BillGates);   //这里将隐式调用覆写后的toString()方法
BillGates.SayHello();
SteveJobs.SayHello();
SteveJobs.ShowMeTheMoney();

var LittleBill = new BillGates.Type("Little Bill", 6); //用BillGate的类型建LittleBill
LittleBill.SayHello();

alert(BillGates.isA(Person));       //true
alert(BillGates.isA(Employee));     //false
alert(SteveJobs.isA(Person));       //true
```

参考资料：

悟透JavaScript

http://www.cnblogs.com/leadzen/archive/2008/02/25/1073404.html

悟透JavaScript 之 甘露模型（新）

http://www.cnblogs.com/leadzen/archive/2008/06/04/1213090.html 
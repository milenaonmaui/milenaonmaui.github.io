---
layout: post
title:      "Ruby ||= (or equal) operator"
date:       2019-01-08 21:01:13 +0000
permalink:  ruby_or_equal_operator
---


The ||= operator provides very neat functionality that is useful in many cases, especially in a situation when we either need to initialize a new variable/object or if it already exists we just want to do something with it. 

For example, in my OO School Domain lab my initial code for add_sudent was as follows:

```
def add_student(name, grade)
   if roster[grade] != nil
     roster[grade] << name
  else
    roster[grade]=[]
    roster[grade]<<name
  end

```
In other words, if roster[grade] already exists, push the name of the student in it, but if it doesn't exist, first initialize it, otherwise there will be an error.
This code worked fine, however much shorter and elegant solution is using the ||= operator: 

```
def add_student(student_name, grade)
    roster[grade] ||= []
    roster[grade] << student_name
 end
```
How this works:
x ||= y  expands to  x || x = y . So, if x is truthy, i.e. not nil or false, Ruby will evaluate the whole expression as true without bothering to evaluate the second expression. Only if x is falsey will Ruby execute x=y.
 So in my case if  roster[grade] is not nil the next statement will be executed, but if it is nil then roster[grade]=[] will be executed first.
 
Once I grasped how this works, it became very useful in many situations of that kind. 


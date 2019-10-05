# NSPredicate

>[iOS中的谓词（NSPredicate）使用](http://www.cocoachina.com/ios/20160111/14926.html)

看了这遍文章，对照着写了以下Demo。一般性的使用基本够了。


```swift
//: Playground - noun: a place where people can play

import UIKit
import Foundation


//1.比较运算符
func demoComparationOperator()
{
    {
        let testNum = 123
        let predict = NSPredicate(format: "self == 123")
        predict.evaluate(with: testNum)
    }();
    
    {
        let testNum = 123
        let predict = NSPredicate(format: "self < 123")
        predict.evaluate(with: testNum)
    }();
    
    {
        let testNum = 122
        let predict = NSPredicate(format: "self != 123")
        predict.evaluate(with: testNum)
    }();
    
    
    {
        let testNum = 123
        let predict = NSPredicate(format: "self BETWEEN {100,200}")
        predict.evaluate(with: testNum)
    }();
    
    {
        let testNum = "HELLO"
        let predict = NSPredicate(format: "self == \"HELLO\"")
        predict.evaluate(with: testNum)
    }();
    
    {
        let testNum = "HELLO"
        let predict = NSPredicate(format: "self <= \"HELLO WORLD\"")
        predict.evaluate(with: testNum)
    }();
}


func demoLogic()
{
    {
        let testArray = [1,2,3,4,5,6,7]
        let predict = NSPredicate(format: "self > 2 && self <= 5")
        (testArray as NSArray).filtered(using: predict)
    }();
    
    {
        let testArray = [1,2,3,4,5,6,7]
        let predict = NSPredicate(format: "self <= 2 or self >= 5")
        (testArray as NSArray).filtered(using: predict)
    }();
    
    {
        let testArray = [1,2,3,4,5,6,7]
        let predict = NSPredicate(format: "!(self >= 2)")
        (testArray as NSArray).filtered(using: predict)
    }();
}


func demoString()
{
    {
        let testString = "hello world"
        let predict = NSPredicate(format: "self beginswith \"hello\"")
        predict.evaluate(with: testString)
    
    }();
    
    {
        let testString = "hello world"
        let predict = NSPredicate(format: "self endswith \"world\"")
        predict.evaluate(with: testString)
    }();
    
    {
        let testString = "hello world"
        let predict = NSPredicate(format: "self contains \"hello\"")
        predict.evaluate(with: testString)
    }();
    
    {
        let testString = "HELLO world"
        let predict = NSPredicate(format: "self contains[CD] \"hello\"")
        predict.evaluate(with: testString)
    }();
    
    {
        let testString = "HELLO world"
        let predict = NSPredicate(format: "self LIKE[CD] \"?ello*\"")
        predict.evaluate(with: testString)
    }();
    
    {
        let testString = "666666"
        let predict = NSPredicate(format: "self matches \"[1-9]{6}\"")
        predict.evaluate(with: testString)
    }();
    
}

func demoSetOperation()
{
    {
        let filterArray : NSArray = ["ab","abc"]
        let array : NSArray = ["a","ab","abc","abcd"]
        let predict = NSPredicate(format: "not (self in %@)", filterArray)
        array.filtered(using: predict)
    }();
    
    {
        let filterArray : NSArray = ["ab","abc"]
        let array : NSArray = ["ab","ab","abc","abc","abcd"]
        let predict = NSPredicate(format: "all self in %@", filterArray)
        predict.evaluate(with: array)
    }();
    
    {
        let filterArray : NSArray = ["ab","abc"]
        let array : NSArray = ["abdd","abdd","abcdd","abcdd","abcdd","ab"]
        let predict = NSPredicate(format: "none self in %@", filterArray)
        predict.evaluate(with: array)
    }();
    
    {
        let filterArray : NSArray = ["ab","abc"]
        let array : NSArray = ["abdd","abdd","abcdd","abcdd","abcdd","ab"]
        let predict = NSPredicate(format: "any self in %@", filterArray)
        predict.evaluate(with: array)
    }();
    
    {
        let testDic : NSArray = [["age":18],["age":19],["age":20],["age":21]]
        let predict = NSPredicate(format: "any self.age < 19 && array[SIZE] == 4")
        predict.evaluate(with: testDic)
    }();
    
    {
        let testArray : NSArray = [["age":18],["age":19],["age":20],["age":21]]
        let predict = NSPredicate(format: "self.age <= 19")
        testArray.filtered(using: predict)
    }();
}


demoComparationOperator()
demoLogic()
demoString()
demoSetOperation()

```
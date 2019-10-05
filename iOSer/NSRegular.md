# NSRegular

```swift
//: Playground - noun: a place where people can play

import UIKit
import Foundation

let beforeText = "途牛旅游网于2006年10月创立于南京，以“让旅游更简单”为使命，为消费者提供由北京、天津、上海、广州、深圳、南京等64个城市出发的旅游产品预订服务，产品全面，价格透明，全年365天24小时400电话预订，并提供丰富的后续服务和保障。\n      途牛旅游网提供8万余种旅游产品供消费者选择，涵盖跟团、自助、自驾、邮轮、酒店、签证、景区门票以及公司旅游等，已成功服务累计超过400万人次出游。2014年12月15日，途牛旅游网宣布与弘毅投资、京东商城、携程旗下子公司“携程投资”以及途牛首席执行官与首席运营官签订股权认购协议。根据协议途牛将向上述投资者出售1.48亿美元的新发行股份。[1]\n       2015年11月24日，途牛旅游网与海航旅游集团共同宣布战略结盟。海航旅游战略投资途牛5亿美元，双方将利用各自优质资源，在线上旅游、航空、酒店服务等领域开展深度合作。[2]易观报告显示，2015年第3季度，途牛交易规模达到46.5亿元人民币，同比增长141.1%。这已是途牛连续三个季度同比增速超过三位数：2015年第二季度时，途牛交易规模同比增长135.3%；第一季度，这一数据为122.8%。\n        从增速来看，途牛再度蝉联行业第一。[3]以全年业绩来看，途牛2015年的净收入为76亿元人民币(合11.802亿美元)，较2014年增长116.3%。收入增长主要来自于跟团游、自助游及其他收入的增长。2015年总出游人次为4449053，较2014年的2181834人次增长103.9%。[4]adfasdfadf"

let range = NSMakeRange(0, beforeText.characters.count)
let regexOption : NSRegularExpression.Options = .caseInsensitive
let pattern = "途牛"
let regex = try? NSRegularExpression(pattern: pattern, options: regexOption)



//替换
func replaceString()
{
    guard regex != nil else {
        return;
    }

    //匹配整个range
    var afterText = regex!.stringByReplacingMatches(in: beforeText, options: .withTransparentBounds, range: range, withTemplate: "《途牛》")
    
    //只匹配开头
    afterText = regex!.stringByReplacingMatches(in: beforeText, options: .anchored, range: range, withTemplate: "《途牛》")
    
}
replaceString()

func otherUses() {
    guard regex != nil else {
        return;
    }
    let matchNum = regex!.numberOfMatches(in: beforeText, options: .withTransparentBounds, range: range)
    print(matchNum)
    
    let firstCheckingResult = regex!.firstMatch(in: beforeText, options: .withTransparentBounds, range: range)
    print(firstCheckingResult ?? "")
    
    let firstRange = regex!.rangeOfFirstMatch(in: beforeText, options: .withTransparentBounds, range: range)
    if !NSEqualRanges(NSMakeRange(NSNotFound, 0), firstRange) {
        print(String(describing: firstRange))
    }
    
    let allMatch = regex!.matches(in: beforeText, options: .withTransparentBounds, range: range)
    for ele in allMatch {
        print(String.init(describing: ele))
        print((beforeText as NSString).substring(with: ele.range))
    }
}
otherUses()

//找没找到都会调用 ，即是完成一次匹配就好调用
func searchTextWithProgressOption()
{
    guard regex != nil else {
        return;
    }
    var count = 0
    regex!.enumerateMatches(in: beforeText, options: .reportProgress, range: range, using: { (result, matchFlag, stop) in
        
        if result?.numberOfRanges ?? 0 > 0
        {
            print(String.init(describing: result))
        }
        print(String(describing: matchFlag))
        print(count)
        count += 1
        if count == 10
        {//中断匹配
            //                stop.pointee = true
        }
    })
    
}
searchTextWithProgressOption()


//找到后才会调用
func searchTextWithCompletionOption() {
    
    guard regex != nil else {
        return;
    }
    var count = 110
    regex!.enumerateMatches(in: beforeText, options: .reportCompletion, range: range, using: { (result, matchFlag, stop) in
        
        /*
         the result 可能是nil
         */
        
        //            if result?.numberOfRanges ?? 0 > 0
        //            {
        //                print(String.init(describing: result))
        //            }
        print(String.init(describing: result))
        
        print(matchFlag)
        print(count)
        count -= 1
        if count == 0
        {
            //stop.pointee = true
        }
    })
    
}
searchTextWithCompletionOption()


//校验
func verify() {
    
    let patterns = ["^[a-z]{1,10}$",      // First name
        "^[a-z]$",            // Middle Initial
        "^[a-z']{2,10}$",     // Last Name
        "^(0[1-9]|1[012])[-/.](0[1-9]|[12][0-9]|3[01])[-/.](19|20)\\d\\d$" ]  // Date of Birth
    
    let textFields = [ "Xiong11", "D", "Wei", "01-07-1990" ]
    
    let regexes = patterns.map {
        try? NSRegularExpression(pattern: $0, options: .caseInsensitive)
    }
    
    for index in 0..<textFields.count {
        let checkResult = regexes[index]?.firstMatch(in: textFields[index], options: .withTransparentBounds, range: NSMakeRange(0, textFields[index].characters.count))
        guard checkResult != nil else {
            print("\(textFields[index]) is not valide")
            continue
        }
        if !NSEqualRanges((checkResult?.range)!, NSMakeRange(NSNotFound, 0)) {
            print("\(textFields[index]) is valide")
        }
        else
        {
            print("\(textFields[index]) is not valide")
        }
    }
}

verify()



//示例
func example() {
    let pattern = "\\{\\{([a-z1-9\\s]+):([a-z1-9\\s]+)\\}\\}"
    let textField = ["{{context:function}}","{{context1: function}}","{{ context2 : function }}"]
    let regex = try? NSRegularExpression.init(pattern: pattern, options: .caseInsensitive)
    guard regex != nil else {
        print("regex is nil")
        return
    }
    for str in textField {
        let result = regex?.matches(in: str, options: .withTransparentBounds, range: NSMakeRange(0, str.characters.count))
        guard result != nil else {
            continue
        }
        for ele in result! {
            print(ele.numberOfRanges)
            for index in 0..<ele.numberOfRanges {
                print((str as NSString).substring(with: ele.rangeAt(index)))
            }
        }
    }
}

example()


```
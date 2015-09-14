title: 基于jQueryRotate的前端抽奖转盘实现
date: 2015-05-27 18:16:00
tags:
- jquery rotate
- 抽奖程序
categories:
- WEB前端

---

抽奖是产品运营中常见的活动方式，转盘是最常见也是体验较好的一种抽奖方式，那么问题来了，如何快速在页面上实现一个抽奖转盘，并结合服务器端计算的抽奖概率，实现一个抽奖程序？


<!-- more -->
### 思路 ###
比较简单的一种方案是：

1.UI根据本次参与抽奖的奖品，设计一个转盘素材和指针素材，前端用CSS显示在页面上。前端维护一个字典记录每类奖品在转盘上的角度。
2.点击开始抽奖后，向服务器发异步请求计算出当前用户的中奖结果（可以根据其用户属性，如VIP等级，活跃程度等等计算是否中奖，中什么奖），将中奖结果存入db，并返回给前端。
3.前端拿到结果之后，获取该奖品在转盘上的角度，然后让转盘转起来（可以先转个若干圈），最后停留在所对应的角度上。


### 技术点 ###
所以实现所需的技术点可以归为两点：

1.前端需要实现转盘图片或者指针图片的旋转，并控制它最终停留在某个角度。

这里可以使用jQueryRotate这个插件来实现。

jQueryRotate插件提供了rotate和stopRotate接口：

- rotate(config)
- config是一个json对象，可配置的参数有：
- angle: 起始的角度
- duration: 旋转持续时间
- animateTo: 最终在哪个角度上停止，默认顺时针旋转，如果角度为负数表示逆时针旋转
- callback: 停止后的回调函数

旋转时从0度开始代表会从6点钟位置顺时针旋转，如下图。所以设置各个奖品的角度的时候要注意是相对6点钟位置的度数。

![](http://7x00n0.com1.z0.glb.clouddn.com/rotate.jpg)



2.服务端需要根据用户属性计算抽奖结果，具体情况采用具体策略，这里就不累述。



### 实现代码 ###
方案有了，接下来就可以撸代码了！
首先看下要实现的效果图，偷一下梦幻手游的抽奖图，物品纯看图瞎猜，见怪莫怪。

![](http://7x00n0.com1.z0.glb.clouddn.com/lottery.png)

HTML和CSS：

    <!doctype html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>转盘</title>
    <style>
    * {
    padding: 0;
    margin: 0
    }
    
    body {
    text-align: center
    }
    
    .plate-container {
    position: relative;
    width: 509px;
    height: 509px;
    margin: 50px auto;
    }
    
    .lottery-plate {
    width: 500px;
    height: 500px;
    background: url(images/plate-bg.png);
    position: absolute;
    top: 0;
    left: 0
    }
    
    .plate-container div.lottery-btn-container {
    width: 500px;
    height: 500px;
    position: absolute;
    background: url(images/plate-pointer.png);
    cursor: pointer;
    }
    
    
    </style>
    </head>
    <body>
    <div >
    <div id="lotteryPlate" ></div>
    <div id="lotteryBtn" ></div>
    </div>
    </body>
    <script src="jquery-1.7.2.min.js"></script>
    <script src="jQueryRotate.2.2.js"></script>
    
    <!-- 抽奖时指针转动 -->
    <script src="rotate_btn.js"></script>
    <!-- 抽奖时转盘转动 -->
    <!--<script src="rotate_plate.js"></script>-->
    </html>

这里我做了两种抽奖显示形式，一种是抽奖时指针在转轮盘不转（rotate_btn.js），一种是指针不转轮盘转（rotate_plate.js）。这里贴上rotate_btn.js的代码，rotate_plate.js其实类似，只是转动的目标以及角度需要调整。

	$(function () {
    // 在异步请求期间旋转转盘，持续10s，转5圈
    // 如果这段时间内请求成功返回，会取消旋转，如果超时，回调会执行，提示网络超时
    var fRotateDuringReq = function () {
        $("#lotteryBtn").rotate({
            angle: 0,
            duration: 10000,
            animateTo: 1800,
            callback: function () {
                alert('网络似乎不给力哦')
            }
        });
    }

    /**
     * 开始抽奖
     * @param angle 抽奖结果图片所对应的角度
     * @param text 抽奖结果提示文案
     */
    var fStartLottery = function (angle, text) {
        $('#lotteryBtn').stopRotate();  // 停止之前的旋转
        $("#lotteryBtn").rotate({
            angle: 0,
            duration: 5000,
            animateTo: angle + 1440, // 1440表示4圈，转4圈之后转到抽奖结果的角度
            callback: function () {
                alert(text);    // 提示文案
            }
        });
    };

    // 奖品池字典，仙丹是阳光普照奖
    var awards = {
        "0": {name: "仙丹", angle: [0, 51, 103, 180]}, "1": {name: "鹅卵石", angle: 26}, "2": {
            name: "经书",
            angle: 77
        }, "3": {name: "仙草", angle: 129}, "4": {name: "令牌", angle: 154}, "5": {name: "黄钻", angle: 206}, "6": {
            name: "暗器",
            angle: 231
        }, "7": {name: "珍珠", angle: 283}, "8": {name: "鱼雷", angle: 334}
    };

    /**
     * 抽奖按钮点击事件
     */
    $("#lotteryBtn").click(function () {
        fRotateDuringReq(); // 请求期间的旋转
        var awardIds = ['0', '1', '2', '3', '4', '5', '6', '7', '8'];
        // 随机抽取一个奖品，模拟服务端计算出的用户抽奖结果
        var awardKey = awardIds[Math.floor(Math.random()*(awardIds.length))];
        var award = awards[awardKey];
        console.log("Server return award: " + award.name);

        var angle = award.angle;
        if (typeof angle != "number") {
            // 角度是数组，说明转盘上有多个该奖品，随机取一个
            angle = angle[Math.floor(Math.random() * (angle.length))];
        }
        fStartLottery(angle, "恭喜您抽中" + award.name);
    });

	});


demo页面：[http://www.charlestech.info/examples/jqueryrotate/lottery.html](http://www.charlestech.info/examples/jqueryrotate/lottery.html)

代码Github：[https://github.com/charleschaochen/LotteryProgram](https://github.com/charleschaochen/LotteryProgram)
# h5-locttery
h5转盘抽奖 vue项目

#  运行方式

```
//下载项目

git clone https://github.com/best-fan/h5-locttery.git

//安装依赖

npm install


npm run  serve
```


##  组件说明

-  lottery-nomer 

方案一:通过自带属性实现过渡效果(transition-timing-function)

-  lottery

方案二:通过函数实现转盘的旋转。

###  组件使用方法：
1.  引用组件

```
//html
      <lottery 
        ref='draw'
        v-if='drawData.awardList.length != 0'
        :haveScore='drawData.score'
        :haveTime='drawData.drawTimes'
        :drawList='drawData.awardList'
        :drawScore='drawData.costScore'
        @draw='doDraw'
        @lotteryResult='lottery'
      ></lottery>
//js
import lottery from './components/lottery.vue';
  components: {
  lottery,
  
  },

```
2.设置属性值：

```
haveScore： //剩余积分 Number类型
haveTime： //剩余抽奖次数 Number类型
drawScore： //单次抽奖花费的积分数 Number类型
drawList： //奖品列表  Array类型  支持5~9份的设置
drawList结构如下：
 [
              {
                acitvityAwardId: '1',//奖品ID
                imagePath:
                  '/group1/UIMG/20201106/00495747-8bd2-4f4a-936d-ec12a39d32c6.jpg',//奖品图片
                awardName: '纸巾',//奖品名称
              },
              ...
 ]
 haveScore、haveTime、drawScore 三个属性值 将会判断 积分数、剩余次数 是否满足抽奖条件
 
 @draw  点击抽奖回调 用来请求接口 获取中奖信息
 @lotteryResult  转盘旋转结束回调 用来弹窗提示用户 进行抽奖完成后的后续操作

```

在线演示地址：
[**大转盘抽奖**](http://blog.bravetimes.cn/project/draw/index.html) 
[**技术文档地址**](http://blog.bravetimes.cn/Detail?id=27&navId=1)






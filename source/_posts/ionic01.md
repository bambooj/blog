---
title: 在mac上开发ionic项目
date: 2017-07-14 13:29:25
tags:
---

#### 1. 环境准备
npm install -g cordova ionic
配置android sdk和java环境变量
```js
~$cat ./.bash_profile
JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.7.0_80.jdk/Contents/Home
ANDROID_HOME=/Users/wukangjie/Library/Android/sdk
M2_HOME=/Users/wukangjie/Documents/apache-maven-3.5.0
PATH=$JAVA_HOME/bin:$PATH:.:$M2_HOME/bin:$ANDROID_HOME/platform-tools:$ANDROID_HOME/tools
CLASSPATH=$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib/dt.jar:.
export JAVA_HOME
export ANDROID_HOME
export PATH
export CLASSPAT
export M2_HOME
```
#### 2. 创建ionic应用
ionic start myApp tabs
#### 3. 启动应用
cd myApp
ionic serve

<!--more-->
#### 4. 添加ionic的ios运行平台

city_operation_app$ionic platform add ios
The platform command has been renamed. To find out more, run:

  ionic cordova platform --help

city_operation_app$ionic cordova platform --help
解决办法：实用命令ionic cordova platfrom add ios

如果是添加android平台
运行
>ionic cordova platform add

这时会有提示，选择ios或者android

或者直接运行

>ionic cordova platform add andriod

跑完上面的命令，会在项目下面自动新增一个platform目录，下面有对于端ios目录和android目录。


#### 5. 构建项目
ionic cordova build ios

#### 6. 运行项目
cordova emulate ios --target="iPhone-6"


安装highcharts
city_operation_app$typings install dt~highcharts --save --global
highcharts

#### 7. 集成highcharts
 7.1 安装highcharts
 >npm install highcharts --save
 
 7.2 安装ng-highcharts
 >npm install ng-higcharts --save
 
 7.3 在ts文件中引入
>import * as Highcharts from 'highcharts';
 
 7.4使用
```js
@ViewChild('chart') public chart: ElementRef;
ionViewDidEnter() {
    Highcharts.chart('tourism1', {
      chart: {
        type: 'bar'
      },
      title: {
        text: 'Fruit Consumption'
      },
      xAxis: {
        categories: ['Apples', 'Bananas', 'Oranges']
      },
      yAxis: {
        title: {
          text: 'Fruit eaten'
        }
      },
      series: [{
        name: 'Jane',
        data: [1, 0, 4]
      }, {
        name: 'John',
        data: [5, 7, 3]
      }]
    });
}
```

#### 8 集成chart
8.1 安装组件
>npm install --save  chart.js

8.2 在ts文件中引入
>import * as ChartJs from 'chart.js';

3. 在ts文件中实用
```
getlivelihoodRate() {
    var canvas = <HTMLCanvasElement> document.getElementById("myChart");
    var ctx = canvas.getContext("2d");  // 这里是关键, 不能写在constructor()中
    new ChartJs(ctx, {
      type: 'bar',
      data: {
        labels: ["Red", "Blue", "Yellow", "Green", "Purple", "Orange"],
        datasets: [{
          label: '# of Votes',
          data: [12, 19, 3, 5, 2, 3],
          backgroundColor: [
            'rgba(255, 99, 132, 0.2)',
            'rgba(54, 162, 235, 0.2)',
            'rgba(255, 206, 86, 0.2)',
            'rgba(75, 192, 192, 0.2)',
            'rgba(153, 102, 255, 0.2)',
            'rgba(255, 159, 64, 0.2)'
          ],
          borderColor: [
            'rgba(255,99,132,1)',
            'rgba(54, 162, 235, 1)',
            'rgba(255, 206, 86, 1)',
            'rgba(75, 192, 192, 1)',
            'rgba(153, 102, 255, 1)',
            'rgba(255, 159, 64, 1)'
          ],
          borderWidth: 1
        }]
      },
      options: {
        scales: {
          yAxes: [{
            ticks: {
              beginAtZero: true
            }
          }]
        }
      }
    })
}
```
8.4 html文件中编写图表元素
```
<canvas id="myChart" width="60%" height="60%"></canvas>
```

#### 9 添加导航，并返回
点击按钮，打开一个新页面，新页面带有“返回”按钮。最直接的方式是参考官方文档。
9.1 写好跳转后的页面，入详情页面
9.2 引入detail页面
>import { PopulationDetailPage } from './sub/population_detail';
9.3 在home.ts中写点击事件处理方法
```
openNavDetailsPage(item) {
    this.nav.push(PopulationDetailPage, { item:item });
}
```
9.4 在appModule中声明
```
import { PopulationDetailPage} from '../pages/home/sub/population_detail';
@NgModule({
  declarations: [
    MyApp,
    ...
    PopulationDetailPage
  ],
    entryComponents: [
    MyApp,
    ...
    PopulationDetailPage
  ],
})
```
9.5 修改返回按钮的文字：从BACK变为中文的“返回”

也是在appModule中修改
```
 imports: [
    BrowserModule,
    HttpModule,
    JsonpModule,
    IonicModule.forRoot(MyApp, {
      backButtonText: '返回',
    })
  ],
```

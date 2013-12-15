#监控数据的展示

想像中是这样的：

1. 我们自己写或者改写监控插件，通过简单的方式（http请求）发给数据收集服务器
2. 数据收集服务器将数据存放到我们想要的数据库（mongoDB）
3. 数据库里面的监控数据会做一些处理和统计，暴露给其他客户端使用
3. 在web端通过javascript绘制图标展示

1找到了个小例子，用Python写nagios插件： [How To Create Nagios Plugins With Python On Ubuntu 12.10][0]。
就这个例子来说，将他收集到的数据通过http请求发送到一个web服务器应该是可以做到的。
不过还不太了解这些插件被调用的频率怎么设置。


2、3都是容易做到的。


4可以通过款专门绘制图表的javascript库做到，[highcharts][1]。  
这里是一个官方 [demo][2]

```javascript
$(function () {
        $('#container').highcharts({
            title: {
                text: 'Monthly Average Temperature',
                x: -20 //center
            },
            subtitle: {
                text: 'Source: WorldClimate.com',
                x: -20
            },
            xAxis: {
                categories: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun',
                    'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
            },
            yAxis: {
                title: {
                    text: 'Temperature (°C)'
                },
                plotLines: [{
                    value: 0,
                    width: 1,
                    color: '#8y08080'
                }]
            },
            tooltip: {
                valueSuffix: '°C'
            },
            legend: {
                layout: 'vertical',
                align: 'right',
                verticalAlign: 'middle',
                borderWidth: 0
            },
            series: [{
                name: 'Tokyo',
                data: [7.0, 6.9, 9.5, 14.5, 18.2, 21.5, 25.2, 26.5, 23.3, 18.3, 13.9, 9.6]
            }, {
                name: 'New York',
                data: [-0.2, 0.8, 5.7, 11.3, 17.0, 22.0, 24.8, 24.1, 20.1, 14.1, 8.6, 2.5]
            }, {
                name: 'Berlin',
                data: [-0.9, 0.6, 3.5, 8.4, 13.5, 17.0, 18.6, 17.9, 14.3, 9.0, 3.9, 1.0]
            }, {
                name: 'London',
                data: [3.9, 4.2, 5.7, 8.5, 11.9, 15.2, 17.0, 16.6, 14.2, 10.3, 6.6, 4.8]
            }]
        });
    });
    
```


[0]: https://www.digitalocean.com/community/articles/how-to-create-nagios-plugins-with-python-on-ubuntu-12-10
[1]: http://www.highcharts.com/
[2]: http://jsfiddle.net/gh/get/jquery/1.9.1/highslide-software/highcharts.com/tree/master/samples/highcharts/demo/line-basic/

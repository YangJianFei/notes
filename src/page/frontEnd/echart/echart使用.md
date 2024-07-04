## echart社区
https://www.makeapie.cn/echarts
https://www.isqqw.com/

http://ppchart.com/#/ 
http://analysis.datains.cn/finance-admin/index.html#/chartLib/all
https://www.highcharts.com.cn/demo/highcharts
https://madeapie.com/#/
http://chart.majh.top/
https://antv-2018.alipay.com/zh-cn/g2/3.x/demo/index.html
http://192.144.199.210/forum.php?mod=forumdisplay&fid=2

## echart在线配置
http://echarts-set.com/

## echart事件示例
```
barChart.on('mouseover', function (params) {
    if (params.componentType === 'yAxis') {
        const offsetX = params.event.offsetX + 10
        const offsetY = params.event.offsetY + 10
        barChart.setOption({
            tooltip: {
                formatter: function () {
                    return params.value;
                },
                alwaysShowContent: true,
            }
        })
        barChart.dispatchAction({
            type: 'showTip',
            seriesIndex: 0,
            dataIndex: 0,
            position: [offsetX, offsetY],
        })
    }
});
barChart.on('mouseout', function (params) {
    if (params.componentType === 'yAxis') {
        barChart.setOption({
            tooltip: {
                formatter: function () {
                    return '';
                },
                alwaysShowContent: false,
            }
        })
    }
});
```
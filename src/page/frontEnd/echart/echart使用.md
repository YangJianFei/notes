## echart社区
https://www.makeapie.cn/echarts

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
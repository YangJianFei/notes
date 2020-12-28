## 懒加载
```
var Flow = function () { };
Flow.prototype.lazyimg = function (options) {
    var that = this, index = 0, haveScroll;
    options = options || {};

    var scrollElem = $(options.scrollElem || document); //滚动条所在元素
    var elem = options.elem || 'img';

    //滚动条所在元素是否为document
    var notDocment = options.scrollElem && options.scrollElem !== document;

    //显示图片
    var show = function (item, height) {
        var start = scrollElem.scrollTop(), end = start + height;
        var elemTop = notDocment ? function () {
            return item.offset().top - scrollElem.offset().top + start;
        }() : item.offset().top;

        /* 始终只加载在当前屏范围内的图片 */
        if (elemTop >= start && elemTop <= end) {
            if (!item.attr('src')) {
                var src = item.attr('lay-src');
                // layui.img(src, function () {
                var next = that.lazyimg.elem.eq(index);
                item.attr('src', src).removeAttr('lay-src');

                /* 当前图片加载就绪后，检测下一个图片是否在当前屏 */
                next[0] && render(next);
                index++;
                // });
            }
        }
    }, render = function (othis, scroll) {

        //计算滚动所在容器的可视高度
        var height = notDocment ? (scroll || scrollElem).height() : $(window).height();
        var start = scrollElem.scrollTop(), end = start + height;

        that.lazyimg.elem = $(elem);

        if (othis) {
            show(othis, height);
        } else {
            //计算未加载过的图片
            for (var i = 0; i < that.lazyimg.elem.length; i++) {
                var item = that.lazyimg.elem.eq(i), elemTop = notDocment ? function () {
                    return item.offset().top - scrollElem.offset().top + start;
                }() : item.offset().top;

                show(item, height);
                index = i;

                //如果图片的top坐标，超出了当前屏，则终止后续图片的遍历
                if (elemTop > end) break;
            }
        }
    };

    render();

    if (!haveScroll) {
        var timer;
        scrollElem.on('scroll', function () {
            var othis = $(this);
            if (timer) clearTimeout(timer)
            timer = setTimeout(function () {
                render(null, othis);
            }, 50);
        });
        haveScroll = true;
    }
    return render;
};

// 使用  var flow = new Flow();flow.lazyimg();
```
## demo1 图片懒加载
##### 预备知识详情请参考我的博客
https://www.cnblogs.com/xmjs/p/13415904.html
+ 原始版本
```js
        // 绑定滚动事件
        window.addEventListener('scroll', function() {
            lazyLoading()
        });

        function lazyLoading() {
            let imgs = document.getElementsByTagName('img');
            // 滚动过的高度
            let bodyScrollHeight = document.body.scrollTop || document.documentElement.scrollTop;
            // 窗口的高度
            let windowHeight = window.innerHeight;
            for (let i = 0; i < imgs.length; i++) {
                // 图片相对于顶部的位置
                let imgHeight = imgs[i].offsetTop
                //判断是否到达加载区域
                if (imgHeight < bodyScrollHeight + windowHeight && imgHeight >= bodyScrollHeight) {
                    setTimeout(function() {
                        imgs[i].setAttribute('src', imgs[i].getAttribute('data-src'))
                    }, 1000)
                }
            }
        }
        // 第一次执行
        lazyLoading()
```
+ 优化：
1. 判断是否已经加载过
2. 用array的api代替for循环
   遇到了问题，imgs并不能用forEach函数遍历，是一个HTMLCollection，需要将其转换成数组
```js
        // 绑定滚动事件
        window.addEventListener('scroll', function() {
                lazyLoading()
        });

        function lazyLoading() {
            
            let imgs = document.getElementsByTagName('img');
            // 滚动过的高度
            let bodyScrollHeight = document.body.scrollTop || document.documentElement.scrollTop;
            // 窗口的高度
            let windowHeight = window.innerHeight;
            //转换成数组
            let newimgs = Array.prototype.slice.apply(imgs)
            newimgs.forEach(img => {
                // 图片相对于顶部的位置
                let imgHeight = img.offsetTop
                    //判断是否到达加载区域
                if (imgHeight < bodyScrollHeight + windowHeight && imgHeight >= bodyScrollHeight) {
                    //判断是否已经加载过
                    if (img.getAttribute('src') === img.getAttribute('data-src')) return;
                    //这个定时器是模拟网站加载速度
                    setTimeout(function() {
                        img.setAttribute('src', img.getAttribute('data-src'))
                    }, 1000)
                }
            });
        }
        // 第一次执行
        lazyLoading()
```
3. 性能优化：
+ window.scroll每次滚动都会触发
+ 解决方法：当页面停止滚动的时候，再去执行页面中的方法（节流）
```js
        // 绑定滚动事件
        let timer;
        window.addEventListener('scroll', function() {
            console.log("scroll")
                //节流 当停止滚动再执行 每一次执行都先把上一次的关闭
            if (timer) {
                clearTimeout(timer)
            }
            timer = setTimeout(() => {
                console.log("lazyloading")
                lazyLoading()
            }, 300)
        });

        function lazyLoading() {
            let imgs = document.getElementsByTagName('img');
            // 滚动过的高度
            let bodyScrollHeight = document.body.scrollTop || document.documentElement.scrollTop;
            // 窗口的高度
            let windowHeight = window.innerHeight;
            //转换成数组
            let newimgs = Array.prototype.slice.apply(imgs)
            newimgs.forEach(img => {
                // 图片相对于顶部的位置
                let imgHeight = img.offsetTop
                    //判断是否到达加载区域
                if (imgHeight < bodyScrollHeight + windowHeight && imgHeight >= bodyScrollHeight) {
                    // 判断是否已经加载过
                    if (img.getAttribute('src') === img.getAttribute('data-src')) return;
                    setTimeout(function() {
                        img.setAttribute('src', img.getAttribute('data-src'))
                    }, 1000)
                }
            });
        }
        // 第一次执行
        lazyLoading()
```

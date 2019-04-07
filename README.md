# 基于jquery插件的图片有预跟无序加载
    核心代码
```angularjs
/**
 * 图片预加载
 */
(function ($) {
    function Preload(imgs,options) {
        this.imgs = (typeof imgs === "string")?[imgs]:imgs;
        this.opts = $.extend({},Preload.DEFAULTS,options);
        // this._unoredered();
        if(this.opts.order === 'ordered'){
            this._ordered();
        }else {
            this._unoredered();
        }
    }
    Preload.DEFAULTS = {
        /**
         * 默认情况下无序的预加载
         */
        order:"unoredered",
        /**
         * 每张图片加载完毕后执行
         */
        each:null,
        /**
         * 所有图片加载完毕后执行
         */
        all:null,
    };
    Preload.prototype._ordered = function(){
        /**
         * 有序加载
         */
        var opts = this.opts,
            imgs = this.imgs,
            len = imgs.length,
            count = 0;
        load();
        function load() {
            var imgObj = new Image();
            $(imgObj).on("load error",function () {
                opts.each && opts.each(count);
                if(count >= len){
                    /**
                     * 所有图片已经加载完毕
                     */
                    opts.all && opts.all();

                }else {
                    load();
                }
                count++;
            });
            imgObj.src = imgs[count];
        }
    };
    Preload.prototype._unoredered = function () {
        /**
         * 无序加载
         */
        var imgs = this.imgs,
            opts = this.opts,
            count = 0,
            len = imgs.length;
        $.each(imgs,function (i,src) {
            if(typeof src != "string") return;
            var imgObj = new Image();
            $(imgObj).on("load error",function () {
                opts.each && opts.each(count);
                if(count >= len -1){
                    opts.all && opts.all();
                }
                count ++;
            });
            imgObj.src = src;
        });
    };
    $.extend({
        preload:function (imgs,opts) {
            new Preload(imgs,opts)
        }
    })
})(jQuery);
```


# View的绘制 #
* 理论  
View的绘制部分主要是对Paint和Canvas两个类的api的熟悉,主要流程就是配置一个paint，然后调用Canvas.drawXXX进行绘制，这个不用多说。但是这里有几个注意的点:  
1. Path.addCircle(float x, float y, float radius, @NonNull [Direction]() dir)
2. Path.setFillType(FillType fillType)  
通过这个可以配置相交时的剪切图形具体效果可以参考[hencoder的公开课](https://rengwuxian.com/ui-1-1/)

* 应用
1. 实现圆形头像的几种方法  
    1. 使用imageview显示处理成圆形头像的bitmap  
    2. 通过自定义View使用Canvas.clipPath来显示圆形图片具体代码如下:
        ```
         //使用clipPath的方法显示圆形头像
        canvas.save()
        circlePath.addCircle(
            width / 2f,
            picTop + image.height / 2f,
            image.height / 2f,
            Path.Direction.CW
        )
        canvas.drawPath(circlePath, paint)
        canvas.clipPath(circlePath)
        canvas.drawBitmap(
            image,
            picLeft,
            picTop,
            paint
        )
        canvas.restore()
        ```
    3. 使用Paint的xfmode实现圆形图片的显示,PorterDuffXfermode的几种模式详见[PorterDuff.Mode](https://developer.android.com/reference/android/graphics/PorterDuff.Mode)
        ```
        //private val xfermode = PorterDuffXfermode(PorterDuff.Mode.SRC_IN)
        //srcRectF = RectF(0f,0f,width.toFloat(),height.toFloat())
         val save = canvas.saveLayer(srcRectF,null)
        canvas.drawCircle(picLeft+image.width/2f,picTop+image.height/2f,image.height/2f,paint)
        paint.xfermode = xfermode
        canvas.drawBitmap(image, picLeft, picTop, paint)
        paint.setXfermode(null)
        canvas.restoreToCount(save)
        ```



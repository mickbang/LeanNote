# Android动画 #
Android动画主要分为帧动画和属性动画
## 帧动画 ##
帧动画就是靠播放一帧一帧的图片而出现的动画
## 属性动画 ##
### 属性动画三种使用方式 ###
#### 1. 使用xml的方式 #### 
* 定义xml动画
```
<?xml version="1.0" encoding="utf-8"?>  
<set xmlns:android="http://schemas.android.com/apk/res/android"  
    android:fillAfter="true" >  
   
    <alpha  
        android:duration="1000"  
        android:fromAlpha="0.0"  
        android:interpolator="@android:anim/accelerate_interpolator"  
        android:toAlpha="1.0" />  
</set>
————————————————
```
* 加载xml动画
```
val animFadeIn = AnimationUtils.loadAnimation(getApplicationContext(),  
                R.anim.fade_in)
```
* 设置到View上
```
View.startAnimation(animFadeIn)
```
****
#### 2. 使用View.animate().xxx的方式 #### 
```
 view.alpha = 0f
        view.scaleX = 0f
        view.scaleY = 0f
        view.animate()
            .translationX(100.dp.toFloat())
            .translationY(200.dp.toFloat())
            .rotation(360f)
            .alpha(1f)
            .scaleX(1.0f)
            .scaleY(1.0f)
            .setInterpolator(LinearInterpolator())
            .setDuration(1*1000L)
            .start()
```
#### 3. 使用ObjectAnimator.ofxxxx的方式 #### 
```
val translationXAn = ObjectAnimator.ofFloat(view, "translationX", 100.dp.toFloat())
```

* 属性动画合并
```
  val translationXAn = ObjectAnimator.ofFloat(view, "translationX", 100.dp.toFloat())

        val translationYAn = ObjectAnimator.ofFloat(view, "translationY", 200.dp.toFloat())

        val animatorset = AnimatorSet()
        animatorset.interpolator = LinearInterpolator()
        animatorset.duration = 1 * 1000L
        animatorset.playSequentially(translationXAn, translationYAn)
        animatorset.start()
``` 
* 关键帧
```
 val propertyValuesHolderXAn =
            PropertyValuesHolder.ofFloat("translationX", 0f, 100.dp.toFloat())
        val propertyValuesHolderYAn =
            PropertyValuesHolder.ofFloat("translationY", 0f, 200.dp.toFloat())

        val animator =
            ObjectAnimator.ofPropertyValuesHolder(view,propertyValuesHolderXAn, propertyValuesHolderYAn)
        animator.duration = 1 * 1000L
        animator.start()
```
* 插值器
```
Android中提供的Interpolator（时间插值器）
AccelerateInterpolator　　　　　 加速，开始时慢中间加速
DecelerateInterpolator　　　 　　 减速，开始时快然后减速
AccelerateDecelerateInterolator　 先加速后减速，开始结束时慢，中间加速
AnticipateInterpolator　　　　　　 反向 ，先向相反方向改变一段再加速播放
AnticipateOvershootInterpolator　 反向加超越，先向相反方向改变，再加速播放，会超出目的值然后缓 慢移动至目的值
BounceInterpolator　　　　　　　 跳跃，快到目的值时值会跳跃，如目的值100，后面的值可能依次为85，77 ，70，80，90，100
CycleIinterpolator　　　　　　　　 循环，动画循环一定次数，值的改变为一正弦函数：Math.sin(2 * mCycles * Math.PI * input)
LinearInterpolator　　　　　　　　 线性，线性均匀改变
OvershottInterpolator　　　　　　 超越，最后超出目的值然后缓慢改变到目的值
```
## 应用 ##
1. 自定义动画View
```
val foods = arrayOf(
    "甘蔗",
    "苹果",
    "香蕉",
    "橙子",
    "西瓜",
    "葡萄",
    "桃子",
    "李子",
    "草莓",
    "芒果",
    "猕猴桃"
)

class AnimatorView(context: Context?, attrs: AttributeSet?) : View(context, attrs) {
    val paint = Paint(Paint.ANTI_ALIAS_FLAG).apply {
        color = Color.BLUE
        textSize = 20.sp.toFloat()
    }
    var text: String = "甘蔗"
        set(value) {
            field = value
            invalidate()
        }
    
    override fun onDraw(canvas: Canvas) {
        super.onDraw(canvas)
        canvas.drawText(text, 0f, 20.sp.toFloat(), paint)
    }


    class TextEvaluator : TypeEvaluator<String> {
        override fun evaluate(fraction: Float, startValue: String?, endValue: String?): String {
            val startIndex = foods.indexOf(startValue)
            val endIndex = foods.indexOf(endValue)
            return foods[(startIndex + (endIndex - startIndex) * fraction).toInt()]
        }
    }
```
2. 使用
```
 ObjectAnimator.ofObject(view,"text", AnimatorView.TextEvaluator(),"甘蔗","猕猴桃").apply {
            duration = 5*1000L
            repeatMode = ValueAnimator.RESTART
            repeatCount = ValueAnimator.INFINITE
            interpolator = AccelerateInterpolator()
            start()
 }
```
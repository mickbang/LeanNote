# 触摸反馈 #
## 事件分发 ##
事件分发分为View和ViewGroup，主要是通过dispatchTouchEvent来实现的。  
以下是事件分发的模型
### View ###
```
    //事件分发
    override fun dispatchTouchEvent(event: MotionEvent?): Boolean {
        return onTouchEvent()
    }

    //事件消费
    override fun onTouchEvent(event: MotionEvent?): Boolean {
        return super.onTouchEvent(event)
    }

```
### ViewGroup ###
```
    //事件分发
    override fun dispatchTouchEvent(ev: MotionEvent?): Boolean {
        if (disallowIntercept||!onInterceptTouchEvent()) {
           // 循环调用子类的dispatchTouchEvent
        if (child.dispatchTouchEvent){
            return true
        }
        }
        return onTouchEvent()
    }

    //事件拦截
    override fun onInterceptTouchEvent(ev: MotionEvent?): Boolean {
        return super.onInterceptTouchEvent(ev)
    }

    //事件消费
    override fun onTouchEvent(event: MotionEvent?): Boolean {
        return super.onTouchEvent(event)
    }
```

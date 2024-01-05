# View的测量 #
## View的测量主要分为两部分 ##

### 1. View的测量 ###
View的测量过程很简单，总的来说分为三步：
1. 计算View的需要的尺寸
2. 将View的计算尺寸与ViewGroup的期望作比较得出一个新的尺寸
3. 调用setMeasuredDimension(int measuredWidth, int measuredHeight)将结果告诉父布局


以下是一个简单的处理过程(第二步其实可以不自己写直接使用View.resolveSize(int size, int measureSpec))

```
  override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
        val widthSize = 50.dp  //模拟宽度，这里写死，也可以根据需求动态计算
        val heightSize = 100.dp//模拟高度，这里写死，也可以根据需求动态计算

//        resolve() 方法可以不自己写，直接只用View.resolveSize()
        setMeasuredDimension(
            resolve(widthMeasureSpec,widthSize),
            resolve(heightMeasureSpec,heightSize)
        )
    }

    private fun resolve(measureSpec: Int, size: Int): Int {
        val specMode = MeasureSpec.getMode(measureSpec)
        val specSize = MeasureSpec.getSize(measureSpec)
        val result = when (specMode) {
            MeasureSpec.AT_MOST -> {
                if (size < specSize) size else specSize
            }
            MeasureSpec.EXACTLY -> {
                size
            }
            MeasureSpec.UNSPECIFIED -> {
                size
            }
            else -> {
                size
            }
        }
        return result
    }
```

### 2. ViewGroup的测量 ###
对于ViewGroup的测量就比较复杂了(难点是根据业务对子控件进行的测量)，需要根据具体业务作具体的处理。 


```
override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec)
        val childCountCus = childCount
        for (i in 0 until childCountCus) {
            val child = getChildAt(i)
            val childWidthSpec = getChildMeasureCus(child.layoutParams.width,widthMeasureSpec)
            val childHeightSpec = getChildMeasureCus(child.layoutParams.height,heightMeasureSpec)
            child.measure(childWidthSpec,childHeightSpec)
        }
        //子控件测量完了就可以计算出本身的size，后面就跟view的测量过程一样了
        setMeasuredDimension(resolveSize(计算的宽度,widthMeasureSpec), resolveSize(计算的高度,heightMeasureSpec))
    }

    private fun getChildMeasureCus(size:Int, parentSpec: Int):Int {
        val parentSpecMode = MeasureSpec.getMode(parentSpec)
        val parentSpecSize = MeasureSpec.getSize(parentSpec)
        val afterMode:Int
        val afterSize:Int
        when (size) {
            LayoutParams.MATCH_PARENT -> {
                when (parentSpecMode) {
                    MeasureSpec.AT_MOST -> {
                        afterSize = size
                        afterMode = MeasureSpec.AT_MOST
                    }
                    MeasureSpec.EXACTLY -> {
                        afterSize = parentSpecSize
                        afterMode = MeasureSpec.EXACTLY
                    }
                    MeasureSpec.UNSPECIFIED -> {
                        afterSize = size
                        afterMode = MeasureSpec.UNSPECIFIED
                    }
                    else -> {
                        afterSize = size
                        afterMode = MeasureSpec.UNSPECIFIED
                    }
                }
            }

            LayoutParams.WRAP_CONTENT -> {
                when (parentSpecMode) {
                    MeasureSpec.AT_MOST -> {
                        afterSize = size
                        afterMode = MeasureSpec.AT_MOST
                    }
                    MeasureSpec.EXACTLY -> {
                        afterSize = size
                        afterMode = MeasureSpec.AT_MOST
                    }
                    MeasureSpec.UNSPECIFIED -> {
                        afterSize = size
                        afterMode = MeasureSpec.UNSPECIFIED
                    }
                    else -> {
                        afterSize = size
                        afterMode = MeasureSpec.UNSPECIFIED
                    }
                }
            }

            else -> {
                afterSize = size
                afterMode = MeasureSpec.EXACTLY
            }
        }
        return MeasureSpec.makeMeasureSpec(afterSize,afterMode)
    }
```
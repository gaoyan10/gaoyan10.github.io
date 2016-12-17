---
---
cardView的
elevation


toolbar的
app:contentInsetStart="0dp"
app:contentInsetLeft="0dp"


自定义View在RecyclerView中，调用了postInvalidate，5.0以下不生效，不触发onDraw，改为invalidate后生效

RecyclerView在NestedScrollView里面滑动会卡顿
videoList.setNestedScrollingEnabled(false);，就可以恢复流畅，但据说会失去复用效果

http://blog.csdn.net/wangfayinn/article/details/9319161
android手机的Picture size和 Preview size要保证是SupportedPreviewSize，而且小米4手机如果选择了最大的size，会卡死，不会调用PictureCallback
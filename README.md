# RichText

> Android平台下的富文本显示TextView

* 支持Html格式文本
* 支持图片点击事件
* 链接自动回调
* 支持设置加载中和加载错误时的图片

http://blog.csdn.net/guozh/article/details/7378242

### 具体使用请查看demo
引用
http://git.oschina.net/zzhoujay/RichText

extView是不只可以String的，我们平常用的给setText()方法传递String参数的时候，其实是调用的public final void setText (CharSequence text)方法，String类是CharSequence的子类。
而CharSequence子类众多，其中有一个接口Spanned，即类似html的带标记的文本。我们可以用它来在TextView中显示html（自然，有很多html标记是不支持的，只支持一部分）。

Android.text.Html类的一个方法：

public static Spanned fromHtml (String source)  
可以将html代码转换为Spanned。


显示效果：

可以看出，字体效果基本是显示出来了，但是图片没有显示。
要实现图片的显示需要使用Html.fromHtml的另外一个重构方法：
public static Spanned fromHtml (String source, Html.ImageGetter imageGetter, Html.TagHandler tagHandler)
其中Html.ImageGetter是一个接口，我们要实现此接口，在它的getDrawable(String source)方法中返回图片的Drawable对象才可以。
修改后的代码：

Spanned sp = Html.fromHtml(html, new Html.ImageGetter() {  
    @Override  
    public Drawable getDrawable(String source) {  
        InputStream is = null;  
        try {  
            is = (InputStream) new URL(source).getContent();  
            Drawable d = Drawable.createFromStream(is, "src");  
            d.setBounds(0, 0, d.getIntrinsicWidth(),  
                    d.getIntrinsicHeight());  
            is.close();  
            return d;  
        } catch (Exception e) {  
            return null;  
        }  
    }  
}, null);  
textView.setText(sp);  
看起来有些复杂，但其实只是fromHtml()的第二个参数是一个匿名类，用以图片的获取。
其中

用以通过图片的地址获取相应的Drawable实例。
由于用到了网络资源的图片，所以要在Mainifest文件中加入权限：
[html] view plaincopyprint?
<uses-permission android:name="android.permission.INTERNET" />  
修改后的运行结果：


![演示](http://git.oschina.net/uploads/images/2015/0721/172827_3339b62f_141009.png "演示")

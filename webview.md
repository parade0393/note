 ```java
WebSettings settings = mWebView.getSettings();
settings.setNeedInitialFocus(false);// default true 初始不让任何元素抢占焦点
settings.setAppCacheEnabled(false);// default false true需要配合setAppCachePath使用
settings.setAppCachePath("");
settings.setJavaScriptEnabled(true);//default false 如果访问的页面中要与Javascript交互，则webview必须设置支持Javascript
settings.setUseWideViewPort(true);//是否支持HTML meta viewport，default false
settings.setLoadWithOverviewMode(false);//是否可以按屏幕宽度适配内容，如果上面设置true，那这里默认是false
settings.setSupportZoom(false);//是否允许本身的按钮缩放和手势缩放，default true ,此配置不影响zoomIn和zoomOut()
//以下3项是禁止使用file协议加载内容
settings.setAllowFileAccess(false);
settings.setAllowFileAccessFromFileURLs(false);//通过 file mUrl 加载的 Javascript 读取其他的本地文件 .建议关闭
settings.setAllowUniversalAccessFromFileURLs(false);
settings.setJavaScriptCanOpenWindowsAutomatically(true);//影响JavaScript的window.open()方法 default false
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {//5.1
    //适配5.0不允许http和https混合使用情况
    settings.setMixedContentMode(WebSettings.MIXED_CONTENT_ALWAYS_ALLOW);
}

 ```


# 自定义WebView验证隐式Intent的使用
## 第二个应用：自定义WebView来加载URL

![仿真机](https://img-blog.csdnimg.cn/20190424124741649.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlbHRv,size_16,color_FFFFFF,t_70)
<br/><br/>
![仿真机](https://img-blog.csdnimg.cn/20190424124818318.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlbHRv,size_16,color_FFFFFF,t_70)

* activity_main.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context="com.example.cy5962.intent_demo2.MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        >
        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textSize="20sp"
            android:hint="输入网址"
            android:layout_weight="1"
            android:id="@+id/et_url"
            />

        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="访问"
            android:id="@+id/btn_go"
            />
    </LinearLayout>


    <WebView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/my_webView"
        >

    </WebView>
</LinearLayout>
```

* AndroidManifest.xml
```
    <uses-permission android:name="android.permission.INTERNET" />
    <application
        android:usesCleartextTraffic="true"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity android:name=".MyView">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <data android:scheme="http"/>
            </intent-filter>
        </activity>

    </application>
```

* MyView.java
```
package com.example.cy5962.intent_demo2;

import android.net.Uri;
import android.os.Bundle;
import android.os.PersistableBundle;
import android.support.annotation.Nullable;
import android.support.v7.app.AppCompatActivity;
import android.webkit.WebSettings;
import android.webkit.WebView;
import android.webkit.WebViewClient;

public class MyView extends AppCompatActivity {
    private WebView webView;
    private String url;


    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.layout);
        initView();
        setWebView();
    }

    private void initView() {
        webView = (WebView) findViewById(R.id.my_view);
        url = getIntent().getData().toString();
    }


    private void setWebView() {
        WebSettings webSettings = webView.getSettings();
        webSettings.setJavaScriptEnabled(true);
        webSettings.setSupportZoom(true);

        webView.loadUrl(url);
        webView.setWebViewClient(new WebViewClient() {

            @Override
            public boolean shouldOverrideUrlLoading(WebView view, String url) {
                webView.loadUrl(url);
                return true;
            }
        });
    }
}
```
* MainActivity.java
```
package com.example.cy5962.intent_demo2;


import android.content.Intent;
import android.net.Uri;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

public class MainActivity extends AppCompatActivity implements View.OnClickListener{

    private Button btn_go;
    private EditText et_url;
    private String urlHead="http://";
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initView();
    }



    private void initView() {
        btn_go= (Button) findViewById(R.id.btn_go);
        et_url= (EditText) findViewById(R.id.et_url);
        btn_go.setOnClickListener(this);

    }


    @Override
    public void onClick(View view) {
        switch (view.getId()){
            case R.id.btn_go:
                Intent intent=new Intent(Intent.ACTION_VIEW);
                intent.setData(Uri.parse(urlHead+et_url.getText().toString()));
                Intent choose=Intent.createChooser(intent,"选择一个浏览器");
                startActivity(choose);
                break;
        }
    }

}
```

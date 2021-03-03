### 项目介绍

#### 前言

>此项目是基于Kotlin，RxJava2和Retrofit2开发的一个完全模仿于知乎日报的app，鉴于最近刚好学了kotlin语言，此项目用于练手。
其中api的地址来源于 https://github.com/izzyleung/ZhihuDailyPurify/wiki/%E7%9F%A5%E4%B9%8E%E6%97%A5%E6%8A%A5-API-%E5%88%86%E6%9E%90 。

![](files/img/2.jpg)

#### 项目整体搭建

>项目添加的部分依赖如下：

        //Design
        implementation 'com.android.support:design:26.1.0'
        //CardView
        implementation 'com.android.support:cardview-v7:26.1.0'
        //RxJava
        implementation 'io.reactivex.rxjava2:rxjava:2.1.3'
        implementation 'io.reactivex.rxjava2:rxandroid:2.0.1'
        //RxBinding
        implementation 'com.jakewharton.rxbinding2:rxbinding:2.0.0'
        //rxrelay
        implementation 'com.jakewharton.rxrelay2:rxrelay:2.0.0'
        //circleimageview
        implementation 'de.hdodenhof:circleimageview:2.1.0'
        //retrofit and okhttp
        implementation 'com.squareup.retrofit2:retrofit:2.3.0'
        implementation 'com.squareup.retrofit2:adapter-rxjava2:2.3.0'
        implementation 'com.squareup.retrofit2:converter-gson:2.3.0'
        implementation 'com.squareup.okhttp3:okhttp-urlconnection:3.8.0'
        implementation 'com.squareup.okhttp3:logging-interceptor:3.8.0'
        implementation 'com.squareup.okhttp3:okhttp:3.9.0'
        //rxlifecycle
        implementation 'com.trello.rxlifecycle2:rxlifecycle:2.2.1'
        implementation 'com.trello.rxlifecycle2:rxlifecycle-components:2.2.1'

> 其中retrofit网络封装如下：

        private constructor() {
            var logging = HttpLoggingInterceptor(object : Logger{
                override fun log(message: String?) {
                }
            })
            logging.setLevel(HttpLoggingInterceptor.Level.BODY)
            mCookieManager = CookieManager()
            mCookieManager.setCookiePolicy(CookiePolicy.ACCEPT_ALL)
            mOkHttpClient = OkHttpClient.Builder()
                    .addInterceptor(logging)
                    .connectTimeout(20, SECONDS)
                    .cookieJar(JavaNetCookieJar(mCookieManager))
                    .build()
            var url = "http://news-at.zhihu.com/api/4/"
            mRetrofit = Retrofit.Builder()
                    .baseUrl(url)
                    .addConverterFactory(GsonConverterFactory.create())
                    .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
                    .client(mOkHttpClient)
                    .build()
            api = mRetrofit.create(Api::class.java)
        }

>部分API如下：

        @GET("news/latest")
        fun latestNews(): Observable<Response<LatestNews>>

        @GET("news/before/{time}")
        fun beforeNews(@Path("time") time : String): Observable<Response<LatestNews>>

        @GET("theme/{item}")
        fun otherNews(@Path("item") item : String): Observable<Response<InternetNews>>


>因为接口比较少，功能比较少。只是简洁的封装了一次，没有对response的结果进行处理，如果在自己的项目里面一定要对其response进行处理。便于修改和处理问题。

其中在activity中调用入如下：

       HttpClient.getInstance()
                .service()
                .getSection(String.format("%d", section!!.id), time)
                .compose(SchedulerTransformer<Response<LisSections>>())
                .compose(DataTransformer<LisSections>())
                .observeOn(AndroidSchedulers.mainThread())

部分界面效果如下：

![](files/img/3.jpg) ![](files/img/4.jpg)

![](files/img/5.jpg) ![](files/img/6.jpg)


---
layout: default
title: "参考资料"
permalink: /references/
---

# 基础知识

1. 主干开发+分支发布：
   - [https://trunkbaseddevelopment.com/](https://trunkbaseddevelopment.com/)
   - [https://insights.thoughtworks.cn/gitflow-consider-harmful/](https://insights.thoughtworks.cn/gitflow-consider-harmful/)
2. 持续集成：[https://www.infoq.cn/article/ci-theory-practice/](https://www.infoq.cn/article/ci-theory-practice/)
3. 单元测试术语、模式、坏味道...：[http://xunitpatterns.com/](http://xunitpatterns.com/)
4. 结对编程：
   - [https://insights.thoughtworks.cn/pair-programming/](https://insights.thoughtworks.cn/pair-programming/)
   - [https://codingstyle.cn/topics/244](https://codingstyle.cn/topics/244)
   - [https://insights.thoughtworks.cn/seven-skills-about-pair-programming/](https://insights.thoughtworks.cn/seven-skills-about-pair-programming/)
5. 测试驱动开发：
   - [http://insights.thoughtworkers.org/talk-about-tdd-again/](http://insights.thoughtworkers.org/talk-about-tdd-again/)
   - [https://insights.thoughtworks.cn/talk-about-tdd-again-2/](https://insights.thoughtworks.cn/talk-about-tdd-again-2/)
   - [https://insights.thoughtworks.cn/when-we-talk-about-bdd/](https://insights.thoughtworks.cn/when-we-talk-about-bdd/)
6. 代码检视：[https://insights.thoughtworks.cn/code-review/](https://insights.thoughtworks.cn/code-review/)
7. 重构：[https://insights.thoughtworks.cn/principles-of-refactoring/](https://insights.thoughtworks.cn/principles-of-refactoring/)
8. 质量内建：[https://insights.thoughtworks.cn/professional/](https://insights.thoughtworks.cn/professional/)
9. 代码覆盖率：[https://insights.thoughtworks.cn/code-coverage-2/](https://insights.thoughtworks.cn/code-coverage-2/)
10. 单元测试技巧&规范:
    - [https://github.com/qinyu/junit-docs/](https://github.com/qinyu/junit-docs/)
    - [https://github.com/qinyu/junit-docs/blob/master/legacy-code.md](https://github.com/qinyu/junit-docs/blob/master/legacy-code.md)
11. Kotlin 代码规范检查：[https://detekt.github.io/detekt/](https://detekt.github.io/detekt/)

# 练习使用的工具速查

## image_url BindingAdapter 实现

[https://developer.android.com/topic/libraries/data-binding/binding-adapters](https://developer.android.com/topic/libraries/data-binding/binding-adapters)

DataBindingAdapters.kt
```kotlin
package com.cac.baseproject.binding

import android.widget.ImageView
import androidx.databinding.BindingAdapter
import androidx.lifecycle.LiveData
import androidx.recyclerview.widget.RecyclerView
import com.bumptech.glide.Glide
import com.cac.baseproject.NewsListAdapter
import com.cac.baseproject.R
import com.cac.baseproject.model.News

@Suppress("NAME_SHADOWING")
@BindingAdapter("image_url")
fun bindImageViewUrl(imageView: ImageView, thumbnailUrl: String?) {
    Glide.with(imageView).load(thumbnailUrl).placeholder(R.color.colorPrimary).into(imageView)
}
```

item_news.xml
```xml
<ImageView
    android:id="@+id/iv_thumbnail_0"
    android:layout_width="0dp"
    android:layout_height="match_parent"
    android:layout_weight="1"
    android:visibility="@{news.thumbnailUrls.size() > 0 ? View.VISIBLE : View.GONE}"
    app:image_url="@{news.thumbnailUrls.size() > 0 ? news.thumbnailUrls.get(0) : null}"/>

<ImageView
    android:id="@+id/iv_thumbnail_1"
    android:layout_width="0dp"
    android:layout_height="match_parent"
    android:layout_weight="1"
    android:visibility="@{news.thumbnailUrls.size() > 1 ? View.VISIBLE : View.GONE}"
    app:image_url="@{news.thumbnailUrls.size() > 1 ? news.thumbnailUrls.get(1) : null}" />
```

## Android Jetpack Library 速查

[https://developer.android.com/jetpack/androidx/versions](https://developer.android.com/jetpack/androidx/versions)

Android Jetpack 被分成了多个包，哪些功能在哪个包，最新的版本，方便在添加依赖

## Koltin Coroutine 测试代码片段

```kotlin
@ExperimentalCoroutinesApi
class NewsViewModelTest {
    private val testDispatcher = TestCoroutineDispatcher()

    @Rule
    @JvmField
    val rule = InstantTaskExecutorRule()

    @Before
    fun setUp() {
        Dispatchers.setMain(testDispatcher)
    }

    @After
    fun tearDown() {
        Dispatchers.resetMain()
        testDispatcher.cleanupTestCoroutines()
    }

    @Test
    fun `should get news list when call news repository`() = runBlocking {
        //given
        val webApi = mock(NewsApi::class.java)
        val newsRepository = NewsRepository(webApi)
        val newsViewModel = NewsViewModel(newsRepository)

        val newsListResponse = mockNewsListResponse()

        `when`(webApi.getNewsListResponse()).thenReturn(newsListResponse)

        //when
        newsViewModel.loadNewsList()

        //then
        verify(webApi).getNewsListResponse()
        assertThat(newsViewModel.newsList.value?.size, `is`(1))
    }

    private fun mockNewsListResponse(): NewsListResponse {
        val news = News("OPPO@CAC", "2020-6-11", "", "", "")
        val mockNewsList = ArrayList<News>()
        mockNewsList.add(news)
        val newsListResponse = NewsListResponse(mockNewsList)
        return newsListResponse
    }
}
```

## Truth断言

[https://truth.dev/](https://truth.dev/)，更易读更流畅更便利的断言（assert）库，不用记各种 macther，记住`assertThat().`就可以了

app/build.gradle 依赖配置

```groovy
dependencies {
    testImplementation 'com.google.truth:truth:1.0'
}
```

代码示例

```java
assertThat(notificationText).contains("testuser@google.com");
```

## RxJava2测试代码片段

app/build.gradle 依赖配置

```groovy
dependencies {
    implementation 'io.reactivex.rxjava2:rxjava:2.2.11'
}
```
单元测试中用到的 TestObserver 和 TestScheduler 
```java
TestScheduler testScheduler = new TestScheduler();

TestObserver<Integer> testObserver = someIntegerObservable
    .subscribeOn(testScheduler)
    .observeOn(testScheduler)
    .test();

testObserver.assertNotTerminated() // not compulsory, but STRONGLY recommended
    .assertNoErrors()
    .assertValueCount(0);// "time" hasn't started so no value expected

testScheduler.advanceTimeBy(1L, TimeUnit.SECONDS);
testObserver.assertValueCount(1);// 1 value expected after the initial delay of 1 second
```

参考：[https://proandroiddev.com/rxjava-2-unit-testing-tips-207887d3f15c](https://proandroiddev.com/rxjava-2-unit-testing-tips-207887d3f15c)

## RxAndroid

app/build.gradle 依赖配置

```groovy
dependencies {
    implementation 'io.reactivex.rxjava2:rxandroid:2.1.1'
}
```

## Retrofit

app/build.gradle 依赖配置

```groovy
dependencies {
    implementation 'com.squareup.retrofit2:retrofit:2.6.2'
    // 也可以选 GSON 或 MOSHI
    implementation 'com.squareup.retrofit2:converter-jackson:2.6.2'
    implementation 'com.squareup.retrofit2:converter-gson:2.6.2'
    implementation 'com.squareup.retrofit2:adapter-rxjava2:2.6.2'
    
    // 测试时可能使用的 Mock
    testImplementation 'com.squareup.retrofit2:retrofit-mock:2.6.2'
    testImplementation 'com.squareup.okhttp3:mockwebserver:4.2.0'
}
```

## Retrofit Mock代码片段

```java
// mock 服务
MockWebServer mockWebServer = new MockWebServer();

// 用 mock 服务构造 API
NewsApi newsApi = new Retrofit.Builder()
    .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
    .addConverterFactory(JacksonConverterFactory.create())
    .baseUrl(mockWebServer.url("/"))
    .build()
    .create(NewsApi.class);

// mock API 调用返回的 json
Buffer jsonBuffer = new Buffer()
    .readFrom(new FileInputStream("src/test/resources/news_list.json"));
mockWebServer.enqueue(
    new MockResponse()
        .setResponseCode(HTTP_OK)
        .setBody(jsonBuffer)
);
```

参考：[https://github.com/square/okhttp/tree/master/mockwebserver](https://github.com/square/okhttp/tree/master/mockwebserver)

## Espresso相关

Espresso cheatsheet：[https://developer.android.com/training/testing/espresso/cheat-sheet](https://developer.android.com/training/testing/espresso/cheat-sheet)

Espresso Koltin DSL：
 - [https://github.com/AdevintaSpain/Barista](https://github.com/AdevintaSpain/Barista)
 - [https://github.com/agoda-com/Kakao](https://github.com/agoda-com/Kakao)

app/build.gradle 依赖配置

``` groovy
dependencies {
    androidTestImplementation 'com.schibsted.spain:barista:3.2.0'
}
```

```kotlin
// 代码示例
clickOn(R.id.button)
clickOn(R.string.button_text)
clickOn("Next")
clickBack()

// 列表有关的操作和断言
clickListItem(R.id.list, 4);
scrollListToPosition(R.id.list, 4);
assertListItemCount(R.id.list, 5)
assertListNotEmpty(R.id.list)
assertDisplayedAtPosition(R.id.list, 0, "text");
assertDisplayedAtPosition(R.id.list, 0, R.id.text_field, "text");
```

## Robolectric

让`androidTest`中的espresso测试可以放到`test`中执行。

app/build.gradle中的配置，参考：[https://medium.com/androiddevelopers/write-once-run-everywhere-tests-on-android-88adb2ba20c5](https://medium.com/androiddevelopers/write-once-run-everywhere-tests-on-android-88adb2ba20c5)

```groovy
android {
    testOptions {
        unitTests.includeAndroidResources = true
    }
}

dependencies {
    testImplementation 'androidx.test:runner:1.2.0'
    testImplementation 'androidx.test.ext:junit:1.1.1'
    testImplementation 'androidx.test.espresso:espresso-intents:3.2.0'
    testImplementation 'androidx.test.espresso:espresso-core:3.2.0'
    testImplementation 'androidx.test.ext:truth:1.2.0'
    testImplementation 'org.robolectric:robolectric:4.3'
}
```

不支持 API level 29 的解决方法，参考：[http://robolectric.org/configuring/](http://robolectric.org/configuring/)

```properties
# src/test/resources/com/mycompany/app/robolectric.properties
sdk=28
```


[返回](./index.md)

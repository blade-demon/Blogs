### 前戏

一晃猪年到了，大家过完年都该逐步走上正轨，开始新一年的码农生活了，作为码农与农民伯伯不一样的是我们不仅需要辛勤劳作还要了解背后更深的知识，这样才能对付随时出现的“寒冬”。对了，听说貌似现在刚“入冬”，滴滴前几天刚裁了 2000 多人。

### 正题

昨天时间笔者在网上看到有人需要解决：从服务器获得依次两个接口最终获得所有新闻的详细页面，现状是后台小哥哥给你准备了两个接口，第一个接口获得新闻的 id，第二个接口使用获得的 id 进行拼装成 url 然后去服务器获得新闻详细页面。笔者自己也遇到过这样类似的问题，我相信大多数人也遇到过类似的问题。下面我们来讨论下怎么解决这个问题，以目前我们最常用的网络请求库`axios`为例。熟悉 async/await 的童鞋可能会这么写。

##### 写法一：async/await

```js
const axios = require("axios");
const baseURL = `https://xxx.xxx.com`;
const getData = async () => {
  // 获得所有新闻的id列表
  const response = await axios.get(`${baseURL}/idList`);
  const idList = response.data;
  let allNews = [];
  for (let id of idList) {
    // 获取单个新闻的详细信息
    const newsResponse = await axios.get(`${baseURL}/news/${id}`);
    const news = newsResponse.data;
    allNews = [...allNews, news];
  }
  // console.log(allNews);
};

// 开始执行
getData();
```

我觉得很棒，但是我就怕你以后面试的考官觉得你这么写并不能体现真正的技术，因为他就像考考你，看你对 Promise 的实际应用能力如何。所以，我们就开始 Promise 的退化之路吧。

##### 写法二：退化不完全 Promise 链式

```js
// 退化不优秀
const axios = require("axios");
const baseURL = `https://xxx.xxx.com`;
let allNews = [];
const getData = () => {
  // 获得所有新闻的id列表
  const getIdList = () => axios.get(`${baseURL}`);
  // 获得每个新闻的详细信息
  getIdList()
    .then(response => response.data.map(news => news.id))
    .then(idList => idList.map(id => axios.get(`${baseURL}/news/${id}`)))
    .then(promiseArray => {
      Promise.all(promiseArray).then(responses => {
            allNews = responses.map(response => response.data;
            console.log(allNews);
        });
      });
    });
};

// 开始执行
getData();
```

当你写出上面的代码时，如果考官不为难你，也算你能过关了。不过有的考官会问一句，你这么写不是还有嵌套吗？得，我们再来变形。其实就是 Promise.all()这边还可以继续以.then()的形式链下去。因为 Promise.all(iterable) 方法返回一个 Promise 实例，此实例在 iterable 参数内所有的 promise 都“完成（resolved）”或参数中不包含 promise 时回调完成（resolve），我们暂不考虑失败的情况。

##### 写法三：退化完全 Promise 链式，并进行优化代码逻辑

```js
// 最终链式舒服写法
const axios = require("axios");
const baseURL = `https://xxx.xxx.com`;
// API1:获得列表
const getIdList = baseURL => axios.get(`${baseURL}`);
// API2:获得单个新闻数据
const getSingleNews = (baseURL, id) => axios.get(`${baseURL}&id=${id}`);

// 主要代码实现细节
const getData = () => {
  getIdList(baseURL)
    .then(response => response.data.map(news => news.id))
    .then(idList => idList.map(id => getSingleNews(baseURL, id)))
    .then(promiseArray => Promise.all(promiseArray))
    .then(responses => responses.map(response => response.data))
    .then(allNews => console.log(allNews));
};
// 开始执行
getData();
```

其实我们查看下[axios 的文档](https://www.npmjs.com/package/axios)会发现，axios 其实是有允许同时执行多个请求的方法：**axios.all**，其实背后原理也很简单，就是用我们的 Promise.all 去实现的，[查看源码](https://github.com/axios/axios/blob/405fe690f93264d591b7a64d006314e2222c8727/lib/axios.js)，然后再用`axios.spread`将获得结果从数组扩展成参数列表[查看源码](https://github.com/axios/axios/blob/405fe690f93264d591b7a64d006314e2222c8727/lib/helpers/spread.js)。

```js
function getUserAccount() {
  return axios.get("/user/12345");
}

function getUserPermissions() {
  return axios.get("/user/12345/permissions");
}

axios.all([getUserAccount(), getUserPermissions()]).then(
  axios.spread(function(acct, perms) {
    // Both requests are now complete
  })
);
```

大家可以试一下用 axios.all 去改写下试试，而在实际工作中根据需要使用不同的方法去实现。

##### 写法四：尝试使用 Reduce 写

其实上面的写法看起来很完美，但是有一点可能会忽略：**虽然 Promsie.all()可以保证所有 Promise Array 里面的异步任务完成后统一返回所有数据，但是他并不能保证其执行顺序依次执行，而是所有异步任务一起执行。**那我们还有没有更高（zhuang）端（bi）完美的写法呢？对了，有的童鞋可能会想到了 reduce 了。不太会使用 reduce 的同学可以去查看[Reduce MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)。reduce() 方法对数组中的每个元素执行一个由您提供的 reducer 函数(升序执行)，将其结果汇总为单个返回值。举个简单好吃易消化的栗子。

```js
const array1 = [1, 2, 3, 4];
// reducer 函数
const reducer = (accumulator, currentValue) => accumulator + currentValue;

// 1 + 2 + 3 + 4，没有初始值就是0
console.log(array1.reduce(reducer));
// expected output: 10

// 5 + 1 + 2 + 3 + 4，这里的5是初始值
console.log(array1.reduce(reducer, 5));
// 15

// 也可以这么写
[0, 1, 2, 3, 4].reduce(function(
  accumulator,
  currentValue,
  currentIndex,
  array
) {
  return accumulator + currentValue;
});
```

reducer 函数接收 4 个参数:`Accumulator (acc) (累计器，必须)，Current Value (cur) (当前值，必须)，Current Index (idx) (当前索引，可选)，Source Array (src) (源数组，可选)`。

简单介绍了 reducer 那我们就开始使用 reducer 进行实操吧，开撸！

```js
// 有八阿哥版
const axios = require("axios");
const baseURL = `https://xxx.xxx.com`;
// API1:获得列表
const getIdList = baseURL => axios.get(`${baseURL}`);
// API2:获得单个新闻数据
const getSingleNews = (baseURL, id) => axios.get(`${baseURL}&id=${id}`);

// 主要代码实现细节
const getData = () => {
  getIdList(baseURL)
    .then(response => response.data.map(news => news.id))
    // 为了能够继续使用then, reducer函数里面每一次执行都必须返回一个promise。
    .then(idList =>
      idList.reduce((promiseChain, currentId) =>
        promiseChain.then(() => getSingleNews(baseURL, currentId))
      )
    )
    .then(responses => responses.map(response => response.data))
    .then(allNews => console.log(allNews));
};
// 开始执行
getData();
```

执行结果看看是啥？发现只获得了最后一个新闻的内容，前面的新闻内容都丢失了。**此处要划重点了。**怎么才能让前面的值不丢失呢？

```js
// 最终Reducer版
const axios = require("axios");
const baseURL = `https://xxx.xxx.com`;
// API1:获得列表
const getIdList = baseURL => axios.get(`${baseURL}`);
// API2:获得单个新闻数据
const getSingleNews = (baseURL, id) => axios.get(`${baseURL}&id=${id}`);

// 主要代码实现细节
const getData = () => {
  getIdList(baseURL)
    .then(response => response.data.map(news => news.id))
    .then(idList =>
      idList.reduce(
        // reducer 函数
        (promiseChain, currentId) =>
          // 每次执行的时候附带上次执行返回的结果：chainResults
          promiseChain.then(chainResults =>
            // 获得单个新闻的详细信息，并扩展运算符浅拷贝到新数组里面去，这个新数组在每次执行后都会增加一个当前新获得的新闻
            getSingleNews(baseURL, currentId).then(currentResult => [
              ...chainResults,
              currentResult
            ])
          ),
        // 设置初始值为空数组，用来存放最终的详细新闻列表
        Promise.resolve([])
      )
    )
    .then(responses => responses.map(response => response.data))
    .then(allNews => console.log(allNews));
};
// 开始执行
getData();
```

其实 reduce 版的核心代码就是：

```js
const tasks = getTaskArray();
return tasks
  .reduce((promiseChain, currentTask) => {
    return promiseChain.then(chainResults =>
      currentTask.then(currentResult => [...chainResults, currentResult])
    );
  }, Promise.resolve([]))
  .then(arrayOfResults => {
    // Do something with all results
  });
```

至此，我们的 Promise 的应用之路就算走得差不多挺完美的了。

### 尾声

大家如果觉得有什么改进或者不足也请下面指正。最后祝各位在以后工作，面试，chuibi，带妹（弟）之路更加一帆风顺。![](https://user-gold-cdn.xitu.io/2019/2/21/1690ef8e92e76c95?w=99&h=98&f=png&s=20643)

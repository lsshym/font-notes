```javascript
{
    //需代理的应用
    context:['/logaudit'],
    pathRewrite:{
    "^/logaudit":'/mock/216/logaudit'
    },
    //代理目标主机
    target:'http://192.168.10.163:3000',
    //代理域名
    changeOrigin: true,
    // 利用node.js重写domain,解决tomcat接口重定向（302）导致接口跨域问题
    hostRewrite:'localhost:8080'
}
// 真实URL
// http://192.168.10.163:3000/mock/216/logaudit/sql/pageQuerySqlLogByQueryCondition
// http://localhost:8080/logaudit/sql/pageQuerySqlLogByQueryCondition

```


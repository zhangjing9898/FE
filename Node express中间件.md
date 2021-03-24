# 中间件

如果在我们的get、post回调函数中，没有next参数，那么就会匹配第一个路由，不会往下匹配。
如果我们想要向下匹配，就需要写next()
```
1app.get("/",function(req,res,next){
2    console.log("1");
3    next();
4});
5
6app.get("/",function(req,res){
7    console.log("2");
});
```
下面这两个路由，感觉没关系：
```
1app.get("/:username/:id",function(req,res){
2    console.log("1");
3    res.send("用户信息" + req.params.username);
4});
5
6app.get("/admin/login",function(req,res){
7    console.log("2");
8    res.send("管理员登录");
});
```
但是实际上还是冲突了，因为admin可以当用户名，login也可以当id

解决方法：
1：交换位置。 也就是说，express中所有的路由（中间件）的顺序至关重要。
匹配上第一个，就不会往下匹配了。 具体的往上写，抽象的往下写。

```
1app.get("/admin/login",function(req,res){
2    console.log("2");
3    res.send("管理员登录");
4});
5
6app.get("/:username/:id",function(req,res){
7    console.log("1");
8    res.send("用户信息" + req.params.username);
});
```

2.
```
1app.get("/:username/:id",function(req,res,next){
2    var username = req.params.username;
3    //检索数据库，如果username不存在，那么next()
4    if(检索数据库){
5        console.log("1");
6        res.send("用户信息");
7    }else{
8        next();
9    }
10});
11
12app.get("/admin/login",function(req,res){
13    console.log("2");
14    res.send("管理员登录");
});
```

路由get、post这些东西，就是中间件，中间件讲究顺序，匹配上第一个之后，就不会往后匹配了。next函数才能够继续往后匹配。

##### app.use()也是一个中间件，与get、post不同的是，他的网址不是精确匹配的，而是能够有小文件夹拓展的，比如网址：http://127.0.0.1:3000/admin/aa/bb/cc/dd

```
1app.use("/admin",function(req,res){ 
2    res.write(req.originalUrl + "\n");   //    /admin/aa/bb/cc/dd
3    res.write(req.baseUrl + "\n");  //   /admin
4    res.write(req.path + "\n");   //    /aa/bb/cc/dd
5    res.end("你好");
});
```

如果写一个/
```
1//当你不写路径的时候，实际上就相当于"/"，就是所有网址
2app.use(function(req,res,next){
3    console.log(new Date());
4    next();
});
```
app.use()给我们增加了一些特定功能的便利场所。
实际上app.use()的东西，基本上都从第三方能得到。

*  大多数情况下，渲染内容用res.render()，将会根据views中的模板文件进行渲染。如果不想使用views文件夹，想自己设置文件夹名字，那么app.set("views","aaaa");

*  如果想写一个快速测试页，当然可以使用res.send()。这个函数将根据内容，自动帮我们设置了Content-Type头部和200状态码。send()只能用一次，和end一样。和end不一样在哪里？能够自动设置MIME类型。

*  如果想使用不同的状态码，可以：
    res.status(404).send('Sorry, we cannot find that!');

*  如果想使用不同的Content-Type，可以：
    res.set('Content-Type', 'text/html');

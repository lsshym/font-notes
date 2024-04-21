electron-vue安装失败



window系统需要额外安装一些东西，以及更改内容

#### A note for Windows Users



https://simulatedgreg.gitbooks.io/electron-vue/content/en/getting_started.html#a-note-for-windows-users

If you run into errors during `npm install` about `node-gyp`, then you most likely do not have the proper build tools installed on your system. Build tools include items like Python and Visual Studio. Thanks to [@felixrieseberg](https://github.com/felixrieseberg), there are a few packages to help simplify this process.

The first item we need to check is our npm version and ensure that it is not outdated. This can is accomplished using [`npm-windows-upgrade`](https://github.com/felixrieseberg/npm-windows-upgrade). If you are using `yarn`, then you can skip this check.

Once that is complete, we can then continue to setup the needed build tools. Using [`windows-build-tools`](https://github.com/felixrieseberg/windows-build-tools), most of the dirty work is done for us. Installing this globally will in turn setup Visual C++ packages, Python, and more.

At this point things should successfully install, but if not then you will need a clean installation of Visual Studio. Please note that these are not direct problems with electron-vue itself (Windows can be difficult sometimes

**主要是这个 Using [`windows-build-tools`](https://github.com/felixrieseberg/windows-build-tools),**



另外并不是所有的包都会发布在yarn

第一次碰到node版本不兼容问题

更改模板

修改.electron-vue/webpack.web.config.js和.electron-vue/webpack.renderer.config.js文件的HtmlWebpackPlugin，添加templateParameters，修改后如下：

```css
new HtmlWebpackPlugin({
      filename: 'index.html',
      template: path.resolve(__dirname, '../src/index.ejs'),
      templateParameters(compilation, assets, options) {
        return {
          compilation: compilation,
          webpack: compilation.getStats().toJson(),
          webpackConfig: compilation.options,
          htmlWebpackPlugin: {
            files: assets,
            options: options
          },
          process,
        };
      },
      minify: {
        collapseWhitespace: true,
        removeAttributeQuotes: true,
        removeComments: true
      },
      nodeModules: false
    }),
    
```



![image-20200530203726044](C:\Users\Mr.Giraffe\AppData\Roaming\Typora\typora-user-images\image-20200530203726044.png)

将所有的包安装在`devDependencies`下



















问题收集 TypeError: fs.existsSync is not a function



但个人觉得更为合理的解释是，electron本身就是node服务端内容，而import默认是由webpack来处理，相当于是浏览器进程来尝试加载。浏览器进程不能调用服务进程的一些接口，如fs模块只能在服务端加载，这时候在浏览器进程加载electron就会报错。



作者：夏天的小明
链接：https://www.jianshu.com/p/308ff85958d6
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

用路由的方式打开新的窗口

https://www.jianshu.com/p/98df105042c0





TypeError: Cannot destructure property 'BrowserWindow' of 'window.remote' as it is undefined不知道什么错误





https://blog.csdn.net/zoepriselife316/article/details/89920309

https://www.cnblogs.com/lfqcode/p/9534754.html
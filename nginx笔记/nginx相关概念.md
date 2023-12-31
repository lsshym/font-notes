## 正向代理和反向代理
```
正向代理：局域网中的电脑用户想要直接访问网络是不可行的，只能通过代理服务器（Server）来访问，这种代理服务就被称为正向代理。
我也不知道为啥直接访问不行

反向代理：客户端无法感知代理，因为客户端访问网络不需要配置，只要把请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据，然后再返回到客户端，此时反向代理服务器和目标服务器对外就是一个服务器，暴露的是代理服务器地址，隐藏了真实服务器IP地址。

正向代理：A通过电话直接给B打电话；反向代理：A给B的秘书打电话，B的秘书去联系B再转达给A
```
起个反向代理的名字估计是音译的，没啥跟`反`有关系的，

## 负载均衡
平衡服务器压力，将请求分配到多个子服务器上

## 动静分离

nginx分为动态资源和静态资源，一般webpack打包出来的全是静态资源，但是静态资源可以请求动态资源，如axios请求一个json，当一个axios请求写在一个JavaScript文件中时，这个JavaScript文件本身是静态资源，因为它在构建时被打包成一个静态文件，不会根据用户请求的不同而变化。

但是，这个JavaScript文件中包含的axios请求是用于获取动态数据的代码。这个请求会在用户访问页面时被执行，根据请求的参数和服务器的响应动态获取数据。所以，虽然JavaScript文件是静态的，它包含的axios请求用于获取动态资源。

#### nginx要怎么区分动态和静态资源，它是怎么知道的
Nginx 可以通过配置文件和请求的路径来区分动态资源和静态资源。通常情况下，Nginx不会知道资源的动态或静态性质，而是根据您的配置来处理这些请求。以下是一些常见的方法来配置 Nginx 以区分动态和静态资源：

1. **根据文件扩展名区分**：通常，静态资源（如图片、CSS、JavaScript）的文件扩展名是已知的，例如`.jpg`、`.css`、`.js`等。您可以通过 Nginx 配置文件使用 `location` 块来匹配这些文件扩展名，并指定如何处理这些请求。例如：

   ```nginx
   location ~* \.(jpg|jpeg|gif|css|js)$ {
       # 静态资源处理方式，例如使用 alias 指令指向静态资源目录
       alias /path/to/static/files;
   }
   ```

2. **根据路径前缀区分**：您还可以根据请求路径的前缀来区分资源。例如，静态资源可以位于 `/static/` 路径下，您可以使用 `location` 块来匹配这个前缀并处理这些请求。例如：

   ```nginx
   location /static/ {
       # 静态资源处理方式，例如使用 alias 指令指向静态资源目录
       alias /path/to/static/files;
   }
   ```

3. **反向代理至应用服务器**：对于动态资源，您可以将请求反向代理到后端应用服务器，然后由应用服务器生成动态内容。这通常涉及到配置一个反向代理规则，将请求传递给后端服务器处理。例如：

   ```nginx
   location /api/ {
       # 反向代理至后端应用服务器
       proxy_pass http://backend-server;
   }
   ```

4. **使用变量和正则表达式**：Nginx 还允许您使用变量和正则表达式来更灵活地匹配请求，以便根据需要自定义资源的处理。这样您可以根据请求中包含的信息来决定如何处理资源。

总之，Nginx 的配置文件允许您根据请求的路径、文件扩展名、前缀等条件来区分动态资源和静态资源，并决定如何处理它们。这使得您可以根据项目的需求和架构来灵活配置 Nginx 以有效地分发不同类型的资源。

如果不进行配置，就不区分动静态

# Google Pipe

Google pipe 是基于ASP.Net Core 1.0开发的谷歌反向代理网站。

演示网址：[http://www.gglink.pw](http://www.gglink.pw)

您可以在Linux、Windows、Mac上部署这个网站系统，但前提是能够访问到www.google.com，这样就可以让其他用户通过您的网站访问Google了。

部署时，需要修改Startup.cs中app.UseReverseProxy中第二个参数，即域名部分。请在解析列表中使用泛解析，即`*.yourdomain.postfix`。

这个GitHub仓库中存储的是源代码，不是发布版本，因此如果您欲直接使用源代码部署，可能需要安装相应的环境，如dnvm、dnx、dnu等必要软件，您可以参阅微软官方网站获得更多信息：[http://docs.asp.net](http://docs.asp.net)

这份代码只有短短37行，即[Startup.cs](https://github.com/CodeComb/GooglePipe/blob/master/src/GooglePipe/Startup.cs)，只需要引用NuGet包：[CodeComb.AspNet.ReverseProxy](https://www.nuget.org/packages/CodeComb.AspNet.ReverseProxy)即可实现反向代理，支持泛域名。

```c#
using System.Net;
using Microsoft.AspNet.Builder;
using Microsoft.AspNet.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.DependencyInjection;

namespace GooglePipe
{
    public class Startup
    {
        public void ConfigureServices(IServiceCollection services)
        {
        }
        
        public void Configure(IApplicationBuilder app, ILoggerFactory logger)
        {
#if DNX451
            ServicePointManager.ServerCertificateValidationCallback += (sender, cert, chain, sslPolicyErrors) => true;
#endif
            logger.AddConsole();
            logger.MinimumLevel = LogLevel.Debug;
            
            app.UseReverseProxy("https://*.google.com", "http://*.gglink.pw", x => 
            {
                x = x.Replace("<span id=\"fsr\">", "<span id=\"fsr\"><svg style=\"position:relative;bottom:-8px\" aria-hidden=\"true\" class=\"octicon octicon-mark-github\" height=\"28\" role=\"img\" version=\"1.1\" viewBox=\"0 0 16 16\" width=\"28\"><path d=\"M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59 0.4 0.07 0.55-0.17 0.55-0.38 0-0.19-0.01-0.82-0.01-1.49-2.01 0.37-2.53-0.49-2.69-0.94-0.09-0.23-0.48-0.94-0.82-1.13-0.28-0.15-0.68-0.52-0.01-0.53 0.63-0.01 1.08 0.58 1.23 0.82 0.72 1.21 1.87 0.87 2.33 0.66 0.07-0.52 0.28-0.87 0.51-1.07-1.78-0.2-3.64-0.89-3.64-3.95 0-0.87 0.31-1.59 0.82-2.15-0.08-0.2-0.36-1.02 0.08-2.12 0 0 0.67-0.21 2.2 0.82 0.64-0.18 1.32-0.27 2-0.27 0.68 0 1.36 0.09 2 0.27 1.53-1.04 2.2-0.82 2.2-0.82 0.44 1.1 0.16 1.92 0.08 2.12 0.51 0.56 0.82 1.27 0.82 2.15 0 3.07-1.87 3.75-3.65 3.95 0.29 0.25 0.54 0.73 0.54 1.48 0 1.07-0.01 1.93-0.01 2.2 0 0.21 0.15 0.46 0.55 0.38C13.71 14.53 16 11.53 16 8 16 3.58 12.42 0 8 0z\"></path></svg> <a target=\"_blank\" href=\"https://github.com/codecomb/googlepipe\">View source on GitHub</a>");
                x = x.Replace(".google.com.hk", ".gglink.pw");
                x = x.Replace(".gglink.pw.hk", ".gglink.pw");
                x = x.Replace("www.gstatic.com", "www.gglink.pw");
                x = x.Replace("ssl.gstatic.com", "ssl.gglink.pw");
                x = x.Replace("http://www.google.com", "http://www.gglink.pw");
                return x;
            });
        }

        public static void Main(string[] args) => WebApplication.Run<Startup>(args);
    }
}
```

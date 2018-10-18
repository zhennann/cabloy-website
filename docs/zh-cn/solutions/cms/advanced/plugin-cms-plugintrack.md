# 插件：cms-plugintrack

模块`cms-plugintrack`是官方提供的站点统计插件，实现常用站点统计代码的渲染，包括google、baidu、qq

## 渲染代码

`cms-plugintrack/backend/cms/plugin/track.ejs`

``` javascript
<%
const track=site.plugins['cms-plugintrack'].track;
%>
<% if(track.google){ %>
  <script async src="https://www.googletagmanager.com/gtag/js?id=<%=track.google%>"></script>
  <script>
    window.dataLayer = window.dataLayer || [];
    function gtag(){dataLayer.push(arguments);}
    gtag('js', new Date());
    gtag('config', '<%=track.google%>');
  </script>
<% } %>
<% if(track.baidu){ %>
  <script>
  var _hmt = _hmt || [];
  (function() {
    var hm = document.createElement("script");
    hm.src = "https://hm.baidu.com/hm.js?<%=track.baidu%>";
    var s = document.getElementsByTagName("script")[0];
    s.parentNode.insertBefore(hm, s);
  })();
  </script>
<% } %>
<% if(track.qq){ %>
  <script type="text/javascript" src="https://tajs.qq.com/stats?sId=<%=track.qq%>" charset="UTF-8"></script>
<% } %>
```

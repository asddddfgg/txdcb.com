quartz\components\Head.tsx
<font color="#ff0000">不是Header.tsx</font>

  {additionalHead.map((resource) => {
          if (typeof resource === "function") {
            return resource(fileData)
          } else {
            return resource
          }
        })}
      </head>
    )
  }

  return Head
}) satisfies QuartzComponentConstructor

上边这一堆代码前边加上百度统计代码，
你拿过来就用不可以，得加上dangerous那玩意儿，要不然刷一溜警告
        <script
          dangerouslySetInnerHTML={{
            __html: `
              var _hmt = _hmt || [];
              (function() {
                var hm = document.createElement("script");
                hm.src = "https://hm.baidu.com/hm.js?19f0f36c4c000e07fe0661d936e7366c";
                var s = document.getElementsByTagName("script")[0]; 
                s.parentNode.insertBefore(hm, s);
              })();
            `,
          }}
        />

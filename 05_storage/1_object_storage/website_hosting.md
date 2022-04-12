---
author: 张果
created_at: 2022-04-12
updated_at: 2022-04-12
---

# 静态网站托管

https://cloud.tencent.com/document/product/436/14984

## 单页应用

单页应用(Single Page Application, SPA)是

[Flutter的URL Strategy](https://docs.flutter.dev/development/ui/navigation/url-strategies)
[History模式](https://developer.mozilla.org/en-US/docs/Web/API/History_API)

### 路由配置

[404重定向](https://cloud.tencent.com/document/product/436/56555#.E9.9D.99.E6.80.81.E7.BD.91.E7.AB.99.E5.8A.9F.E8.83.BD.E9.85.8D.E5.90.88.E5.89.8D.E7.AB.AF-vue-.E6.A1.86.E6.9E.B6.E4.B8.80.E8.B5.B7.E4.BD.BF.E7.94.A8.EF.BC.8C.E5.BD.93.E8.B7.AF.E7.94.B1.E8.AE.BE.E7.BD.AE.E4.B8.BA-history-.E6.A8.A1.E5.BC.8F.EF.BC.8C.E5.88.B7.E6.96.B0.E9.A1.B5.E9.9D.A2.E9.81.87.E5.88.B0404.E9.97.AE.E9.A2.98.E6.80.8E.E4.B9.88.E5.8A.9E.EF.BC.9F)。

### 根目录设置

但这个方法有一个问题是，只允许`web/index.html`的`<base>`标签配置绝对路径而**不允许相对路径**。这会为云端环境部署时带来新的问题。[Stack Overflow的问答](https://stackoverflow.com/questions/59870357/how-to-remove-hash-from-url-in-flutter-web/65709246#65709246)对此有进一步的解答。

在云开发的静态网站部署中，由于一个环境提供一个存储桶，根目录是根目录，如果Flutter for Web应用没有在此环境的根目录下，则Flutter for Web应用无法正常运行。在本地时，只需要简单设置为`/`即可，而在云端则需要是`<folder_name>/`，这会不一致。

有两种可能的思路：

1. 想办法让云端配置在本地可以正常运行。
2. 设法写JS传入路径，让本地和云端使用不同的配置，以保证兼容性。
3. 联系云开发静态网站团队商议解决办法。

[Updated in 2021-10-20]已经换对象存储解决问题。

[Updated in 2022-04-12]新版Flutter for Web可以在环境变量设置根路径，在CI中使用即可。可以解决上述带来的问题，因此CI中肯定会写部署到存储桶的哪个路径，这样云开发或者COS都没有问题了。

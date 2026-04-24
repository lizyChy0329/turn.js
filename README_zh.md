# 📚 项目静态资源引用指南

[English](.) | [简体中文](./README_zh.md)

---

本文档记录了项目中用于实现 **书本翻页效果 (Flipbook)** 的核心依赖库。

## 🛠 核心依赖 (CDN)

为了保证加载速度与稳定性，建议在 HTML 的 `<head>` 或 `<body>` 底部按顺序引入以下资源：

### 1. jQuery
* **版本**: `4.0.0`
* **描述**: `turn.js` 的核心驱动依赖。
* **链接**: 
    ```html
    <script src="https://cdn.jsdelivr.net/npm/jquery@4.0.0/dist/jquery.min.js"></script>
    ```

### 2. Turn.js
* **版本**: `4.1.0` (Minified)
* **描述**: 强大的 HTML5 翻页效果库，适用于画册、电子书展示。
* **链接**:
    ```html
    <script src="https://cdn.jsdelivr.net/gh/lizyChy0329/turn.js@4.1.0/turn.min.js"></script>
    ```

---

## 🚀 快速集成示例

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>翻页电子书演示</title>
    <style>
        #flipbook { width: 800px; height: 500px; }
        #flipbook .page { background: white; border: 1px solid #ccc; line-height: 500px; text-align: center; }
    </style>
</head>
<body>

    <div id="flipbook">
        <div class="hard"> 封面 </div>
        <div class="page"> 第一页 </div>
        <div class="page"> 第二页 </div>
        <div class="hard"> 封底 </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/jquery@4.0.0/dist/jquery.min.js"></script>
    <script src="https://cdn.jsdelivr.net/gh/lizyChy0329/turn.js@4.1.0/turn.min.js"></script>

    <script>
        $(document).ready(function() {
            $("#flipbook").turn({
                width: 800,
                height: 500,
                autoCenter: true
            });
        });
    </script>
</body>
</html>
```

---

## ⚠️ 注意事项

1.  **加载顺序**: 必须先加载 `jquery`，再加载 `turn.min.js`，否则会报错 `$ is not defined`。
2.  **版本匹配**: `jQuery 4.0.0` 是较新版本，若发现 `turn.js` 出现兼容性问题（如插件无法挂载），请尝试回退至 `jQuery 3.x` 系列。
3.  **HTTPS**: 以上 CDN 均支持 HTTPS，请确保在生产环境中统一使用加密链接。

> [!TIP]
> **资源来源**: `turn.js` 托管于 GitHub 仓库，由 `jsDelivr` 提供全球加速。

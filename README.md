# 📚 Guidelines for referencing static resources in a project

This document records the core dependency libraries used in the project to implement the **flipbook effect**.

## 🛠 Core Dependencies (CDN)

To ensure loading speed and stability, it is recommended to use the `<html>` tag in the HTML.<head> ` or `<body> The following resources are listed at the bottom in order:

### 1. jQuery
* **Version**: `4.0.0`
* **Description**: The core driver dependency of `turn.js`.
* **Link**:
```html
<script src="https://cdn.jsdelivr.net/npm/jquery@4.0.0/dist/jquery.min.js"></script>
```

### 2. Turn.js
* **Version**: `4.1.0` (Minified)
* **Description**: A powerful HTML5 page-turning effect library, suitable for displaying brochures and e-books.
* **Link**:
```html
<script src="https://cdn.jsdelivr.net/gh/lizyChy0329/turn.js@4.1.0/turn.min.js"></script>
```

---

## 🚀 Quick Integration Example

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<title>Page-turning eBook Demonstration</title>
<style>
#flipbook { width: 800px; height: 500px; }
#flipbook .page { background: white; border: 1px solid #ccc; line-height: 500px; text-align: center; }
</style>
</head>
<body>

<div id="flipbook">
<div class="hard">Cover</div>
<div class="page">Page 1</div>
<div class="page">Page 2</div>
<div class="hard">back cover</div>
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

## ⚠️ Important Notes

1. **Loading order**: `jquery` must be loaded first, then `turn.min.js`, otherwise the error `$ is not defined` will occur.
2. **Version Compatibility**: `jQuery 4.0.0` is a newer version. If you encounter compatibility issues with `turn.js` (such as plugins failing to mount), please try reverting to the `jQuery 3.x` series.
3. **HTTPS**: All of the above CDNs support HTTPS. Please ensure that encrypted links are used consistently in the production environment.

[!TIP]
**Resource Source:** `turn.js` is hosted on a GitHub repository and globally accelerated by `jsDelivr`.

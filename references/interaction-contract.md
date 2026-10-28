# 原型与 PRD 联动协议

本协议用于拆分后的 `prototype.html` 与 PRD 展示页。PRD 的可下载源文件仍应是 `prd.md`；单文件工作台可以复用同样的 `featureId` 和定位函数，不必使用 `postMessage`。

## 消息格式

```js
{
  type: "linked-prd-prototype:focus",
  featureId: "search-filter",
  source: "prototype" // 或 "prd"
}
```

发送方只发送稳定的 `featureId`，不要发送 DOM 节点、HTML 或用户输入。接收方收到消息后，先确认 `event.origin` 是允许的来源，再按 `featureId` 查找自己的目标节点。

## 发送方

```js
function sendFocus(featureId, source) {
  if (!featureId || !window.parent || window.parent === window) return;
  window.parent.postMessage(
    { type: "linked-prd-prototype:focus", featureId, source },
    window.location.origin
  );
}
```

如果两个页面不是同源静态文件，使用部署后的明确 origin 配置，不要使用 `*`。如果无法确定安全来源，退回到同页锚点或新页面 `?focus=`。

## 接收方

```js
window.addEventListener("message", (event) => {
  if (event.origin !== window.location.origin) return;
  const data = event.data || {};
  if (data.type !== "linked-prd-prototype:focus") return;
  const target = document.querySelector(
    `[data-feature-id="${CSS.escape(data.featureId)}"]`
  );
  if (!target) return;
  target.scrollIntoView({ behavior: "smooth", block: "center" });
  target.classList.add("is-focused");
  target.setAttribute("aria-current", "true");
  window.setTimeout(() => {
    target.classList.remove("is-focused");
    target.removeAttribute("aria-current");
  }, 1400);
});
```

跨文档工作台应在父页面维护 `?focus=`，并把焦点消息转发给另一侧 iframe。若直接打开子页面，子页面仍应支持自己的 `?focus=` 参数，这样导出的页面不会失去可用性。

## 可访问性与回退

- 可点击的非按钮元素要有 `role="button"`、`tabindex="0"`，并处理 Enter/Space。
- 高亮不能只依赖颜色；同时使用边框、阴影或短暂的“已定位”文字。
- `CSS.escape` 不存在时，使用映射表中的预生成选择器，不把未经校验的字符串拼入选择器。
- 接收无效 ID 时不改变当前滚动位置、不弹出错误框。

## 单文件工作台的下载约定

单文件工作台应在右上角提供两个明确的下载按钮：

- `下载 PRD` 生成 `prd.md`，内容来自当前右侧 PRD 的 Markdown 源文本。
- `下载原型` 生成 `prototype.html`，内容至少包含当前左侧原型的标记、响应式样式和必要交互。

优先使用浏览器原生 `Blob` 和 `URL.createObjectURL`，这样用户直接双击本地 HTML 也能下载，不需要后端接口。工作台内嵌原型与下载原型应从同一份模板或数据生成，避免用户看到的内容和下载内容出现漂移。

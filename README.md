# container-resizer

A Vue 3 component for resizing elements, supporting drag adjustment in both horizontal and vertical directions.

<img src=".\img\1.gif" alt="demo" width="500px"/>

## Type definition

```typescript
interface Props {

	direction: 'horizontal' | 'vertical'; // 调整方向

	modelValue: number; // v-model 绑定值（百分比）

	containerSelector?: string; // 容器选择器（可选）

	minPercent?: number; // 最小百分比（默认：20）

	maxPercent?: number; // 最大百分比（默认：80）

}
```

## Basic Usage

```vue
<template>
  <div class="container">
    <!-- 左侧面板 -->
    <div class="left-panel" :style="{ width: `${leftWidth}%` }">
      内容...
    </div>
    <!-- 分隔条 -->
    <Resizer
        direction="horizontal"
        v-model="leftWidth"
        :min-percent="30"
        :max-percent="70"
    />
    <!-- 右侧面板 -->
    <div class="right-panel" :style="{ width: `${100 - leftWidth}%` }">
      内容...
    </div>
  </div>
</template>
<script setup lang="ts">
import { ref } from 'vue';
import Resizer from './Resizer.vue';
const leftWidth = ref(50); // 初始宽度为 50%
</script>
```

## Attribute Description

| 属性名            | 类型                       | 必填 | 默认值 | 说明                                    |
| ----------------- | -------------------------- | ---- | ------ | --------------------------------------- |
| direction         | 'horizontal' \| 'vertical' | 是   | -      | 调整方向：水平或垂直                    |
| modelValue        | number                     | 是   | -      | 当前尺寸的百分比值（v-model 绑定值）    |
| containerSelector | string                     | 否   | ''     | 容器元素的 CSS 选择器，为空时使用父元素 |
| minPercent        | number                     | 否   | 20     | 最小百分比限制                          |
| maxPercent        | number                     | 否   | 80     | 最大百分比限制                          |

## Event

| 事件名            | 参数类型 | 说明                        |
| ----------------- | -------- | --------------------------- |
| update:modelValue | number   | 更新尺寸百分比值（v-model） |

## Advanced Usage

### Specify container

```vue
<Resizer
	direction="horizontal"
	v-model="width"
	container-selector=".custom-container"
	:min-percent="20"
	:max-percent="80"
/>
```

### Vertical adjustment

```vue
<Resizer
	direction="vertical"
	v-model="height"
	:min-percent="30"
	:max-percent="70"
/>
```

## Style customization

The component provides basic style classes that can be customized for appearance by overriding the following CSS classes:

```css
// 基础样式
.resizer {
  position: relative;
  z-index: 100;
  flex: none;
  transition: background-color 0.2s;
  background-color: transparent;
}
//水平方向样式
.resizer.horizontal {
  margin: 3px;
  width: 3px;
  cursor: col-resize;
}
// 垂直方向样式
.resizer.vertical {
  margin: 3px;
  height: 3px;
  cursor: row-resize;
}
// 悬停效果
.resizer:hover {
  background-color: #409eff;
}
```

## Precautions

1. Container settings:
   -Ensure that container elements have clear width/height
   -Containers should use relative positioning (position: relative)
   -If using containerselector, ensure that the selector matches the target element correctly

2. Style requirements:
-Adjacent elements need to be resized using percentages
-Suggest using flex or grid layout to organize the elements inside the container
3. Performance considerations:
-The component will frequently update its size when dragged
-Suggest using CSS transform to optimize rendering performance

## Implement details

- -Use native DOM events (mousedown, mousemove, mouseup) to handle dragging
- -Real time percentage calculation based on container size
- -Disable text selection when dragging to enhance user experience
- -Automatically clean up event listeners to prevent memory leaks

## Example project

```vue
<!-- 分割面板示例 -->
<template>
  <div class="split-panel">
    <div class="panel-left" :style="{ width: `${splitPosition}%` }">
      左侧面板
    </div>
    <Resizer
        direction="horizontal"
        v-model="splitPosition"
        :min-percent="30"
        :max-percent="70"
    />
    <div class="panel-right" :style="{ width: `${100 - splitPosition}%` }">
      右侧面板
    </div>
  </div>
</template>
<script setup lang="ts">
import {ref} from 'vue';
import Resizer from "./Resizer.vue";

const splitPosition = ref(50);
</script>
<style scoped>
.split-panel {
  display: flex;
  height: 300px;
  width: 800px;
}

.panel-left {
  width: 300px;
  height: 300px;
  overflow: auto;
  background-color: red;
}

.panel-right {
  width: 300px;
  height: 300px;
  overflow: auto;
  background-color: blue;
}
</style>
```


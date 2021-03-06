# 前言

> 瀑布流，又称瀑布流式布局。 是比较流行的一种网站页面布局，视觉表现为参差不齐的多栏布局，随着页面滚动条向下滚动，这种布局还会不断加载数据块并附加至当前尾部。

最终实现效果如下

**滚动加载：**

![](https://upload-images.jianshu.io/upload_images/10390288-8f8671f31a9f397c.gif?imageMogr2/auto-orient/strip)

**宽度自适应：**

![](https://upload-images.jianshu.io/upload_images/10390288-8ee75e578f475e03.gif?imageMogr2/auto-orient/strip)

# 原理

分析可发现，该瀑布流每个图片元素宽度相等，高度不同；且从第二行开始，每个元素实际上都是插入到最小高度的那一列上。

![](https://upload-images.jianshu.io/upload_images/10390288-cf8aa0eecb9b0a6f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

emmmm，就这么简单......

## 布局

布局比较简单
容器设置'position: relative;'相对定位， 图片元素设置'position: absolute;'绝对定位

```bash
<div class="waterfall_box">
  <div class="waterfall_item">
  </div>
</div>

.waterfall_box {
  position: relative;
  margin: 0 auto;
  .waterfall_item {
    float: left;
    position: absolute;
    img {
      width: 100%;
      height: auto;
    }
  }
}
```

## 实现

直接上代码比较容易理解
Vue template 结构

```bash
<template>
  <div class="waterfall_box" ref="waterfall_box" :style="`height:${waterfall_box_height_exect}`">
    <template v-if="waterfall_col_num>1">
      <div v-for="(img,index) in waterfall_list" class="waterfall_item" :style="{top:img.top+'px',left:img.left+'px',width:img_width+'px',height:img.height}" :key="index">
        <slot :row="img"></slot>
      </div>
    </template>
    <!-- 列数小于1 没有瀑布流的必要，直接正常布局即可 -->
    <template v-else>
      <div v-for="(img,index) in img_list" :key="index" style="margin-bottom: 20px;">
        <slot :row="img"></slot>
      </div>
    </template>
  </div>
</template>
```

定义变量

```bash

@Prop({ required: true, type: Array })
readonly img_list!: [];

@Prop({ required: true, type: Number }) # 图片宽度
readonly img_width!: number;

@Prop({ required: true, type: Number }) # 图片下边距
readonly img_margin_bottom!: number;

img_margin_right!: number; # 图片右边距

/* 容器宽高 */
waterfall_box_width = 0;

waterfall_box_height = 0;

waterfall_col_num = 0; # 列数

waterfall_col_height_list: Array<number> = []; # 每列最大高度

waterfall_list = []; # 瀑布流用到的数据
```

接下来就是重点,也是直接上代码

1. 初始化一些必要参数

```bash
  # 算出当前视图宽度最多可以放几列，向下取整
  this.waterfall_col_num = Math.floor(this.waterfall_box_width / this.img_width) || 1;
  # 根据列数存储一个临时数组，用于存放当前每一列的最小高度
  this.waterfall_col_height_list = new Array(this.waterfall_col_num).fill(0);
  # 算出每行图片之间的间隙
  this.img_margin_right = (this.waterfall_box_width - this.waterfall_col_num * this.img_width) / (this.waterfall_col_num - 1);
  # 只有一列，很明显没有瀑布流布局的必要了，直接正常布局
  if (this.waterfall_col_num <= 1) return;
  this.layoutWatreFall();
```

2. 瀑布流布局计算

```bash
  # 用一个临时存储，全部计算完再更新Vue数据
  const temp_waterfall_list: any = [];
  # 遍历每个图片数据，动态加一些其他参数。每轮遍历都把该数据加入到最小高度的那一列并设置left和top
  this.img_list.forEach((img_item: any) => {
    const minIndex = this.finMinHeightIndex();
    const img_obj = {
      ...img_item,
      url: img_item.url,
      width: this.img_width,
      height: Math.floor((this.img_width / img_item.width) * img_item.height), # 图片等比例缩放
      top: this.waterfall_col_height_list[minIndex],
      left: minIndex * (this.img_margin_right + this.img_width),
    };
    temp_waterfall_list.push(img_obj);
    this.waterfall_col_height_list[minIndex] += img_obj.height + this.img_margin_bottom; # 每次放完元素，实时更新每个列数的最小高度
  });
  this.waterfall_list = temp_waterfall_list;
  this.waterfall_box_height = Math.max.call(null, ...this.waterfall_col_height_list); # 更新父容器的高度
```

3. 辅助函数

```bash
  # 找到数组最小值下标
  finMinHeightIndex() {
    return this.waterfall_col_height_list.indexOf(Math.min.call(null, ...this.waterfall_col_height_list));
  }
```

# 源码

使用,传一个图片宽度，和下边距即可

```bash
<v-water-fall :img_list="photo_list" :img_width="250" :img_margin_bottom="20" class="photo_img_box">
  <template slot-scope="scope">
    <img v-lazy="scope.row.url"> # 作用域插槽
  </template>
</v-water-fall>
```

[源码](https://github.com/zhongzihao1996/my-blog/blob/dev/blogs/6.%20Vue%20%E6%89%8B%E5%86%99%E5%9B%BE%E7%89%87%E7%80%91%E5%B8%83%E6%B5%81%E7%BB%84%E4%BB%B6(%E9%99%84%E6%BA%90%E7%A0%81)/waterFall.vue)

---

[我的博客](https://github.com/zhongzihao1996/my-blog/tree/master)

---

END
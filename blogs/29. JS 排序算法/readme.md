# 前言

**排序算法是《数据结构与算法》中最基本的算法之一。**

## 原地算法（In-place）

> 不依赖额外的资源或者依赖少数的额外资源，仅依靠输出来覆盖输入，空间复杂度为 𝑂(1) 。

![](https://upload-images.jianshu.io/upload_images/10390288-f5548d9568dd9903.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

时间复杂度：

1. 平方阶 (O(n2)) 排序 各类简单排序：直接插入、直接选择和冒泡排序。

2. 线性对数阶 (O(nlog2n)) 排序 快速排序、堆排序和归并排序；

3. O(n1+§)) 排序，§ 是介于 0 和 1 之间的常数。 希尔排序

4. 线性阶 (O(n)) 排序 基数排序，此外还有桶、箱排序。

稳定性：

1. 稳定的排序算法：冒泡排序、插入排序、归并排序和基数排序。

2. 不是稳定的排序算法：选择排序、快速排序、希尔排序、堆排序。

# 排序算法

**本文所有输出的都是从小到大排序**

测试数据如下：

``` bash 
const data = [];
let len = 20;
while (len > 0) {
  data.push(Math.floor(Math.random() * 100));
  len--;
}
```

## 1. 冒泡排序

### 1.1 算法步骤
- 1. 比较相邻的元素，如果第一个比第二个大，就交换它们两个；
- 2. 依次比较相邻的元素，直到数组元素比较完成，那么这时，最大的元素就应该在数组的最后面；
- 3. 不断重复 1 和 2 的操作

### 1.2 代码实现

``` bash 
const bubbleSort = (list) => {
  const len = list.length;
  let k = len - 1;
  let last_index = 0; // 最后一次交换的位置
  for (let i = 0; i < len - 1; i++) {
    let is_change = false;// 是否有交换
    for (let j = 0; j < k; j++) {
      if (list[j] > list[j + 1]) {
        is_change = true;
        // 交换值
        [list[j], list[j + 1]] = [list[j + 1], list[j]];
        last_index = j;
      }
    }
    k = last_index;
    // 已经排好序了
    if (!is_change) break;
  }
  return list;
}
```

优化点：

- 1. 如果一轮外循环后，没有发生比较，说明已经排好序了，可以直接中止退出
- 2. 记录下最后一次比较值的位置，下一次比较到这里就 OK 了

## 2 选择排序

### 2.1 算法步骤

- 1. 首先在未排序序列中找到最小元素，存放到排序序列的起始位置
- 2. 再从剩余未排序元素中继续寻找最小元素，然后放到已排序序列的末尾
- 3. 重复第二步，直到所有元素均排序完毕

### 2.2 代码实现

``` bash 
const selectSort = (list) => {
  let len = list.length;
  let min = 0;
  for (let i = 0; i < len - 1; i++) {
    min = i;
    for (let j = i + 1; j < len; j++) {
      if (list[min] > list[j]) {
        min = j;
      }
    }
    // 每一轮比较完，交换 i 和 min 位置
    [list[i], list[min]] = [list[min], list[i]];
  }
  return list;
}
```

## 3. 插入排序

### 3.1 算法步骤
- 1. 将第一个元素看成一个有序序列，其他元素看成一个无序序列
- 2. 取出无序序列耳朵第一个元素，与有序序列从尾向前依次比较，若 
- 3. 重复步骤2，直至无序序列全部比较完

### 3.2 代码实现

``` bash 
const insertSort = (list) => {
  let len = list.length
  for (let i = 1; i < len; i++) {
    const temp = list[i];
    let j = i - 1;
    while (j >= 0 && list[j] > temp) {
      list[j + 1] = list[j];
      j--;
    }
    list[j + 1] = temp;
  }
  return list;
}
```

## 4. 希尔排序

> 希尔排序，为了解决插入排序存在如果排序的元素开始排序时，距离它正确的位置很远时，效率极低的痛点，通过增大步伐的灵感来解决。

### 4.1 算法步骤

- 1. 选择一个增量序列 t1，t2，……，tk，其中 ti > tj, tk = 1；

- 2. 按增量序列个数 k，对序列进行 k 趟排序；

- 3. 每趟排序，根据对应的增量 ti，将待排序列分割成若干长度为 m 的子序列，分别对各子表进行直接插入排序。仅增量因子为 1 时，整个序列作为一个表来处理，表长度即为整个序列的长度。

### 4.2 代码实现
``` bash 
const hill_sort = (list) => {
  const len = list.length;
  let gap = 1;
  while (gap < len / 3) {
    gap = gap * 3 + 1;
  }
  while (gap >= 1) {
    for (let i = gap; i < len; i++) {
      const temp = list[i];
      let j = i - gap;
      while (j >= 0 && list[j] > temp) {
        list[j + gap] = list[j];
        j = j - gap;
      }
      list[j + gap] = temp;
    }
    // 重新设置 gap ，向下取整
    gap = Math.floor(gap / 3);
  }
  return list;
}
```

## 5. 归并排序

> 归并排序是建立在归并操作上的一种有效的排序算法，该算法是采用分治法的一个非常典型的应用。将已有序的子序列合并，得到完全有序的序列；即先使每个子序列有序，再使子序列段间有序。若将两个有序表合并成一个有序表，称为二路归并。归并排序是一种稳定的排序方法。

### 5.1 算法步骤

- 1. 创建一个和需要归并的数组相同的新数组，让k指向原来数组的第一个位置，i指向新数组左半部分的第一个元素，j指向右半部分的一个元素。

![](https://upload-images.jianshu.io/upload_images/10390288-20f5769634949edb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 2. 如果i指向的元素ei小于j指向的元素ej，则将ei放入k指向的位置，然后i++指向下一个元素，k++指向下一个需要存放的位置。否则如果ei>ej，则将ej放入k指向的位置，然后j++指向下一个元素，k++指向下一个需要存放的位置。

![](https://upload-images.jianshu.io/upload_images/10390288-56368494a1ac7c51.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 3. 如果左半部分i指向的位置已经超过中间位置，而此时右半部分j还未移动到末尾，那么将j指向位置后面的所有元素都移动到k指向位置的后面，反之类似。

![](https://upload-images.jianshu.io/upload_images/10390288-dc97e1d678fc1616.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

数组[8, 7, 6, 5, 4, 3, 2, 1]进行从小到大归并排序的过程：

![](https://upload-images.jianshu.io/upload_images/10390288-a92d45c558aa5744.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 5.2 代码实现
``` bash 
const merger_sort = (list) => {
  if (list.length <= 1) return list;

  // 基准，切割为左右两半
  const middle_pointer = Math.floor(list.length / 2);
  const left_list = list.slice(0, middle_pointer);
  const right_list = list.slice(middle_pointer, list.length);

  return merge(merger_sort(left_list), merger_sort(right_list));
};

const merge = (left_list, right_list) => {
  // 新数组，长度为左、右序列长度之和
  const result = new Array(left_list.length + right_list.length);
  let k = 0; // 新数组指针

  let i = 0; // 左序列指针
  let j = 0; // 右序列指针
  while (i < left_list.length && j < right_list.length) {
    // 左边比右边大，塞入新数组
    if (left_list[i] <= right_list[j]) {
      result[k++] = left_list[i++];
    } else {
      result[k++] = right_list[j++]
    }
  }
  
  // 若左或者右序列指针还没指到对应的尾部，全部塞入新数组即可
  while (i < left_list.length) {
    result[k++] = left_list[i++];
  }
  while (j < right_list.length) {
    result[k++] = right_list[j++];
  }

  return result;
};
```

## 6. 快速排序

> 快速排序由C. A. R. Hoare在1962年提出。它的基本思想是：通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。

### 6.1 算法步骤

- 1. 从数列中挑出一个元素，称为"基准"（pivot）

- 2. 重新排序数列，所有比基准值小的元素摆放在基准前面，所有比基准值大的元素摆在基准后面（相同的数可以到任何一边）。在这个分区结束之后，该基准就处于数列的中间位置。这个称为分区（partition）操作。

- 3. 递归地（recursively）把小于基准值元素的子数列和大于基准值元素的子数列排序。

![](https://upload-images.jianshu.io/upload_images/10390288-3872725277eba6a8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 6.2 代码实现
``` bash 
const quickSort = (list) => {
  if (list.length <= 1) return list;
  // 基准点
  const basic = list.splice(Math.floor(list.length / 2), 1);
  const left_arr = [];
  const right_arr = [];
  for (let i = 0, len = list.length; i < len; i++) {
    if (list[i] <= basic) {
      left_arr.push(list[i]);
    } else {
      right_arr.push(list[i]);
    }
  }
  return quickSort(left_arr).concat(basic, quickSort(right_arr));
};
```

## 7. 快速排序

> 堆排序(Heapsort)是指利用堆积树（堆）这种数据结构所设计的一种排序算法，它是选择排序的一种。可以利用数组的特点快速定位指定索引的元素。堆分为大根堆和小根堆，是完全二叉树。

- 完全二叉树： 除了最后一层之外的其他每一层都被完全填充，并且所有结点都保持向左对齐。

![](https://upload-images.jianshu.io/upload_images/10390288-99c3539337e64ee4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 满二叉树：除了叶子结点之外的每一个结点都有两个孩子，每一层(当然包含最后一层)都被完全填充。

![](https://upload-images.jianshu.io/upload_images/10390288-6a91cb3a464adedf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 完满二叉树：除了叶子结点之外的每一个结点都有两个孩子结点。

![](https://upload-images.jianshu.io/upload_images/10390288-5cf81553c29b77b1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 二叉堆的性质

性质一：索引为i的左孩子的索引是 (2*i+1);
性质二：索引为i的左孩子的索引是 (2*i+2);
性质三：索引为i的父结点的索引是 floor((i-1)/2);

![](https://upload-images.jianshu.io/upload_images/10390288-7efe014737638b0d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 7.1 算法步骤

- 1. 初始化堆：将数列a[1...n]构造成最大堆

- 2. 交换数据：将a[1]和a[n]交换，使a[n]是a[1...n]中的最大值；然后将a[1...n-1]重新调整为最大堆。 接着，将a[1]和a[n-1]交换，使a[n-1]是a[1...n-1]中的最大值；然后将a[1...n-2]重新调整为最大值。

- 3. 重复2步骤，直到整个数列都是有序的

1. 初始化堆

将数组{20,30,90,40,70,110,60,10,100,50,80}转换为最大堆{110,100,90,40,80,20,60,10,30,50,70}的步骤

i=11/2-1，即 i = 4 开始

1.1 i=4

![](https://upload-images.jianshu.io/upload_images/10390288-1cf3426bfa52accd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上面是maxheap_down(a, 4, 9)调整过程。maxheap_down(a, 4, 9)的作用是将a[4...9]进行下调；a[4]的左孩子是a[9]，右孩子是a[10]。调整时，选择左右孩子中较大的一个(即a[10])和a[4]交换。

1.2 i=3

![](https://upload-images.jianshu.io/upload_images/10390288-0a6fbaa8d71a6fbe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上面是maxheap_down(a, 3, 9)调整过程。maxheap_down(a, 3, 9)的作用是将a[3...9]进行下调；a[3]的左孩子是a[7]，右孩子是a[8]。调整时，选择左右孩子中较大的一个(即a[8])和a[4]交换。

1.3 i=2

![](https://upload-images.jianshu.io/upload_images/10390288-613bea574a662e9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上面是maxheap_down(a, 2, 9)调整过程。maxheap_down(a, 2, 9)的作用是将a[2...9]进行下调；a[2]的左孩子是a[5]，右孩子是a[6]。调整时，选择左右孩子中较大的一个(即a[5])和a[2]交换。

1.4 i=1

![](https://upload-images.jianshu.io/upload_images/10390288-c6fdf139912626cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上面是maxheap_down(a, 1, 9)调整过程。maxheap_down(a, 1, 9)的作用是将a[1...9]进行下调；a[1]的左孩子是a[3]，右孩子是a[4]。调整时，选择左右孩子中较大的一个(即a[3])和a[1]交换。交换之后，a[3]为30，它比它的右孩子a[8]要大，接着，再将它们交换。

1.5 i=0

![](https://upload-images.jianshu.io/upload_images/10390288-8eb7c52e30920eae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上面是maxheap_down(a, 0, 9)调整过程。maxheap_down(a, 0, 9)的作用是将a[0...9]进行下调；a[0]的左孩子是a[1]，右孩子是a[2]。调整时，选择左右孩子中较大的一个(即a[2])和a[0]交换。交换之后，a[2]为20，它比它的左右孩子要大，选择较大的孩子(即左孩子)和a[2]交换。

调整完毕，就得到了最大堆。此时，数组{20,30,90,40,70,110,60,10,100,50,80}也就变成了{110,100,90,40,80,20,60,10,30,50,70}。

2. 交换数据

![](https://upload-images.jianshu.io/upload_images/10390288-17321a720420e06a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上面是当n=10时，交换数据的示意图。

当n=10时，首先交换a[0]和a[10]，使得a[10]是a[0...10]之间的最大值；然后，调整a[0...9]使它称为最大堆。交换之后：a[10]是有序的！

当n=9时， 首先交换a[0]和a[9]，使得a[9]是a[0...9]之间的最大值；然后，调整a[0...8]使它称为最大堆。交换之后：a[9...10]是有序的！

...

依此类推，直到a[0...10]是有序的。


### 7.2 代码实现
``` bash 

```

# 参考

图片均来源于网络

[十大经典排序算法动画与解析，看我就够了！（配代码完全版）](https://mp.weixin.qq.com/s/vn3KiV-ez79FmbZ36SX9lg)

[堆排序](https://www.cnblogs.com/skywang12345/p/3602162.html)

# 源码

[源码](https://github.com/zhongzihao1996/my-blog/tree/master/blogs/29.%20JS%20%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95)

---

END

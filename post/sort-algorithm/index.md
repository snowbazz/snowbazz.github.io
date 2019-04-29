
<!--more-->



# 

## 算法比较

| 算法名称 | 复杂度    | 实现关键                                                     |
| -------- | --------- | ------------------------------------------------------------ |
| 冒泡排序 | O(n^2)    | （无序区，有序区）。从无序区通过交换找出最大元素放到有序区前端。 |
| 选择排序 | O(n^2)    | （有序区，无序区）。在无序区里选择一个最小的元素跟在有序区的后面。 |
| 插入排序 | O(n^2)    | （有序区，无序区）。把无序区的第一个元素插入到有序区的合适的位置。 |
| 希尔排序 | nlog^2(n) | 每一轮按照事先决定的增量进行插入排序，增量会依次缩小，最后一次增量一定要是1。 |
| 快速排序 | nlog(n)   | （小数，分界点，大数）。                                     |
| 归并排序 | O(nlogn)  | 分治法                                                       |

<br/>

## 抽取通用方法



```java
// 交换两个数
public static void swap(int[] arr, int start, int end) {
    int temp = arr[start];
    arr[start] = arr[end];
    arr[end] = temp;
}

// 初始化一个数组
public static int[] initArr() {
    int[] arr = new int[10];
    for (int i =0; i < arr.length; i ++) {
        arr[i] = (int)Math.ceil((Math.random()*100));
    }
    return arr;
}

// 打印数组
public static void printArr(int[] arr) {
    StringBuilder sb = new StringBuilder();
    sb.append("[");
    for (int i = 0; i < arr.length; i ++) {
        if (i != arr.length - 1) {
            sb.append(arr[i] + ",");
        }else {
            sb.append(arr[i] + "]");
        }
    }
    System.out.println(sb);
}
```

<br/>

## 冒泡



![](https://icejam-1259031545.cos.ap-guangzhou.myqcloud.com/sort/C%255CUsers%255Ciceja%255CPictures%255Ctypora%255CBubble_sort_animation.gif)

<br/>

{{% admonition tip tip %}}
每次都把未排序的第一个作为起始点，然后逐渐冒泡上升，之后未排序区越来越少，最终排序完成
{{% /admonition %}}

<br/>

```java
/**
 * Created by icejam.
 * BubbleSort
 */
class Sort {
    
    // Solution 1
    public static void bubbleSort1(int[] arr) {
        for (int i = arr.length - 1; i > 0; i -- ) {	// 每执行完一轮排序，需要排序的个数减1；
            for (int j = 0; j < i; j ++) {	// 临界值的j != i，否则越界
                if (arr[j] > arr[j + 1]) {  // 相邻两个数排序并替换
                    swap(arr, j, j + 1);
                }
            }
        }
    }
    
    // Solution 2
    public static void bubbleSort2(int[] arr) {
        for (int i = 0; i < arr.length - 1; i ++) { // 循环次数为数据个数 -1
            // 比较相邻两个数，若arr[j] > arr[j + 1]，则执行swap()
            // a[j]就像一个气泡一样“浮”到合适位置了
            for (int j = 0; j < arr.length - 1 - i; j ++) {
                if (arr[j] > arr[j + 1]) {
                    swap(arr, j, j + 1);
                }
            }
        }
    }
}

```



<br/>

## 选择排序

![Selection_sort_animation](https://icejam-1259031545.cos.ap-guangzhou.myqcloud.com/sort/C%255CUsers%255Ciceja%255CPictures%255Ctypora%255CSelection_sort_animation.gif)



<br>

{{% admonition tip tip %}}
每一趟从待排序的数据元素中**选出最小（或最大）的一个元素**，顺序放在已排好序的数列的最后，直到全部待排序的数据元素排完。
{{% /admonition %}}

<br>

![](https://icejam-1259031545.cos.ap-guangzhou.myqcloud.com/sort/C%255CUsers%255Ciceja%255CPictures%255Ctypora%255C1555593450174.png)

```java
// SelectSort
public static void selectSort(int[] arr) {
    for (int i = 0; i < arr.length - 1; i++) {  // 循环次数为数据个数 -1
        int minIndex = i;	// 定义最小值下标
        for (int j = i + 1; j <arr.length; j ++) {
            // 找最小值的下标
            if (arr[minIndex] > arr[j]) {
                minIndex = j;
            }
        }
        // 上面循环完，minIndex已经是最小值的下标
        // 交换最小值和第一个位置的数据
        if (i != minIndex) {
            swap(arr, i, minIndex);
        }
    }
}
```



<br/>

## 插入排序



![](https://icejam-1259031545.cos.ap-guangzhou.myqcloud.com/sort/C%255CUsers%255Ciceja%255CPictures%255Ctypora%255CInsertion_sort_animation.gif)

<br>

{{% admonition tip tip %}}
每次插入前，该位置前面的所有数据都是有序的 
{{% /admonition %}}

<br>

```java
// InsertSort
// Solution 1   移动法
public static void insertSort(int[] arr) {
    int temp = 0;
    for (int i = 1; i < arr.length; i++) {
        temp = arr[i];  // 当前数据
        int j = i - 1;  // 往前插入的起始位置
        while (j >= 0 && arr[j] > temp) { // 和当前数据的前面所有数据进行比较
            arr[j + 1] = arr[j];  // 往后移
            j--;
        }
        // 找到插入的位置，把该数据放到插入位置的后面
        arr[j + 1] = temp;
    }
}


// Solution 2   跟Arrays.class  jdk1.8第1345行源码方式一样，交换法
public static void insertSort2(int[] arr) {
    for (int i = 1; i < arr.length; i++) {
        for (int j = i;j > 0;j--) {
            if (arr[j] < arr[j - 1]) {
                swap(arr, j, j - 1);
            }
        }
    }
}
```





<br/>

## 希尔排序



{{% admonition tip tip %}}
对插入排序再加一个步长的循环就是希尔排序了，是插入排序的高级版，也称为缩小增量排序。

希尔排序是把记录按下标的一定增量分组，对每组使用直接插入排序算法排序；随着增量逐渐减少，每组包含的关键词越来越多，当增量减至1时，整个文件恰被分成一组，算法便终止。

{{% /admonition %}}

<br>

例如

```
[ 13 14 94 33 82 25 59 94 65 23 45 27 73 25 39 10 ]
```



按照5步长排序，则相当于按列先进行排序(实际是通过下标实现的)

```
13 14 94 33 82
25 59 94 65 23
45 27 73 25 39
10
```



排序后结果为

```
10 14 73 25 23
13 27 94 33 39
25 59 94 65 82
45
```



多次循环后，只需要**最终步长为1**即可。



```java
// Solution1  插入时采用交换法
public static void shellSort1(int[] arr) {
    //增量gap，并逐步缩小增量
    for (int gap = arr.length / 2; gap > 0; gap /= 2) {
        //从第gap个元素，逐个对其所在组进行直接插入排序操作
        for (int i = gap; i < arr.length; i++) {
            int j = i;
            while (j - gap >= 0 && arr[j] < arr[j - gap]) {
                //插入排序采用交换法
                swap(arr, j, j - gap);
                j -= gap;
            }
        }
    }
}

// Solution2  插入时采用移动法
public static void shellSort2(int[] arr) {
    //增量gap，并逐步缩小增量
    for (int gap = arr.length / 2; gap > 0; gap /= 2) {
        //从第gap个元素，逐个对其所在组进行直接插入排序操作
        for (int i = gap; i < arr.length; i++) {
            int j = i;
            int temp = arr[j];
            if (arr[j] < arr[j - gap]) {
                while (j - gap >= 0 && temp < arr[j - gap]) {
                    //移动法
                    arr[j] = arr[j - gap];
                    j -= gap;
                }
                arr[j] = temp;
            }
        }
    }
}
```



<br/>



## 快速排序



![quicksort](https://icejam-1259031545.cos.ap-guangzhou.myqcloud.com/sort/quicksort.gif)



<br>

{{% admonition tip tip %}}
每次迭代都选出一个基准，左边放小的，右边放大的，最终迭代完成。
{{% /admonition %}}

<br>

```java
// QuickSort
private static void quickSort(int[] arr, int low, int high) {

    if (low < high) {
        // 找寻基准数据的正确索引
        int index = getIndex(arr, low, high);

        // 进行迭代对index之前和之后的数组进行相同的操作使整个数组变成有序
        quickSort2(arr, 0, index - 1);
        quickSort2(arr, index + 1, high);
    }

}
private static int getIndex(int[] arr, int low, int high) {
    // 基准数据
    int tmp = arr[low];
    while (low < high) {
        // 当队尾的元素大于等于基准数据时,向前挪动high指针
        while (low < high && arr[high] >= tmp) {
            high--;
        }
        // 如果队尾元素小于tmp了,需要将其赋值给low
        arr[low] = arr[high];
        // 当队首元素小于等于tmp时,向前挪动low指针
        while (low < high && arr[low] <= tmp) {
            low++;
        }
        // 当队首元素大于tmp时,需要将其赋值给high
        arr[high] = arr[low];

    }
    // 跳出循环时low和high相等,此时的low或high就是tmp的正确索引位置
    // 由原理部分可以很清楚的知道low位置的值并不是tmp,所以需要将tmp赋值给arr[low]
    arr[low] = tmp;
    return low; // 返回tmp的正确位置
}
```

<br>



## 归并排序

```java
// -----------------------------------------Merge Sort----------------------------------------
private static void mergeSort(int[] arr) {
    int[] temp = new int[arr.length];   // 在排序前，先建好一个长度等于原数组长度的临时数组，避免递归中频繁开辟空间
    mergeSort(arr, 0, arr.length - 1, temp);
}
private static void mergeSort(int[]arr, int left, int right, int[] temp) {
    if (left < right) {
        int mid = (left + right) / 2;
        mergeSort(arr, left, mid, temp);
        mergeSort(arr, mid + 1, right, temp);   // 右边归并排序，使得右子序列有序
        merge(arr, left, mid, right, temp);     // 将两个有序子数组合并操作
    }
}

private static void merge(int[] arr, int left, int mid, int right, int[] temp) {
    int i = left;   // 左序列指针
    int j = mid + 1;// 右序列指针
    int t = 0;      // 临时数组指针
    while (i <= mid && j <= right) {
        if (arr[i] <= arr[j]) {
            temp[t++] = arr[i++];
        }else {
            temp[t++] = arr[j++];
        }
    }
    while (i <= mid) {  //将左边剩余元素填充进temp中
        temp[t++] = arr[i++];
    }
    while (j <= right) {    //将右序列剩余元素填充进temp中
        temp[t++] = arr[j++];
    }
    t = 0;
    // 将temp中的元素全部拷贝到原数组中
    while (left <= right) {
        arr[left++] = temp[t++];
    }
}
```

<br>

## 参考资料

[图解排序算法(二)之希尔排序](https://www.cnblogs.com/chengxiao/p/6104371.html)

[快速排序](<https://blog.csdn.net/nrsc272420199/article/details/82587933>)

[图解排序算法(四)之归并排序](https://www.cnblogs.com/chengxiao/p/6194356.html)

全部代码在这	：<https://github.com/snowbazz/blog-code/tree/master/Sort>

<br>
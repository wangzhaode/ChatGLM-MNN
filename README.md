# ChatGLM-MNN
## Describe
该模型使用ChatGLM-6B, 将其转换到MNN模型并使用C++进行推理。在实现上做了如下优化：

1. 对其中的词表做了部分删减，删除了模型中未使用的前20000个词；将vocab, embedding, lm_head的大小从130528缩小到130528;
2. `Embedding`操作调用次数较少，使用`fseek`, `fread`加载的方式降低内存;
3. `lm_head`操作为`[num, 4096] @ [4096, 130528]`，将其优化为分段实现的矩阵乘`[130528, 4096] @ [4096, 1]`;
2. 原模型对显存要求较高；将模型按层拆分成28个模型，可以根据用户显存大小动态将计算任务分配给GPU和CPU，充分利用显卡算力;

## Usage
### 1. Compile MNN library
```bash
git clone https://github.com/alibaba/MNN.git
cd MNN
mkdir build
cmake ..
make -j8
cp -r include /path/to/ChatGLM-MNN/
cp libMNN.so /path/to/ChatGLM-MNN/libs
```
### 2. Download Models
Download models from `github release` to `/path/to/ChatGLM-MNN/resource/models`

### 2. Build and Run
```bash
mkdir build
cd build
cmake ..
make -j8
./demo
```

## Example

Q: 用Python帮我写一段快速排序代码

A: 当然可以!以下是使用Python实现快速排序算法的示例代码:

```python
def quick_sort(arr):
    if len(arr) <= 1:
        return arr
    else:
        pivot = arr[0]
        left = []
        right = []
        for i in range(1, len(arr)):
            if arr[i] < pivot:
                left.append(arr[i])
            else:
                right.append(arr[i])
        return quick_sort(left) + [pivot] + quick_sort(right)
```

这个实现中,我们首先检查传入的数组长度是否小于等于1。如果是,我们直接返回该数组,因为这种情况下快速排序是不稳定的。否则,我们选择数组的第一个元素作为基准点(pivot),并将数组分为两个部分:左边的部分的所有元素都小于基准点,右边的部分的所有元素都大于基准点。然后我们递归地对左边和右边的部分执行快速排序,并将结果拼接起来。最后,我们递归地对左右两个部分执行相同的操作,并返回结果。

你可以像这样调用这个函数:

```python
arr = [3, 6, 1, 9, 2, 5, 8, 4]
sorted_arr = quick_sort(arr)
print(sorted_arr)
```

这将输出 `[1, 2, 3, 4, 5, 6, 7, 8, 9]`,表示快速排序已成功地对数组进行了排序。

---

Q: 内存泄露如何定位

A: 内存泄漏(Memory Leak)是指程序在运行过程中,占用的内存空间不断增加,最终导致程序无法继续运行,或者运行效率降低。

以下是一些常见的内存泄漏检查方法:

1. 使用内存分析工具:例如Eclipse Memory Analyzer、VisualVM等,这些工具可以帮助检测程序中未释放的内存空间,以及内存分配的异常。

2. 检查变量的生命周期:如果一个变量在它的生命周期内没有被释放,那么它的内存空间就会被一直占用,最终导致内存泄漏。可以通过查看变量的生命周期信息来检查是否存在这个问题。

3. 检查代码中的异常:如果程序在执行过程中出现了异常,而没有释放所占用的内存空间,也会导致内存泄漏。可以通过调试程序,查找并释放这些内存空间。

4. 优化代码:如果程序中的代码存在内存泄漏问题,那么可能是因为程序的性能问题或者内存占用过高导致的。可以通过优化代码,减少内存分配和释放的次数,来解决这个问题。

5. 定期清理无用的数据:如果程序中存在大量的无用数据,也会导致内存泄漏。可以通过定期清理这些数据,释放内存空间。

内存泄漏问题的解决需要从多个方面入手,通过不断地调试和优化程序,来找到内存泄漏的根本原因,并有效地解决问题。

---
Q: 北京有什么特色小吃？

A: 北京是中国历史文化名城,也是中国美食之都之一,有许多特色小吃。以下是一些著名的北京特色小吃:

1. 炸酱面:炸酱面是中国传统面食之一,以黄酱和肉末为主要材料,配以豆瓣酱、黄瓜丝和豆芽等配料,味道鲜美。

2. 烤鸭:烤鸭是北京最著名的美食之一,以薄饼和鸭肉为主要材料,烤制过程中还会加入葱、姜等调料,口感鲜美。

3. 豆汁:豆汁是一种传统的北京小吃,以黄豆为主要原料,配以辣椒油、醋、蒜泥等调料,味道酸甜可口。

4. 羊蝎子:羊蝎子是一道以羊肉和羊肝为主要材料的炖菜,口感鲜美,营养丰富。

5. 糖葫芦:糖葫芦是一种传统的北京小吃,以草莓、山楂等水果为主料,沾上糖浆,口感酸甜可口。

6. 煎饼果子:煎饼果子是一种流行的中式早餐,以薄饼和蛋、肉松、油条等为主要材料,口感酥脆。

7. 驴打滚:驴打滚是一种传统的北京小吃,以糯米粉和豆沙为主要材料,通过卷起来和炸的方式制作,口感香甜。

这只是北京众多特色小吃中的一小部分,北京还有很多其他美食,如北京火锅、北京炸酱面、北京小吃街等等,值得一试。
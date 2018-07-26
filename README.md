## 算法
### 排序算法
1. 冒泡排序
##### 冒泡排序算法通过多次比较和交换来实现排序，每次通过比较相邻两个元素，不断交换，每一次都会将未排序元素中的最大值交换至末尾。
    public void BubbleSort(int[] a){
        for(int i=0;i<a.length-1;i++){   //外层循环控制循环次数，为 数组元素个数-1 次
            for(int j=0;j<a.length-i-1;j++){   //内层循环控制每一次的比较次数
                if(a[j] > a[j+1]){
                    int temp = a[j];
                    a[j] = a[j+1];
                    a[j+1] = temp;
                }
            }
        }
    }
2. 选择排序
##### 选择排序算法通过选择和交换来实现排序，每次从未排序元素中选择最小的元素，放在已排序元素的末尾，直至排序完毕。
    public void SelectSort(int[] a){
        for(int i=0;i<a.length-1;i++){  //外层循环控制循环次数
            int min = i;
            for(int j=i+1;j<a.length;j++){   //内层循环选择出未排序元素中最小元素的序号
                if(a[j] < min){
                    min = j;
                }
            }
            if(min != i){   //如果最小元素不是未排序元素的第一个，则进行交换。如果最小元素是第一个，则没有交换的必要，直接进入下一次循环
                int temp = a[i];
                a[i] = a[min];
                a[min] = temp;
            }
        }
    }
3. 插入排序
##### 插入排序算法通过比较和插入来实现排序，将未排序元素逐个插入至合适的位置来完成排序工作。
    public void InsertionSort(int[] a){
        for(int i=1;i<a.length;i++){  //外层循环控制循环次数，从第二个元素开始循环
            int temp = a[i];
            int j = i-1;
            while(j>=0 && a[j]> temp){   //通过循环，不断后移
                a[j+1] = a[j];
                j--;
            }
            a[j+1] = temp;   //放到合适的位置
        }
    }
4. Shell排序
##### Shell排序算法基于插入排序的思想，又称为希尔排序或缩小增量排序。
    public void ShellSort(int[] a){
        for(int h = a.length/2;h>=1;h/=2){  //划组排序,有多种划法
            for(int i=h;i<a.length;i++){   /后面和插入排序类似，只是步长为h
                int temp = a[i];
                int j = i-h;
                while(j>=0 && temp < a[j]){
                    a[j+r] = a[j];
                    j -= r;
                }
                a[j+r] = temp;
            }
        }
    }
4. 快速排序
##### 快速排序与冒泡排序类似，都是基于交换排序的思想。
    public void QuickSort(int[] a, int left, int right){
        if(left > right){
            return;
        }
        int pivot = a[left];   //选取最左边的元素为基准值
        int i = left;
        int j = right;
        while(i<j){
            while(pivot<=a[j] && i<j){   //从右到左，找到比基准小的数
                j--;
            }
            while(pivot>=a[i] && i<j>){   //从左到右
                i++;
            }
            if(i<j){
                int temp = a[i];
                a[i] = a[j];
                a[j] = temp;
            }
            a[left] = a[i];
            a[i] = pivot;
            QuickSort(a,left,i-1);
            QuickSort(a,i+1,right);
        }

    }
6. 堆排序
##### 堆排序需要经过反复两个步骤：构造堆和堆排序输出。
    public void HeapSort(int[] a){
        //先构造大根堆
        for(int i=a.length/2-1;i>=0;i--){   //从第一个非叶子节点从下至上，从右到左调整
            adjustHeap(a,i,a.length)
        }
        for(int i=a.length-1;i>0;i--){
            int temp = a[0];    //先将堆顶元素与末尾元素进行交换
            a[0] = a[i];
            a[i] = temp;
            adjustHeap(a,0,i);   //再重新对堆进行调整
        }
    }

    public void adjustHeap(int[] a, int i, int length){
        int temp = a[i];
        for(int j = i*2+1;j<length;k=k*2+1){
            if(k+1 < length && a[k] < a[k+1]){
                k++;
            }
            if(a[k] >temp){
                a[i] = a[k];
                i = k;
            }else{
                break;
            }
        }
        a[i] = temp;
    }
7. 归并排序
##### 将多个有序数据表合并成一个有序数据表。
    public void MergeSort(int[] a, int left, int right){
        if(left >= right){
            return;
        }
        int mid = (left+right)/2;
        MergeSort(a,left,mid);
        MergeSort(a,mid+1,right);
        merge(a,left,mid,right);
    }

    public void merge(int[] a, int left, int mid, int right){
        int[] tmp = new int[a.length];
        int r1 = mid + 1;
        int tIndex = left;
        int cIndex=left;
        // 逐个归并
        while(left <=mid && r1 <= right) {
            if (a[left] <= a[r1]) 
                tmp[tIndex++] = a[left++];
            else
                tmp[tIndex++] = a[r1++];
        }
        // 将左边剩余的归并
        while (left <=mid) {
            tmp[tIndex++] = a[left++];
        }
        // 将右边剩余的归并
        while ( r1 <= right ) {
            tmp[tIndex++] = a[r1++];
        }

        //从临时数组拷贝到原数组
        while(cIndex<=right){
        a[cIndex]=tmp[cIndex];
        cIndex++;
        }
    }
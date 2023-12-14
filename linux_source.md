函数的入参是数组，怎么可以在提供接口时，让调用者感知不到数组的大小

问题代码描述：

```c
//1.c 接口定义
void f1(int *data, int dataSize);
//1.h 接口声明
#define DATA_SIZE 10
void f1(int *data, int dataSize);

//2.c 调用接口
void f2()
{
    int data[DATA_SIZE];//问题在这里，怎么样让调用者不需要担心自己定义的数组大小呢？
    f1(&data, DATA_SIZE);
}
```



方法一：利用结构体

```c
//1.c 接口定义
void f1(DataType *data);
//1.h 接口声明
#define DATA_SIZE 10
typedef struct {
    int data[DATA_SIZE];
}DataType;
void f1(DataType *data);

//2.c 调用接口
void f2()
{
		DataType data;
		f1(&data); //这样的话调用者就不需要知道数组的大小，这样做好吗？
}
```

方法二：有没有更好的办法呢？
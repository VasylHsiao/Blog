# 流程控制

## break与continue

### 通常概念

在任何迭代语句的主体部分，都可以使用 **break** 和 **continue** 来控制循环的流程。 

**break** 退出break所在的本层循环，不再执行本层循环；

**continue** 跳过continue所在的本次循环的其余部分，然后回到本层循环起始处继续下一次执行。

### 带标签的break与continue

尽管 goto 仍是 Java 的一个保留字，但并未在语言中得到正式使用。java可以在使用break、continue时利用标签`：`实现跳转。

```java
public static void study() {
	      // 标签1
        label_outer:
        for (int i = 0; i < 10; i++) {
            // 标签2
            label_inner:
            for (int j = 0; j < 10; j++) {
                if (i == 4 && j == 3) {
                    continue label_outer;
                }

                if (i == 5 && j == 4) {
                    break label_outer;
                }
                System.out.println(i + " ; " + j);
            }
        }
}
```

1. 对 Java 来说，唯一用到标签的地方是在循环语句之前；
2. 只能跳出语句块，不能跳入语句块；
3. 如上述代码，在多层循环中，
    
    不加标签的continue（或break），会默认跳转至（或退出）它们所在的本层循环，就是标签2（label_inner）所指示的内层循环，与`continue/break label_inner；`作用相同；
    
    添加标签1（label_outer）后，会跳转至（或退出）标签1所指示的外层循环，continue会从外层循环继续执行。

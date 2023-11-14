## iBOT论文阅读

文章标题：$iBOT:Image\ Bert\ pre-training\ with\ Online\ Tokenizer$，可以理解为使用在线tokenizer进行图像BERT式预训练



### 写在前面：

iBOT可以视为BEIT和DINO的结合，在IBOT中学生网络和教师网络的输出会有两部分，一部分是和DINO中类似的$h^{[CLS]}$ 一部分是和BEIT中类似的$h^{patch}$



### iBOT的模型结构图和算法流程：

![image-20231114181728201](C:\Users\Shipu\AppData\Roaming\Typora\typora-user-images\image-20231114181728201.png)

![image-20231114181818531](C:\Users\Shipu\AppData\Roaming\Typora\typora-user-images\image-20231114181818531.png)



算法流程：

1. 与DINO类似，一开始学生网络和教师网络的参数是一致的

2. 与DINO类似，每个图像$x$，会经过两种不同的增广方式得到$u$和$v$

3. 与DINO不同的是，进入学生网络的图像$\hat{u}$和$\hat{v}$是$u$和$v$被mask掉的一部分后的图像

4. **在iBOT中学生网络和教师网络的输出会有两部分**：

   * 第一部分与DINO类似（暂时理解为图像的全局特征，对图像整体的编码），学生网络输出$\hat{u}_s^{[CLS]}$和$\hat{v}_s^{[CLS]}$

     教师网络输出$u_t^{[CLS]}$和$v_t^{[CLS]}$

   * 第二部分与BEIT类似（被mask掉的patch特征），学生网络输出$\hat{u}_s^{patch}$和$\hat{v}_s^{patch}$

     教师网络输出$u_t^{patch}$和$v_t^{patch}$

     

     第一部分的损失函数：![image-20231114192735633](C:\Users\Shipu\AppData\Roaming\Typora\typora-user-images\image-20231114192735633.png)

     

     和DINO文章中的损失函数是一致的：

     ![image-20231114193023775](C:\Users\Shipu\AppData\Roaming\Typora\typora-user-images\image-20231114193023775.png)

     

     $H$表示交叉熵损失函数，更准确的说是带有温度参数（锐度）和偏置量（居中)的交叉熵损失函数。位置和居中的平衡避免了模型在训练过程中崩溃。

     

     第二部分的损失函数：

     ![image-20231114193859918](C:\Users\Shipu\AppData\Roaming\Typora\typora-user-images\image-20231114193859918.png)

     计算被mask掉patch的特征相似度（$m_u$和$m_v$的参数含义可能是被mask掉的比例？这里需要再了解一下）

     

     总体损失函数：

     $L_{[CLS]}.mean()+L_{MIM}.mean()$

5. 参数更新：

   * 学生网络的参数更新是根据损失函数反向传播结果优化的

   * 教师网络参数更新与DINO类似，采用动量法，结合自己本来的参数以及学生网络的参数

   * 同样的，偏置量$C$和$C^{'}$也是采用动量法更新

     采用动量法更新的公式如下图所示：

     ![image-20231114194902437](C:\Users\Shipu\AppData\Roaming\Typora\typora-user-images\image-20231114194902437.png)



 

​                                                                                                                                                                                          初读于2023.11.14














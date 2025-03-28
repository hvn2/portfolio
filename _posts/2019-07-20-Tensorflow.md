---
layout: default
title: CƠ BẢN VỀ TENSORFLOW
---
#### <span style="color:red">Update:</span> Bài viết này phù hợp với TF 1.xx; gần đây TF 2.xx đã có nhiều đổi mới giúp việc khai báo cú pháp đơn giản hơn rất nhiều. TF 2.xx có cú pháp và cách hoạt động rất giống với Pytorch, một DL framework rất phổ biến khác trong DL và sẽ được giơi thiệu ở các bài viết sau.
Tensorflow (TF) là một thư viện mã nguồn mở phát triển bởi Google hoạt động trên nền tảng nhiều ngôn ngữ lập trình, đặc biệt thích hợp cho Machine Learning (ML) và Deep Learning (DL). Trong các ứng dụng ML/DL chủ yếu sử dụng TF với ngôn ngữ Python. TF cung cấp nhiều lớp, module và hàm ở những mức độ trừu tượng khác nhau hỗ trợ cho ML/DL, do vậy phát triển ML/DL trên TF sẽ rất nhanh chóng.

Trong phần đầu của bài viết này sẽ trình bày một số khái niệm cơ bản của TF ở mức độ trừu tượng thấp (Low level API). Để chạy một chương trình (tính toán) trong TF cần phải xây dựng một tf.Graph (luồng dữ liệu) thực hiện tf.Session (chạy luồng dữ liệu đó). Hai khái niệm này giúp cho TF có thể thực hiện một cách nhanh chóng thông qua tính toán phân tán (thực ra cũng chẳng cần quan tâm làm gì).
## tf.Graph()
TF định nghĩa một sơ đồ luồng dữ liệu (tf.Graph) là một dãy các toán tử TF (Tensorflow operation) sắp xếp thành một sơ đồ (graph). Mỗi sơ đồ gồm có 2 đối tượng:
- tf.Operation (ops): Thể hiện bằng các node của sơ đồ, Operation thông thường là các phép toán trả về kết quả là tensor.
- tf.Tensor: Là các cạnh (edge) của sơ đồ, thể hiện giá trị sẽ chạy qua sơ đồ dữ liệu. Tensor dịch ra tiếng Việt là khối dữ liệu (nhiều chiều). Trong TF thì số chiều của tensor gọi là rank.
```
    3 --> a rank 0 tensor; a scalar with shape [],
    [1., 2., 3.] --> a rank 1 tensor; a vector with shape [3]
    [[1., 2., 3.], [4., 5., 6.]] --> a rank 2 tensor; a matrix with shape [2, 3]
```
    **Quy luật:** Đếm dấu ngoặc vuông suy ra rank, ví dụ [[[1., 2., 3.]], [[7., 8., 9.]]] có 3 dấu ngoặc vuông $\Rightarrow$ rank(3). 

    Đếm số phần tử trong từng dấu ngoặc vuông suy ra số phần tử trong chiều (shape). Ví dụ [[[1., 2., 3.]], [[7., 8., 9.]]], dấu ngoặc vuông đầu tiên có 2 phần tử, dấu ngoặc thứ 2 có 1 phần tử, dấu ngoặc thứ 3 có 3 phần tử $\Rightarrow$ shape (2,1,3)

**Ví dụ:** Graph như hình 1 trong TF sẽ thực hiện bằng các câu lệnh sau:

```python
a = tf.constant(2, name='a') # Kiểu dữ liệu tự động suy ra từ giá trị constant
b = tf.constant(3, name = 'b')
c = tf.constant(4, name ='c')
x = tf.add(a, b)
y = tf.multiply(x,c)
print(a)
print(x)
print(y)
```

<div class="imgcap">
 <img src ="/images/bai-03/tfgraph.PNG" align = "center">
 <div class = "thecap">Hình 1. tf.grahp()</div>
</div>

Trên graph ở Hình 1 thì các node là Operation (x: Add, y: Multiply, cả a, b,c: Constant nữa), còn cạnh là tensor (nhìn kỹ có chữ scalar). Khi chạy chương trình trên chỉ in ra tên, số chiều (shape) và loại (dtype) dữ liệu của các tensor mà chưa cho ra giá trị của phép tính.
```python
Tensor("a_7:0", shape=(), dtype=int32)
Tensor("Add_7:0", shape=(), dtype=int32)
Tensor("Mul_7:0", shape=(), dtype=int32)
```
 Muốn thực thi chương trình trên thì cần phải tạo một runtime cho nó
## tf.Session()
Có hai cách tạo Session (chọn 1 trong 2 cách). Có thể hiểu ```tf.Gphaph()``` giống như là tạo ra một file mã nguồn (```.py```) và ```tf.Session()``` là chạy mã nguồn đó.

```python
sess = tf.Session()
print(sess.run(a))
print(sess.run(x))
print(sess.run(y))
sess.close()

'''Cách này tự động close session'''
with tf.Session() as sess:
    print(sess.run(a))
    print(sess.run(x))
    print(sess.run(y))
```

Sẽ cho ra kết quả:

```python
2
5
20
```
## Placeholder và Variable
Khi muốn thay đổi các giá trị truyền vào graph, thay vì cứ sủ dụng một giá trị không đổi (constant) như ở trên thì sử dụng ```tf.placehoder```; khi truyền giá trị vào placeholder thì dùng phương thức ```feed_dict```. Placeholder thường dùng để chứa train/validation/test data. Ví dụ:

```python
x = tf.placeholder(tf.float32) # Có thể khởi tạo name, dtype
y = tf.placeholder(tf.float32)
z = x + y
with tf.Session() as sess:
    print(sess.run(z, feed_dict={x: 3, y: 4.5}))
    print(sess.run(z, feed_dict={x: [1, 3], y: [2, 4]}))
```

Khi muốn lưu trữ một biến (một giá trị tạm thời) mà giá trị của nó có thể thay đổi khi chạy chương trình thì sử dụng ```tf.Variable``` (class có nhiều operation hơn function) hoặc ```tf.get_variable``` (function). Variable thường dùng để chứa Weights cho model. Ví dụ:
```python
#tf.Variable is a class with many operations
sca = tf.Variable(2.0, name="scalar") # gia tri khoi tao 2, ten 'scalar', shape, type tu dong sinh ra tu ininitial value
m=tf.Variable([[1,2],[3,4]], name='matrix')
print(sca)
print(m)
#tf.get_variable is a function
sca1 = tf.get_variable("scalar1",initializer = 3.)
m1 = tf.get_variable("matrix1", initializer=tf.constant([[0, 1], [2, 3]]))
print(sca1)
print(m1)
```

Kết quả:

```python
<tf.Variable 'scalar:0' shape=() dtype=float32_ref>
<tf.Variable 'matrix:0' shape=(2, 2) dtype=int32_ref>
<tf.Variable 'scalar1:0' shape=() dtype=float32_ref>
<tf.Variable 'matrix1:0' shape=(2, 2) dtype=int32_ref>
```

Để sử dụng Varible phải khởi tạo cho nó. Ví dụ:

```python
m= 2*m
m1 = 2+m1
init = tf.global_variables_initializer()
with tf.Session() as sess:
    sess.run(init)
#   sess.run(tf.global_variables_initializer()) #bat buoc phai co cau lenh khoi tao nay
    print("Mỗi lần chạy là giá trị của m được nhân 2:",sess.run(m))
    print("Mỗi lần chạy là giá trị của m1 được cộng 2:", sess.run(m1))
```

Kết quả:

```python
Mỗi lần chạy là giá trị của m được nhân 2: [[ 64 128], [192 256]]
Mỗi lần chạy là giá trị của m1 được cộng 2: [[10 11], [12 13]]
```

Đây là những khái niệm cơ bản nhất của TF, ngoài ra TF còn hỗ trợ nhiều lớp, đối tượng, hàm,...ở mức trừu tượng cao hơn. Trong hướng dẫn sử dụng của TF khuyên nên sử dụng high level API mỗi khi có thể, chỉ sử dụng low level API khi high level API không thể giải quyết được. Một trong những high level API được sử dụng rộng rãi của TF là [Keras](https://keras.io). Hiện nay Keras đã là một phần của TF, do vậy có thể sử dụng cả low level API và high level API trong TF hoàn toàn được (sẽ có các ví dụ sau).

Phần tiếp theo sẽ trình bày 2 ví dụ cơ bản của ML với TL: Linear Regression và Logistic Regression.
## Linear Regression với Tensorflow
Giới thiệu sơ lược về **Machine Learning**, trong nhiều giáo trình hoặc trên mạng Internet có thể dễ dàng tìm được định nghĩa ML là gì. Ở đây chấp nhận một định nghĩa của Wikipedia như sau *"Machine learning is the subfield of computer science that gives computers the ability to learn without being explicitly programmed"*. Dịch ra thành "ML là một ngành hẹp của khoa học máy tính cho phép máy tính có thể học (từ dữ liệu) mà không cần được lập trình cụ thể". Người ta có nhiều cách phân loại, cách phân loại rộng nhất là chia thành Supervised learning, unsupervised learning và reinforcement learning, Deep learning là một lĩnh vực hẹp của ML sử dụng kiến trúc mạng neuron nhiều lớp. Để làm rõ định nghĩa ở trên xét ví dụ sau:

**Ví dụ:** Cho bảng số liệu chiều cao (x-kg) và cân nặng (y-cm):

```
x = [147, 150, 153, 158, 163, 165, 168, 170, 173, 175, 178, 180, 183]
y = [49, 50, 51,  54, 58, 59, 60, 62, 63, 64, 66, 67, 68]
```

Đồ thị của nó như hình 2

<div class="imgcap">
 <img src ="/images/bai-03/linreg.png" align = "center">
 <div class = "thecap">Hình 2. Đồ thị liên hệ x và y</div>
</div>

Trước khi nói tới việc máy học thì người học ra sao? Dừng lại một chút, nếu bạn nhìn vào bảng số và đồ thị trên bạn sẽ "học" được điều gì? Đối với một người học hết cấp 2, dễ dàng đoán được đó là mối quan hệ tuyến tính giữa cân nặng và chiều cao $y=ax+b$. Chỉ cần biết $a, b$ và bây giờ nếu có một giá trị chiều cao $x$ mới sẽ suy ra được cân nặng $y$ tương ứng. Tương tự như vậy, nếu đưa cho máy tính bảng số liệu trên rồi bằng cách nào đó máy tính cho ta biết dược giá trị của $a, b$ thì đó chính là học máy. Nếu so sánh với phương pháp không phải là ML thì chúng ta phải lập trình cụ thể, đó là con người tìm ra các con số $a, b$ trước, sau đó lập trình hàm số $y=ax+b$, dựa vào đây máy cho đầu ra $y$ tương ứng với $x$. Đây là một ví dụ đơn giản, gọi là hồi quy tuyến tính (Linear regression), có thể coi bài toán Hello World của ML. Thực tế ML phức tạp hơn nhiều, ví dụ đầu vào máy là bức ảnh, đầu ra máy phải cho biết trong bức ảnh đó con mèo hay không? đầu ra trong bức ảnh đó có mấy cái oto, nằm ở vị trí nào? đầu ra là một đoạn text mô tả nội dung bức ảnh đó.... Bất cứ từ dữ liệu (con số, hình ảnh, tiếng nói, video,...) mà con người nhìn vào đó có thể rút ra được thông tin gì mà máy cũng làm được điều tương tự thì đó có thể coi là ML. Làm thế nào máy có thể làm được điều kì diệu đó? Nó phải học. Vậy con người học như thế nào? Đó là phải được dạy lý thuyết, được thực hành qua các ví dụ, được kiểm tra, đánh giá (học chưa được thì phải học lại). Như ví dụ trên chẳng hạn, đầu tiên phải chỉ ra được đây là quy luật tuyến tính (lý thuyết), đưa các dữ liệu $x, y$ vào để tìm $a, b$ (thực hành) và phải có một tiêu chuẩn nào đó để kiểm tra xem $a, b$ tìm được đã tốt chưa? Máy học (ML) cũng như vậy.

Quay trở lại ví dụ Linear Regression ở trên. Theo ngôn ngữ của ML thì hàm số tuyến tính bậc nhất $y=ax+b$ là mô hình (model), $a, b$ là các tham số của mô hình (parametes hoặc weights). Các cặp dữ liệu (x,y) là các lữ liệu mẫu (sample/example), dữ liệu mẫu chỉ dùng cho việc học (train). Thực chất việc học (train, learn) là tìm ra các tham số $a$ và $b$ tối ưu, nghĩa là sau khi tìm được $a$ và $b$ thì giá trị $y=ax+b$ phải xấp xỉ gần đúng nhất với $y$ từ các training samples. Quá trình học ấy diễn ra như sau: ban đầu khởi tạo $a$ và $b$ là các giá trị ngẫu nhiên, sau đó tính ra $y_{hat}=ax+b$ ứng với một giá trị $x$ trong training samples. Tiếp theo tối thiểu hóa sai lệch giữa $y_{hat}$ và $y$ trong training set, ví dụ ${\| y-y_{hat} \|_2}^2$, nói cách khác tối thiểu hóa sai số giữa $y_{hat}$ và $y$ theo biến $a$ và $b$. Sau một số vòng lặp sẽ thu được giá trị $a$ và $b$ tối ưu. Thuật toán thực hiện quá trình tối ưu này thường là back propagation (lan truyền ngược), ví dụ Gradient Descent.

Traning là quá trình tối thiểu hóa sai lệch giữa đầu ra mô hình ($y_{hat}$) và nhãn ($y$) của training sample, vậy sai lệch này nhỏ đến đâu là đủ? Có thể dặt một ngưỡng $\epsilon$ đủ nhỏ nào đó; khi sai lệch dưới ngưỡng này thì dừng quá trình train. Đó là một ý tưởng tốt nhưng không phải lúc nào cũng đúng, làm như vậy chỉ chứng minh được rằng mô hình hoạt động tốt trên training samples-đây có thể coi là điều kiện cần. Khi đưa mô hình đã huấn luyện ra làm việc với dữ liệu mới (testing samples), do tính ngẫu nhiên của dữ liệu-có thể hình dung là training samples chỉ là một phần của phân bố dữ liệu gồm vô hạn các điểm dữ liệu-dữ liệu mới có thể phân bố lệch một chút so với dữ liệu huấn luyện. Nên mô hình có thể làm việc không hiệu quả với dự liệu mới đó. Trong ML/DL điều này gọi là overfitting (quá khít) và là điều không mong muốn. Nếu so sánh với quá trình học của con người thì đây giống như việc học tủ, học gạo; chỉ hiểu những gì có trong đề cương có sẵn. Để đề phòng overfitting cần có một bước nữa là evaluation, thông thường nếu training samples đủ lớn sẽ chia ra theo tỉ lệ 80-20 (hoặc 70-30) cho training-validation. Nếu mô hình hoạt động tốt trên cả training lẫn validation samples thì được coi là tốt. Ngoài ra cũng có một số kỹ thuật khác để phòng tránh overfitting.

Cuối cùng sau training và valiation đã tìm được giá trị tối ưu $a$ và $b$ cho mô hình, thì mô hình sẽ được sử dụng để dự đoán (predict) giá trị $y$ tương ứng với một đầu vào $x$ mới.

Thường khi giải bài toán ML/DL cơ bản thực hiện các bước sau:
- Chuẩn bị dữ liệu: training, validation, testing sets.
- Xây dựng mô hình: neural network
- Xác định hàm mất mát và phương pháp tối ưu hóa
- Training/Fitting mô hình với dữ liệu
- Đánh giá mô hình
- Ứng dụng

Ví dụ Linear Regression như trên thực hiện trong TF như sau:
```python
x_data = np.array([[147, 150, 153, 158, 163, 165, 168, 170, 173, 175, 178, 180, 183]], dtype=float)
y_data = np.array([[49, 50, 51,  54, 58, 59, 60, 62, 63, 64, 66, 67, 68]],dtype = float)
x_data = x_data.T/100
y_data = y_data.T/100
def init_weight(shape):
    return tf.Variable(tf.random.normal(shape,stddev = 0.1))
def model(_input,w1,b1):
    return tf.add(tf.matmul(_input,w1), b1)
iteration = 1000
learning_rate = 0.01

x = tf.placeholder(tf.float32, shape=(None,1))
y = tf.placeholder(tf.float32, shape = (None,1))

_input = x
w1 = init_weight([1,1])
b1 = tf.Variable([0.])
y_pred = model(_input,w1,b1)
loss_op = tf.reduce_mean(tf.square(y - y_pred))
optimizer = tf.train.GradientDescentOptimizer(learning_rate)
train_op = optimizer.minimize(loss_op)

with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())    
    for epoch in range (iteration):
        sess.run(train_op,feed_dict = {x:x_data,y:y_data})
        if (epoch+1) % 100 == 0:
            print('Epoch = ',epoch+1,'Training loss = ', sess.run(loss_op,feed_dict={x:x_data,y:y_data}))
    print('training done!')
    print('w=',sess.run(w1),'b=',sess.run(b1))
```
## Logistic Regression
Logistic Regression là bài toán cơ bản thứ 2 của ML/DL, đây là bài toán mà đầu ra của mô hình (đầu ra dự đoán) là một tập hữu hạn các giá trị rời rạc. Nhiều khi người ta còn gọi đây là bài toán phân loại, vì mỗi giá trị rời rạc ở đầu ra có thể đại diện cho một lớp. Một số ví dụ Linear Logistic Regression
  - Dữ liệu là số giờ ôn thi, số giờ có mặt trên lớp và kết quả đậu/rớt của một sinh viên. Như hình 3, ở đây giả sử đậu là 1, rớt là 0. Ví dụ này đầu ra có hai lớp, còn gọi là binary classification
  - Bộ dữ liệu [Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set) là bộ dữ liệu lâu đời cho ML. Dữ liệu gồm chiều dài và chiều rộng cánh hoa, đài hoa của 3 loại hoa lan. Trong trường hợp này đầu ra là 3 lớp dữ liệu - có thể mã hóa thành 0,1,2 (hình 4).
  - Dữ liệu là một bức ảnh chứa một trong 2 loại chó hoặc mèo. Dự đoán đầu ra là chó (1) hay mèo (0). Trường hợp này đầu vào là dữ liệu không có cấu trúc (unstructure data) - không phải là vector có số chiều cố định như hai ví dụ trên
  - Bộ dữ liệu MNIST, CIFAR10,..đầu vào là là ảnh các chữ số viết tay đen trắng (MNIST), hoặc các hình ảnh màu khác (CIFAR10), đầu ra là các các chữ số tương ứng với con số trong ảnh MNIST, hoặc các con số đại diện cho một lớp hình ảnh nào đó trong ảnh CIFAR10 (ví dụ số 6 là đại diện cho xe tải- truck)

<div class="imgcap">
 <img src ="/images/bai-03/logis_reg.png" width="300">
 <div class = "thecap">Hình 3. Ví dụ logistic với đầu ra 2 lớp</div>
</div>

<div class="imgcap">
 <img src ="/images/bai-03/Iris.png" width="300">
 <div class = "thecap">Hình 4. Một phần bộ dữ liệu Iris</div>
</div>

Trong hai ví đầu các feature vector (vecto $\mathbf{x}$) hoàn toàn giống với bài toán Linear Regression, hai ví dụ sau có thể dùng phép biến đổi (ví dụ kéo dài ma trận thành vector) để biến thành feature vector như hai ví dụ đầu. Vậy nên có thể nghĩ đến cách giải tương tự như Linear Regression, tuy nhiên vì nhãn (label) là những con số tương đối nhỏ, trong khi đầu ra dự đoán theo Linear Regression $\widehat{\mathbf{y}=\mathbf{Wx+b}$ lại có thể là một số rất lớn, hoặc cũng thể là số âm. Mặt khác hàm tuyến tính rất nhạy cảm với nhiễu, chỉ một training sample vượt ra khỏi phân phối sẽ ảnh hưởng rất nhiều đến kết quả. Do vậy trong logistic regression người ta sử dụng một hàm kích hoạt (activation function) ở đầu ra để giải quyết các vấn đề này. Hàm này phải có các tính chất: Đầu ra của nó phải nằm giữa 0 hoặc 1 (hoặc -1, 1), nếu $y_{hat}$ rất lớn thì giá trị đầu ra gần với 1, nếu $y_{hat}$ rất nhỏ thì giá trị đầu ra gần với 0, mặt khác hàm này phải dễ tính được đạo hàm. Do vậy hàm được chọn là hàm *sigmoid* hoặc *softmax* (*sigmoid* là trường hợp riêng của *softmax* khi chỉ có 2 lớp). Đầu ra của *softmax* là xác suất để output đó thuộc về lớp tương ứng, xác suất này càng gần 1 nghĩa là dự đoán càng chính xác. Tương ứng như vậy loss function không còn là mean square error (MSE) nữa, thay vào đó là một hàm đo lường sự giống nhau giữa hai phân phối đơn vị (dự đoán và nhãn). Loss function đó gọi là *categorical cross entropy* trong trường hợp nhãn được mã hóa về dạng *one hot*, còn không, nhãn vẫn ở dạng các con số rời rạc thì gọi là *sparse categorical cross entropy*. Như vậy Logistic Regression giống với Linear Regression ngoại trừ loss function (và predicted output).

Ví dụ dưới đây là một mô hình Linear Logistic Regression để phân loại bộ dữ liệu Iris. Trong ví dụ có sử dụng một số câu lệnh trong thư viện [scikit-learn](https://scikit-learn.org/) để tiền xử lý dữ liệu đưa về dạng tiêu chuẩn (còn gọi là feature matrix) với feature matrix là một ma trận có các hàng là vecto có 4 phần tử (X) tương ứng với chiều dài, rộng của đài hoa, cánh hoa và nhãn (label) mã hóa dưới dạng one-hot (Y) tương ứng feature vector đó thuộc loại hoa (hớp) nào. Bộ dữ liệu có 150 mẫu được chia thành dữ liệu traing và testing với tỷ lệ 75%, 25%.
```python
import tensorflow as tf
print(tf.__version__)
from sklearn import datasets
from sklearn.model_selection import train_test_split
import numpy as np
from sklearn.preprocessing import OneHotEncoder

iris = datasets.load_iris()
X = iris['data']
y = iris['target']
y = np.expand_dims(y,axis =1)
onehot_encoder = OneHotEncoder(sparse=False)
Y = onehot_encoder.fit_transform(y)
X_tr,X_ts,Y_tr,Y_ts = train_test_split(X,Y,test_size = 0.25,random_state = 42)
print(X_ts.shape)
# Tạo ra x chứa dữ liệu, W chứa tham số mô hình, ở đây chỉ là 1 ma trận
x=tf.placeholder(tf.float32,[None,4])
# print(x)
W=tf.Variable(tf.random.normal([4,3],stddev = 0.1))
b= tf.Variable([0.])
# y_true là nhãn, y_pred là dự đoán(đầu ra)
y_true=tf.placeholder(tf.float32,[None,3])
y_pred= tf.add(tf.matmul(x,W),b)
# Loss fucntion
cross_entropy=tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits_v2(logits=y_pred,labels=y_true))
# Optimizer
gd_step=tf.train.GradientDescentOptimizer(0.5).minimize(cross_entropy)
#metric
correct_mask=tf.equal(tf.argmax(y_pred,1),tf.argmax(y_true,1))
accuracy=tf.math.reduce_mean(tf.cast(correct_mask,tf.float32))
NUM_STEPS = 1000
#train and run
with tf.Session() as sess:
    sess.run(tf.global_variables_initializer()) #bắt buộc phải chạy đoạn code này để chạy các biến
    for iteration in range(NUM_STEPS):
        sess.run(gd_step,feed_dict={x: X_tr,y_true:Y_tr})
        if iteration%50 == 0:
            train_acc = sess.run(accuracy,feed_dict={x: X_tr,y_true: Y_tr})
            print('Iteration:',iteration,' training accuracy:{:.4}% '.format(train_acc*100))

    ans=sess.run(accuracy,feed_dict={x:X_ts,y_true:Y_ts})
print ("Training done!\n Test accuracy: {:.4}%".format(ans*100))
```
Kết quả:
```
Iteration: 0  training accuracy:33.93% 
Iteration: 50  training accuracy:65.18% 
Iteration: 100  training accuracy:71.43% 
Iteration: 150  training accuracy:87.5% 
Iteration: 200  training accuracy:72.32% 
Iteration: 250  training accuracy:95.54% 
Iteration: 300  training accuracy:96.43% 
Iteration: 350  training accuracy:96.43% 
Iteration: 400  training accuracy:96.43% 
Iteration: 450  training accuracy:96.43% 
Iteration: 500  training accuracy:96.43% 
Iteration: 550  training accuracy:96.43% 
Iteration: 600  training accuracy:96.43% 
Iteration: 650  training accuracy:96.43% 
Iteration: 700  training accuracy:97.32% 
Iteration: 750  training accuracy:96.43% 
Iteration: 800  training accuracy:96.43% 
Iteration: 850  training accuracy:96.43% 
Iteration: 900  training accuracy:97.32% 
Iteration: 950  training accuracy:97.32% 
Training done!
 Test accuracy: 97.37%
```
Bài này đã trình bày một số khái niệm cơ bản về TF và hai ví dụ cơ bản nhất của ML với TF (Linear Regression và Logistic Regression). Thực tế khi sử dụng TF ở mức trừu tượng thấp khá bất tiện mặc dùng có thể hiểu tương đối kỹ về cách thức hoạt động của TF, khi thực hiện các bài toán phức tạp hơn người ta thường sử dụng các thư viện (framework) ở mức trừu tượng cao hơn như Keras (với backend - nền tảng là TF) sẽ giúp cho việc thực hiện ngắn gọn và dễ hiểu hơn rất nhiều. Những bài tiếp theo sẽ trình bày Keras và ứng dụng trong ML/DL.
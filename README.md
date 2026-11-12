# DES - 2DES - 3DES
## DES
**[DES](https://en.wikipedia.org/wiki/Data_Encryption_Standard)**, viết tắt cho từ **Data Encryption Standard**, là một thuật toán mã hóa đối xứng có ảnh hưởng lớn đến sự phát triển của ngành mật mã học. Từng được sử dụng rộng rãi trên phạm vi toàn thế giới, tuy nhiên tới thời điểm hiện tại, DES được xem là không đủ an toàn cho nhiều ứng dụng. Nguyên nhân chủ yếu là độ dài của khóa là quá nhỏ. Khóa DES đã từng bị phá trong vòng chưa đầy 24 giờ.

DES sử dụng một khóa 56 bit để mã hóa và giải mã. Tuy nhiên thực tế, khóa đầu vào là 64 bit, nhưng có 8 bit được sử dụng cho kiểm tra lỗi, nên chỉ có 56 bit thực sự được sử dụng cho quá trình mã hóa.

Cho nên chính vì điểm yếu đó mà DES đã có ảnh hướng lớn đến sự phát triển của thuật toán mã hóa nâng cao hơn là **AES**.

Bây giờ ta sẽ tìm hiểu sơ về cơ chế mã hóa DES :
Hãy nhìn vào hình vẽ này : 

<img width="368" height="994" alt="{94E4537C-7EA5-4372-9D7D-2FEA8AFE8012}" src="https://github.com/user-attachments/assets/511ee927-8e6b-4bfe-9c3c-ca58a4b4c4b4" />

Giải thích sơ qua cho mọi người dễ hình dung : 
- Cũng giống như AES, DES sẽ yêu cầu về độ dài văn bản là $64bits$ để mã hóa. 
- Đầu tiên, Plaintext sẽ được đi qua khối IP (Initial permutation), có nghĩa là hoán vị các bits trong Plaintext.
- Sau khi qua khối IP, dữ liệu lúc này sẽ được chia ra làm 2 nửa trái và phải (tạm gọi là $L_0$ và $R_0$) có độ dài bằng nhau và bằng 32bits.
- Sau đó, $L_0$ và $R_0$ sẽ trải qua một quá trình mã hóa (sẽ nói rõ ở phần sau) để thành $L_1$ và $R_1$. Bước này sẽ được lặp lại 16 lần cho đến khi ta có $L_{16}$ và $R_{16}$.
- Cuối cùng, $L_{16}$ và $R_{16}$ sẽ được ghép lại và đi qua khối FP (Final permutation), hoán vị một lần nữa để cho ra Ciphertext có độ dài là 64bits.

Từ sơ đồ trên, ta sẽ tìm hiểu về IP, FP, Hộp F cũng như là thuật toán sinh khóa của DES

### IP và FP
**[IP và FP](https://en.wikipedia.org/wiki/DES_supplementary_material)** (Initial and Final permutation) là 2 phép hoán vị chuỗi dữ liệu DES. Trong quá trình mã hóa, IP sẽ được thực hiện trước FP (giải mã thì ngược lại). 

IP có cách hoán vị như sau : 

<img width="750" height="411" alt="image" src="https://github.com/user-attachments/assets/83721ca0-3bee-4120-9f37-6cf67149fbc1" />

>Tức là các bit trong chuỗi 64bits ban đầu sẽ được dịch chuyển đến vị trí mới 

Hay có thể hiểu ở bảng sau :

![{6C38FCA5-DF71-443E-A6AC-6C3BD59398A7}](https://hackmd.io/_uploads/Hk_C4KXvyl.png)
> Tức là đầu ra sau quá trình IP sẽ là một chuỗi bits mới với bit đầu tiên là bit thứ 58 trong Plaintext, bit thứ 2 là bit ở vị trí thứ 50 trong Plaintext,... Cứ như thế cho đến bit cuối cùng.

Tương tự ta cũng có cách hoán vị của FP như sau :

![image](https://hackmd.io/_uploads/BkCsFFQvyl.png)

Dễ hiểu hơn thì :

![{0904C27A-EB73-4D70-A98C-8E9F99FF6A66}](https://hackmd.io/_uploads/SJOAKYXPJx.png)
> Ở đây FP còn có thể hiểu là ngược lại của IP, kí hiệu là $IP^{-1}$



### Hộp F (hàm Feistel)
Trong sơ đồ mã hóa bên trên, hộp F của chúng ta sẽ lấy 2 giá trị đầu vào là $R_i$ có 32bits và khóa $K_i$ có 48 bits cho ra kết quả là $F(R_i, K_i)$.

Cụ thể ở hình vẽ này : 

![image](https://hackmd.io/_uploads/BkcbntQPke.png)

Hàm F sẽ có 4 giai đoạn : 
- **Mở rộng** : 32 bit đầu vào được mở rộng thành 48 bit sử dụng thuật toán hoán vị mở rộng (expansion permutation) với việc nhân đôi một số bit. Giai đoạn này được ký hiệu là E trong sơ đồ.

Đây là cách mà E hoạt động :

![image](https://hackmd.io/_uploads/SyQ5aF7wJx.png)

Cụ thể hơn ở bảng này :

![{1578F13D-729E-448B-9355-06A97F1B5752}](https://hackmd.io/_uploads/SkAi6tQw1l.png)

- **Trộn khóa** : 48 bit thu được sau quá trình mở rộng được XOR với khóa con. Mười sáu khóa con 48 bit được tạo ra từ khóa chính 56 bit theo một chu trình tạo khóa con (key schedule) sẽ được nói cụ thể ở phần sau.

- **Thay thế** : 48 bit sau khi trộn được chia làm 8 khối con 6 bit và được xử lý qua hộp thay thế S-box. Đầu ra của mỗi khối 6 bit là một khối 4 bit theo một chuyển đổi phi tuyến tính.
    - S-box (Substitution boxes) gồm 8 bảng. Nó khác với S-box của AES, trong sơ đồ, sau khi ta thực hiện phép toán $E(R) \oplus K$ thì phần output đó sẽ chia ra làm 8 phần có độ dài bằng nhau (output = 48 bits nên 8 phần đó sẽ có độ dài là 6 bits)
    - Mỗi bảng S-box sẽ biến đổi 6bits này thành 4bits theo quy tắc : Đặt 6bits đó có dạng **xyyyyx**, ta sẽ dùng 2 bit ngoài cùng là **xx** để xác định hàng và 4 bit bên trong là **yyyy** để xác định cột. Có hàng và cột thì ta đối chiếu sang bảng S-box tương ứng. 
    - Ví dụ : Cho Input là 110101, xx = 11, yyyy = 1010. Ta có bảng S-box sau : ![{2E89B810-0B48-428D-9A4D-4FDF813CCAAF}](https://hackmd.io/_uploads/HJbZzcXDke.png)
Dựa vào bảng trên ta có thể suy ra giá trị cần tìm là 3, hay mã nhị phân của nó là 0011. Cứ như thế cho 8 phần, mỗi một phần là một bảng S-box riêng, không giống nhau. Các bạn có thể tra khảo bảng **[S-box ở đây](https://en.wikipedia.org/wiki/DES_supplementary_material#Substitution_boxes_(S-boxes))** 

- **Hoán vị** : Cuối cùng, 32 bit thu được sau S-box (từ 48bits cắt bớt đi $2 \times 8 = 16 bits$ còn 32bits) sẽ được sắp xếp lại theo một thứ tự cho trước (còn gọi là P-box).
    - P-box cũng chỉ là phép hoán vị theo quy luật :

      ![image](https://hackmd.io/_uploads/SknONcQw1x.png)

	- Cụ thể :
	  ![image](https://hackmd.io/_uploads/B1N94qmvJx.png)

Và đó chính là cách mà hàm F hoạt động. Sau khi trải qua một quá trình mã hóa như vậy sẽ cho ta 2 giá trị mới là $L_i$ và $R_i$ và **được sắp xếp** theo công thức :

$$
L_i = R_{i-1} , R_i = L_{i-1} \oplus F(R_{i-1}, K_i)
$$



### Thuật toán sinh khóa DES
Như đã nói, DES sẽ sử dụng khóa đầu vào là 64bits. Tuy nhiên, có 8bits sẽ bị loại bỏ chỉ còn 56bits được chọn để mở rộng khóa mã hóa. 

Mô tả ở hình vẽ này :

![image](https://hackmd.io/_uploads/SJ1qOqmPyl.png)

Giải thích chi tiết : 
- Đầu tiên khóa 64bits sẽ được đi vào khối $PC1$ (Permuted Choice 1). Khối này có nhiệm vụ chọn ra 56bits để sinh khóa, bỏ đi 8 bits theo phép hoán vị sau :

![image](https://hackmd.io/_uploads/HJFLFcmvkx.png)

Cụ thể :

![{33A9901B-BF27-4334-964C-29FB510A3B38}](https://hackmd.io/_uploads/SJ1_KqmDyg.png)
> Để ý thấy thì ta sẽ không có các bit : 8, 16, 24, 32, 40, 48, 56, 64. Vì chúng được chỉ định để sử dụng làm bit chẵn lẻ.
- Sau khi chọn xong khóa mới 56bits thì lúc này ta chia nó ra làm 2 phần bằng nhau (= 28bits), rồi sau đó sẽ tiến hành dịch vòng trái bit (dấu $<<<$ trong sơ đồ. Và sau đó, 2 phần này sẽ được đưa vào khối $PC2$ (Permuted Choice 2) rút gọn từ 56bits xuống 48bits để tạo ra khóa.
> Lưu ý : Dịch bit sẽ khác nhau ở mỗi chu trình. Ở chu trình 1,2,9,16 sẽ dịch 1 bit, còn lại thì sẽ dịch 2 bit.

$PC2$ hoán vị như sau :

![image](https://hackmd.io/_uploads/r1aMDnND1e.png)

Cụ thể :

![{461A76B4-D2D3-4C22-87A1-E1D3CF70B17A}](https://hackmd.io/_uploads/rkqEw24Pyx.png)
> $PC2$ sẽ bỏ qua các bit 9, 18, 22, 25, 35, 38, 43, 54.


### Ví dụ về Code DES
```python
#Khai báo các thư viện cần thiết cho DES
from Crypto.Cipher import DES
from Crypto.Util.Padding import pad, unpad
from Crypto.Random import get_random_bytes

# Khóa DES dài 64bits = 8bytes
key = get_random_bytes(8)
msg = b'DES{A good cipher}'
cipher = DES.new(key, DES.MODE_ECB)

#Mã hóa
ct = cipher.encrypt(pad(msg, DES.block_size))
print(ct.hex())

#Giải mã
pt = unpad(cipher.decrypt(ct), DES.block_size).decode()
print(pt)
```




## 2DES (Double DES)
**Double DES** là một biến thể của thuật toán mã hóa DES (Data Encryption Standard), được thiết kế nhằm tăng cường độ bảo mật so với DES tiêu chuẩn. Thay vì mã hóa dữ liệu một lần, Double DES thực hiện quá trình mã hóa hai lần bằng cách sử dụng hai khóa khác nhau.

Chúng ta biết rằng, nếu mã hóa DES 1 lần thì sẽ rất dễ bị phá do DES chỉ xài khóa có độ dài 56bits, vì vậy, Double DES ra đời nhằm mục đích khắc phục hạn chế này bằng cách sử dụng hai lần mã hóa DES, nhưng không thực sự cải thiện đáng kể về độ bảo mật.

Cách 2DES hoạt động :

![image](https://hackmd.io/_uploads/rkiNj3Vw1g.png)

- Văn bản gốc $P$ sẽ được mã hóa DES lần một với khóa đầu tiên $K_1$ tạo thành văn bản trung gian $X$. 
- Sau đó, $X$ sẽ được mã hóa DES thêm một lần nữa với khóa $K_2$ khác với khóa $K_1$ để cho ra văn bản mã hóa cuối cùng ($C$).

Có thể hiểu theo công thức này : $C = E_{K_2}(E_{K_1}(P))$
Khi giải mã thì ta sẽ làm ngược lại, theo công thức này : $P = D_{K_1}(D_{K_2}(C))$

Nhìn an toàn là thế, tuy nhiên 2DES có điểm yếu là có thể bị tấn công **Meet-in-the-middle attack** sẽ được nói ở phần sau.




## 3DES (Triple DES)
3DES (Triple DES) là một phiên bản nâng cao của thuật toán DES (Data Encryption Standard), được thiết kế để giải quyết những hạn chế về bảo mật của DES (chẳng hạn như không gian khóa nhỏ). 3DES tăng cường độ bảo mật bằng cách mã hóa dữ liệu ba lần, sử dụng hai hoặc ba khóa khác nhau.

Cũng giống như 2DES, chỉ khác là sẽ mã hóa thêm một lần nữa. Có hai chế độ phổ biến của 3DES: **EDE (Encrypt-Decrypt-Encrypt)** và **EEE (Encrypt-Encrypt-Encrypt)**.

Hình vẽ minh họa :

<img width="1044" height="991" alt="image" src="https://github.com/user-attachments/assets/f5b69e7a-3862-497c-b4ec-dcbb8e6a15fc" />

Cách mã hóa như sau : 
- Nếu ta chọn chế độ **EEE** thì nó cũng sẽ tương tự như 2DES, chỉ khác là sẽ mã hóa thêm lần thứ ba với khóa $K_3$ vậy thôi. Công thức :

$$
C = E_{K_3}(E_{K_2}(E_{K_1}(P)))
$$

- Nếu ta chọn chế độ **EDE** thì sẽ hơi khác ở quy trình thứ 2, là ta sẽ giải mã văn bản đã được mã hóa ở lần thứ nhất bằng khóa $K_2$. Tất nhiên là nó sẽ chẳng sao cả vì 2 khóa là khác nhau, nên văn bản được giải mã ở quy trình thứ 2 sẽ chỉ là một loại văn bản không có nghĩa. Công thức :-1: 

$$
C = E_{K_3}(D_{K_2}(E_{K_1}(P)))
$$

Và giải mã ta chỉ cần làm ngược lại : 

$$
P = D_{K_1}(E_{K_2}(D_{K_3}(C)))
$$

Chắc chắn một điều rằng chế độ **EDE** sẽ được ưa chuộng hơn vì nó có bước giải mã ở giữa, làm cho kẻ tấn công cũng sẽ mất thời gian để phá. 

Hãy chắc chắn rằng, khi sử dụng 2DES hoặc 3DES thì các khóa phải khác nhau. Nếu chỉ sử dụng duy nhất một khóa đễ mã hóa 2 hoặc 3 lần thì chắc chắn sẽ không an toàn. Nó sẽ chẳng khác gì DES bình thường, chả qua là tốn thời gian để giải mã thôi.



# AES
## Khái niệm và cách hoạt động
### Định nghĩa
- **AES** (viết tắt của từ tiếng anh: **Advanced Encryption Standard**, hay Tiêu chuẩn mã hóa nâng cao) là một thuật toán mã hóa khối được chính phủ Hoa Kỳ áp dụng làm tiêu chuẩn mã hóa.
- Thuật toán được xây dựng dựa trên **Rijndael Cipher** phát triển bởi 2 nhà mật mã học người Bỉ: **Joan Daemen** và **Vincent Rijmen**.
- AES làm việc với các khối dữ liệu 128bit và độ dài khóa 128bit, 192bit hoặc 256bit. Các khóa mở rộng sử dụng trong chu trình được tạo ra bởi thủ tục sinh khóa Rijndael.
- Hầu hết các phép toán trong thuật toán AES đều thực hiện trong một trường hữu hạn của các byte. Mỗi khối dữ liệu đầu vào 128bit được chia thành 16byte, có thể xếp thành 4 cột, mỗi cột 4 phần tử hay một ma trận 4x4 của các byte, nó gọi là ma trận trạng thái.
- Tùy thuộc vào độ dài của khóa khi sử dụng 128bit, 192bit hay 256bit mà thuật toán được thực hiện với số lần lặp khác nhau.



### Cách hoạt động 
Khi mã hóa văn bản gốc bằng AES thì ta phải trải qua các bước sau :
- **Chọn khóa AES** : Có 3 loại khóa AES là `AES-128`, `AES-192`, `AES-256`. Các số 128, 192, 256 biểu thị cho độ dài của khóa AES lần lượt là **128bits**, **192bits**, **256bits**.
- **Đảm bảo độ dài của văn bản bằng với khóa** : Giả sử khi ta chọn khóa `AES-128` thì độ dài văn bản phải là bội của **128bits** (128, 256, 384,...).
- Sau khi chọn xong khóa và đảm bảo được độ dài văn bản thì ta sẽ tiến hành mã hóa.

Ở đây ta sẽ làm ví dụ với khóa `AES-128` : 
- Đầu tiên, AES sẽ chia văn bản `128bits = 16bytes` của chúng ta ra làm 4 phần, với mỗi phần có **4bytes** và xếp thành cột, tạo ra một khối văn bản $4\times4$: 
![image](https://hackmd.io/_uploads/BJ8LCrIU1x.png)
- Tiếp theo, khối văn bản đó sẽ trải qua các bước : 
    - **Initial key addition**: Bước đầu của quá trình mã hóa, key đầu tiên mà ta chọn (dài 128bits) sẽ được $\oplus$ với khối văn bản.
    - **Round** : Đây là một quá trình mã hóa gồm 10 vòng, trong đó có 9 vòng chính và 1 vòng cuối cùng. Với mỗi một vòng, văn bản sẽ được mã hóa thông qua các hàm : **AddRoundKey**, **Subbytes**, **ShiftRow**, **MixColumns**. Đặc biệt, ở vòng mã hóa cuối cùng không có bước **MixColumns**.
> Có một điều là số vòng mã hóa AES cũng sẽ phụ thuộc vào loại khóa mà bạn chọn. Ví dụ khi chọn khóa **AES-128** thì số vòng mã hóa sẽ là $10$. Tuy nhiên nếu chọn khóa **AES-192** hoặc **AES-256** thì số vòng mã hóa sẽ là $12$ và $14$.

Minh họa ở hình vẽ này :

![image](https://hackmd.io/_uploads/rJgNUU8Lke.png)



Chúng ta sẽ đi tìm hiểu về các vòng mã hóa AES : 
#### AddRoundKey
Tại đây, khối văn bản gốc và khối khóa sẽ được $\oplus$ với nhau :

<img width="638" height="498" alt="image" src="https://github.com/user-attachments/assets/bc368c9d-4c05-4c13-a69b-a8229568bace" />

Ví dụ : 
Ta có một khối văn bản như sau (các dữ liệu trong khối thuộc **base16**) :

$$
\begin{bmatrix}
1a & 2b & 3c & 4d \\
5e & 6f & 7a & 8b \\
9c & ad & be & cf \\
da & eb & fc & 0a
\end{bmatrix}_{\text{Plain}}
$$

Và ta có một khối khóa : 

$$
\begin{bmatrix}
0f & 1e & 2d & 3c \\
4b & 5a & 69 & 78 \\
87 & 96 & a5 & b4 \\
c3 & d2 & e1 & f0
\end{bmatrix}_{\text{Key}}
$$

Và giờ ta sẽ XOR $\oplus$ từng phần tử của `khối Plain` với `khối Key`. Và đây là kết quả :

$$
\begin{bmatrix}
15 & 35 & 11 & 71 \\
15 & 35 & 13 & f3 \\
1b & 3b & 1b & 7b \\
19 & 39 & 1d & fa
\end{bmatrix}_{\text{Ans}}
$$



#### SubBytes (Substitude bytes)
Ở bước này sẽ thay thế các giá trị hex trong khối thành các giá trị hex mới trong bảng **Rijndael S-box**.

![S_box Table AES](https://hackmd.io/_uploads/ryVHT8LIyl.png)

<img width="1000" height="520" alt="image" src="https://github.com/user-attachments/assets/ab3827ef-a99a-4eee-8a74-8f0620b60275" />

Ví dụ : 
Ta sẽ **SubBytes** `khối Ans` hồi nãy :

$$
\begin{bmatrix}
15 & 35 & 11 & 71 \\
15 & 35 & 13 & f3 \\
1b & 3b & 1b & 7b \\
19 & 39 & 1d & fa
\end{bmatrix}_{\text{Ans}}
$$

Với :
- Phần tử $a_{11} = 15$, ta sẽ đối chiếu lên bảng S-box với cách tra là : dòng 1, cột 5. Kết quả là $59$.
- Hay phần tử $a_{44} = fa$, ta sẽ đối chiếu lên bảng S-box với cách tra là : dòng f, cột a. Kết quả là $2d$. 

Tương tự với các phần tử còn lại, ta thu được khối mới là:

$$
\begin{bmatrix}
59 & 96 & 82 & a3 \\
59 & 96 & 7d & 0d \\
af & e2 & af & 21 \\
d4 & 12 & a4 & 2d
\end{bmatrix}_{\text{SubBytes}}
$$



#### ShiftRows
Ở bước này, các hàng của khối sẽ được **dịch sang trái** theo quy luật : 
- Hàng 1 : Không đổi
- Hàng 2 : Dịch sang trái 1 phần tử 
- Hàng 3 : Dịch sang trái 2 phần tử 
- Hàng 4 : Dịch sang trái 3 phần tử

<img width="1907" height="706" alt="{C8BBBD5C-1880-4E19-8421-3DBD787EE4C7}" src="https://github.com/user-attachments/assets/65861f84-560b-44dc-b081-597f1643c23b" />

Ví dụ : 
Ta sẽ **ShiftRow** khối đã **SubBytes** hồi nãy :

$$
\begin{bmatrix}
59 & 96 & 82 & a3 \\
59 & 96 & 7d & 0d \\
af & e2 & af & 21 \\
d4 & 12 & a4 & 2d
\end{bmatrix}_{\text{SubBytes}}
$$

Xét hàng : $\begin{bmatrix} 59 & 96 & 82 & a3 \end{bmatrix}$ : Hàng này là hàng đầu tiên nên không thay đổi.
Với hàng : $\begin{bmatrix} 59 & 96 & 7d & 0d \end{bmatrix}$ : Dịch trái 1 phần tử, ta có hàng mới là:

$$
\begin{bmatrix} 96 & 7d & 0d & 59 \end{bmatrix}
$$  

Làm tương tự với 2 hàng cuối, ta sẽ có khối mới :

$$
\begin{bmatrix}
59 & 96 & 82 & a3 \\
96 & 7d & 0d & 59 \\
af & 21 & af & e2 \\
12 & a4 & 2d & d4 
\end{bmatrix}_{\text{ShiftRows}}
$$



#### MixColumns 
Ở bước này, ta sẽ nhân từng cột của khối với khối **c(x)** để ra một khối mới. Khối **c(x)** được định nghĩa như sau :

$$
\begin{bmatrix}
02 & 03 & 01 & 01 \\
01 & 02 & 03 & 01 \\
01 & 01 & 02 & 03 \\
03 & 01 & 01 & 02 \\
\end{bmatrix}_{\text{c(x)}}
$$

Chi tiết hơn thì bạn có thể xem ở đây : **[c(x) MixColumns](https://en.wikipedia.org/wiki/Rijndael_MixColumns)**

Ví dụ : 
Ta sẽ **MixColumns** khối **ShiftRow** bằng cách nhân từng cột của nó với khối **c(x)**

$$
\begin{bmatrix}
02 & 03 & 01 & 01 \\
01 & 02 & 03 & 01 \\
01 & 01 & 02 & 03 \\
03 & 01 & 01 & 02 \\
\end{bmatrix}_{\text{c(x)}}
\times
\begin{bmatrix}
59 \\
96 \\
af \\
12 \\
\end{bmatrix}_{\text{column 1}} =
\begin{bmatrix}
ae \\
96 \\
bc \\
f6 \\
\end{bmatrix}
$$

$$
\begin{bmatrix}
02 & 03 & 01 & 01 \\
01 & 02 & 03 & 01 \\
01 & 01 & 02 & 03 \\
03 & 01 & 01 & 02 \\
\end{bmatrix}_{\text{c(x)}}
\times
\begin{bmatrix}
96 \\
7d \\
21 \\
a4 \\
\end{bmatrix}_{\text{column 2}} =
\begin{bmatrix}
35 \\
ab \\
5e \\
ae \\
\end{bmatrix}
$$

$$
\begin{bmatrix}
02 & 03 & 01 & 01 \\
01 & 02 & 03 & 01 \\
01 & 01 & 02 & 03 \\
03 & 01 & 01 & 02 \\
\end{bmatrix}_{\text{c(x)}}
\times
\begin{bmatrix}
82 \\
0d \\
af \\
2d \\
\end{bmatrix}_{\text{column 3}} =
\begin{bmatrix}
8a \\
5f \\
bd \\
65 \\
\end{bmatrix}
$$

$$
\begin{bmatrix}
02 & 03 & 01 & 01 \\
01 & 02 & 03 & 01 \\
01 & 01 & 02 & 03 \\
03 & 01 & 01 & 02 \\
\end{bmatrix}_{\text{c(x)}}
\times
\begin{bmatrix}
a3 \\
59 \\
e2 \\
d4 \\
\end{bmatrix}_{\text{column 4}} =
\begin{bmatrix}
80 \\
f8 \\
42 \\
f6 \\
\end{bmatrix}
$$

Như vậy, ta sẽ có khối mới :

$$
\begin{bmatrix}
ae & 35 & 8a & 80 \\
96 & ab & 5f & f8 \\
bc & 5e & bd & 42 \\
f6 & ae & 65 & f6 \\
\end{bmatrix}_\text{Mix Columns}
$$



#### Key Schedule (Key Expansion hay Sinh khóa)
Mỗi vòng **(Round)** đều có bước **AddRoundKey**, ngay cả ở bước đầu tiên cũng vậy. Dù đây là mật mã mã hóa đối xứng nhưng chúng ta sẽ không chỉ sử dụng một khóa duy nhất cho cả quá trình mã hóa, mà thay vào đó, từ một khóa ban đầu ta sẽ mở rộng ra thêm nhiều khóa con để mã hóa cho từng vòng.

Trong trường hợp chọn khóa `AES-128`, từ khóa **16bytes** ban đầu, ta sẽ tạo ra thêm 10 khóa **16bytes** khác để mã hóa cho 10 vòng mã hóa (tổng cộng sẽ có 11 khóa, 1 khóa ban đầu và 10 khóa con được tạo ra từ đó).

Đầu tiên ta sẽ nói về **Round Constants** (Hằng số vòng) : 
- **Round Constant** hay kí hiệu là **Rcon** là hằng số được dùng để mở rộng khóa AES. 
- Rcon[j] = (RC[j], 00, 00, 00) với RC[1] = 01, RC[2] = 02,... Tất cả có 10 giá trị $j$ được ghi ở bảng sau : 

| Round | 1       | 2       | 3       | 4       | 5        | 6        | 7        | 8        | 9        | 10       |
|-------|---------|---------|---------|---------|----------|----------|----------|----------|----------|----------|
| Rcon  | [01,0,0,0] | [02,0,0,0] | [04,0,0,0] | [08,0,0,0] | [10,0,0,0] | [20,0,0,0] | [40,0,0,0] | [80,0,0,0] | [1B,0,0,0] | [36,0,0,0] |

Cụ thể hơn các bạn có thể xem ở đây : **[Rcon](https://en.wikipedia.org/wiki/AES_key_schedule)**

Bây giờ chúng ta sẽ đi vào phần mở rộng khóa : Ta lấy một khóa `AES-128` là **`e2912e9af62fa99807e3b05d628bd51d`**

Chia khóa đó ra làm 4 phần có độ dài là 4bytes : 
- w0 = `e2912e9a` (4 bytes đầu)
- w1 = `f62fa998` (4 bytes tiếp theo)
- w2 = `07e3b05d` (4 bytes tiếp)
- w3 = `628bd51d` (4 bytes cuối)

Ta sẽ tạo ra khóa thứ nhất từ khóa ban đầu **(khóa cho vòng 1)**
Đặt **khóa 1** có dạng là : `w4w5w6w7`

Để có được 4 bytes đầu tiên (w4) ta có công thức sau :

$$
w4 = w0 \oplus g(w3)
$$

Ở đây để có đc $g(w3)$ ta sẽ trải qua 3 công đoạn : 
- Công đoạn 1 : RotWord $w3$, hay nói cụ thể hơn là dịch trái $w3$ 1 lần. 
- Công đoạn 2 : SubBytes $w3$ mới dựa trên bảng S-Box.
- Công đoạn 3 : Xor $w3$ mới vừa tìm được ở công đoạn 2 với hằng số Rcon tương ứng của vòng.

Ví dụ : 
Ta có : w3 = `628bd51d`
- Dịch trái 1 lần được : `8bd51d62`
- SubBytes `8bd51d62` được : `3d03a4aa`
- Xor `3d03a4aa` với hằng số Rcon tương ứng với vòng. Ở đây ta đang tạo khóa cho vòng 1 nên sẽ sử dụng **Rcon[1] = [1, 0, 0, 0]** hay chính là `01000000`.
    > `3d03a4aa` $\oplus$ `01000000` = `3c03a4aa`

Như vậy ta có $g(w3)$ $=$ `3c03a4aa`
=> $w4$ = `e2912e9a` $\oplus$ `3c03a4aa` = `de928a30`

Để tính $w5$, $w6$, $w7$ thì ta sẽ tính dựa trên công thức:

$$
w_i = w_{i - 1} \oplus w_{i - 4}
$$
> Công thức này chỉ đúng khi tính 3 bytes $w5, w6, w7$

Từ đây ta có : 
- $w5$ = `28bd23a8`
- $w6$ = `2f5e93f5`
- $w7$ = `4dd546e8`

=> $key_1$ = **`de928a3028bd23a82f5e93f54dd546e8`**

Đó là cách để sinh khóa mới từ khóa ban đầu. Để sinh khóa cho vòng 2, ta sẽ sử dụng khóa $key_1$ biến đổi theo quá trình trên là sẽ ra khóa $key_2$. Cứ như thế cho đến khóa cho vòng cuối cùng.

Hình vẽ minh họa :

![{ADB6B840-30FA-4A3E-94C3-3C28E42D63F7}](https://hackmd.io/_uploads/BypYEfFI1g.png)






# Block cipher modes of operation
## Electronic Code Book (ECB)
**Electronic Code Block (ECB)** là một trong những phương thức hoạt động (modes of operation) của các thuật toán mã hóa khối (block cipher). Đây là phương thức mã hóa đơn giản nhất nhưng không được khuyến khích sử dụng trong các hệ thống hiện đại do những hạn chế về bảo mật. 

Cách để mã hóa là ta sẽ chia văn bản gốc thành những khối cố định (thường là 64 hoặc 128 bit, tùy thuộc vào thuật toán mã hóa (như AES hoặc DES), sau đó ta sẽ mã hóa AES từng khối với cùng một khóa mã hóa. 

Minh họa ở hình vẽ sau :  

<img width="1909" height="769" alt="{05C816EE-DA32-4869-8CB0-A22C8B7B4650}" src="https://github.com/user-attachments/assets/bb7d02b1-44fb-43be-ad6e-13a08501134a" />


Khi giải mã ta cũng sẽ làm tương tự với quá trình trên : 

<img width="1906" height="768" alt="{FCEFCF2C-A0B4-439E-B70C-45838D5F1AE8}" src="https://github.com/user-attachments/assets/083a89b4-2db7-4e3e-9532-ed13686a4b1c" />


ECB không an toàn trong hầu hết các trường hợp sử dụng thực tế do những vấn đề sau:


- Nếu hai khối văn bản gốc giống hệt nhau, thì các khối mã hóa tương ứng cũng sẽ giống nhau. Điều này làm lộ mẫu (patterns) trong dữ liệu, tạo điều kiện cho kẻ tấn công suy luận thông tin từ dữ liệu mã hóa.
- Vì mỗi khối được mã hóa độc lập, kẻ tấn công có thể sử dụng các phương pháp phân tích khối mã hóa để phá giải.
- ECB không thêm bất kỳ yếu tố ngẫu nhiên nào vào quá trình mã hóa, dẫn đến tính bảo mật thấp khi dữ liệu có cấu trúc rõ ràng.

Đây là một ví dụ minh họa về mã hóa ECB khi dùng để mã hóa hình ảnh. Có thể thấy từ hình ảnh mã hóa có thể dễ dàng đoán được hình ảnh gốc :

![image](https://hackmd.io/_uploads/HkGjtLqI1x.png)


Đây là một ví dụ về code Python của mã hóa **ECB** : 
```python=
# khai báo các thư viện cần thiết cho AES, ECB
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes
from Crypto.Util.Padding import pad,unpad

key = get_random_bytes(16) #tạo key 16bytes 
msg = "ECB_is_so_weak"

cipher = AES.new(key, AES.MODE_ECB)
encryp = cipher.encrypt(pad(msg.encode(), 16)) #mã hóa 
print(encryp)
# b'\xa2\x9aJF\xb7\x81\xf6:7\xfc\xd1\xa3\x07\x9f\xa6\xd0'

decryp = unpad(cipher.decrypt(encryp), 16) #giải mã
print(decryp)
# b'ECB_is_so_weak'
```





## Cipher block chaining (CBC)
**Cipher block chaining (CBC)** là một dạng mã hóa khối khi văn bản gốc vẫn sẽ được chia thành những khối kích thước giống nhau, nhưng khác ở chỗ, khối văn bản đầu tiên sẽ được XOR ($\oplus$) với **Initialization Vector** (tạm dịch là vector khởi tạo), rồi đưa vào thuật toán AES, cho ra ciphertext đầu tiên. Từ ciphertext đầu tiên đó, các khối văn bản phía sau sẽ được XOR với nó và đưa vào thuật toán cho ra ciphertext mới (hoặc có thể hiểu trước khi được đưa qua thuật toán mã hóa AES thì những khối văn bản đó sẽ được **XOR** với **khối văn bản mã hóa phía trước nó**.)

Hình ảnh minh họa cụ thể : 
![image](https://hackmd.io/_uploads/B1YCdl3Ike.png)

**Initialization Vector (IV)** là một giá trị ngẫu nhiên hoặc giả ngẫu nhiên được sử dụng trong các chế độ mã hóa khối, chẳng hạn như Cipher Block Chaining (CBC), để đảm bảo rằng kết quả mã hóa của cùng một văn bản gốc sẽ khác nhau mỗi lần mã hóa, ngay cả khi dùng chung một khóa mã hóa. Đặc điểm của IV:
- Độ dài: IV thường có cùng độ dài với kích thước khối của thuật toán mã hóa (ví dụ: 128 bit đối với AES).
- Ngẫu nhiên: Giá trị IV phải được chọn ngẫu nhiên hoặc giả ngẫu nhiên để tránh lặp lại, điều này đảm bảo tính an toàn của hệ thống.
- Không cần bảo mật: IV không phải là một bí mật, nhưng nó cần phải được gửi hoặc lưu trữ cùng với dữ liệu được mã hóa để giải mã chính xác.

Cụ thể thì có thể xem ở đây : **[Initialization Vector.](https://en.wikipedia.org/wiki/Initialization_vector)**

Đây là ảnh minh họa quá trình giải mã : 
![image](https://hackmd.io/_uploads/rydg9g38Jg.png)

Có thể thấy, từ khối ciphertext ban đầu ta có thể dễ dàng suy ra các khối phía sau. Tuy nhiên, ở khối ciphertext ban đầu, nếu ta chọn sai IV đầu vào thì khi giải mã ra khối plaintext đầu sẽ sai. 

Code mẫu trong python:
```python=
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
from Crypto.Random import get_random_bytes

key = get_random_bytes(16)
iv = get_random_bytes(16) 

pt = b'CBC_is_cool'

def encrypt_CBC(key, iv, pt):
    cipher = AES.new(key, AES.MODE_CBC, iv) 
    pt = pad(pt,16)
    ct = cipher.encrypt(pt)
    return ct

def decrypt_CBC(key, iv, ct):
    cipher = AES.new(key, AES.MODE_CBC, iv) 
    pt = unpad(cipher.decrypt(ct), 16)
    return pt

ct = encrypt_CBC(key, iv, pt)
print(ct)
#b'\xd7\xe1,\xf6\xdf/\xe6:\x82F\x03\x04}\xe2\xe7h'

pt = decrypt_CBC(key, iv, ct)
print(pt)
#b'CBC_is_cool'
```




## Propagating Cipher Block Chaining (PCBC)
**PCBC (Propagating Cipher Block Chaining)** được thiết kế nhằm làm cho các thay đổi nhỏ trong bản mã lan truyền vô hạn khi giải mã cũng như khi mã hóa. 

Trong chế độ PCBC, Plaintext được thực hiện phép XOR với cả Plaintext và Ciphertext trước đó trước khi được mã hóa. Giống như trong chế độ CBC, một IV sẽ được sử dụng cho khối Plaintext đầu tiên. 

Sơ đồ mã hóa : 
![image](https://hackmd.io/_uploads/BJMndIftyl.png)

Giải mã : 
![{EB1A5D93-BD25-4AF6-8898-04E078B461AD}](https://hackmd.io/_uploads/rJNcF8ftye.png)

Khác với CBC, việc giải mã PCBC với IV không chính xác sẽ làm hỏng tất cả các khối Plaintext phía sau do chúng được XOR với khối Plaintext đầu tiên có XOR với IV.

Ví dụ : Challenge **AESOS - KCSC Recruitment 2025** : 

`chal.py` : 
```python
from Crypto.Util.number import *
from Crypto.Util.Padding import *
from aes import *
import os
from pwn import xor
from secret import flag

cipher = AES(os.urandom(16))

def encrypt(msg: bytes) -> bytes:
    iv = os.urandom(16) #lấy ngẫu nhiên 16byte
    return iv + cipher.encrypt(msg, iv)

def decrypt(c: bytes) -> bytes:
    return cipher.decrypt(c[16:], c[:16])

while True:

    print("1. Encrypt")
    print("2. Decrypt")
    print("3. Flag")
    
    otp = int(input(">> "))
    
    if otp == 1:
        
        msg = bytes.fromhex(input("Enter your message: "))
        print(encrypt(msg).hex())
    
    elif otp == 2:
        
        msg = bytes.fromhex(input("Enter the ciphertext: "))
        print(decrypt(msg).hex())
        
    elif otp == 3:
        
        print(encrypt(flag).hex())
        
    else:
        exit()
```
::: spoiler AES_Source code
```python
#!/usr/bin/env python3
"""
This is an exercise in secure symmetric-key encryption, implemented in pure
Python (no external libraries needed).

Original AES-128 implementation by Bo Zhu (http://about.bozhu.me) at 
https://github.com/bozhu/AES-Python . PKCS#7 padding, CBC mode, PKBDF2, HMAC,
byte array and string support added by me at https://github.com/boppreh/aes. 
Other block modes contributed by @righthandabacus.


Although this is an exercise, the `encrypt` and `decrypt` functions should
provide reasonable security to encrypted messages.
"""


s_box = (
    0x63, 0x7C, 0x77, 0x7B, 0xF2, 0x6B, 0x6F, 0xC5, 0x30, 0x01, 0x67, 0x2B, 0xFE, 0xD7, 0xAB, 0x76,
    0xCA, 0x82, 0xC9, 0x7D, 0xFA, 0x59, 0x47, 0xF0, 0xAD, 0xD4, 0xA2, 0xAF, 0x9C, 0xA4, 0x72, 0xC0,
    0xB7, 0xFD, 0x93, 0x26, 0x36, 0x3F, 0xF7, 0xCC, 0x34, 0xA5, 0xE5, 0xF1, 0x71, 0xD8, 0x31, 0x15,
    0x04, 0xC7, 0x23, 0xC3, 0x18, 0x96, 0x05, 0x9A, 0x07, 0x12, 0x80, 0xE2, 0xEB, 0x27, 0xB2, 0x75,
    0x09, 0x83, 0x2C, 0x1A, 0x1B, 0x6E, 0x5A, 0xA0, 0x52, 0x3B, 0xD6, 0xB3, 0x29, 0xE3, 0x2F, 0x84,
    0x53, 0xD1, 0x00, 0xED, 0x20, 0xFC, 0xB1, 0x5B, 0x6A, 0xCB, 0xBE, 0x39, 0x4A, 0x4C, 0x58, 0xCF,
    0xD0, 0xEF, 0xAA, 0xFB, 0x43, 0x4D, 0x33, 0x85, 0x45, 0xF9, 0x02, 0x7F, 0x50, 0x3C, 0x9F, 0xA8,
    0x51, 0xA3, 0x40, 0x8F, 0x92, 0x9D, 0x38, 0xF5, 0xBC, 0xB6, 0xDA, 0x21, 0x10, 0xFF, 0xF3, 0xD2,
    0xCD, 0x0C, 0x13, 0xEC, 0x5F, 0x97, 0x44, 0x17, 0xC4, 0xA7, 0x7E, 0x3D, 0x64, 0x5D, 0x19, 0x73,
    0x60, 0x81, 0x4F, 0xDC, 0x22, 0x2A, 0x90, 0x88, 0x46, 0xEE, 0xB8, 0x14, 0xDE, 0x5E, 0x0B, 0xDB,
    0xE0, 0x32, 0x3A, 0x0A, 0x49, 0x06, 0x24, 0x5C, 0xC2, 0xD3, 0xAC, 0x62, 0x91, 0x95, 0xE4, 0x79,
    0xE7, 0xC8, 0x37, 0x6D, 0x8D, 0xD5, 0x4E, 0xA9, 0x6C, 0x56, 0xF4, 0xEA, 0x65, 0x7A, 0xAE, 0x08,
    0xBA, 0x78, 0x25, 0x2E, 0x1C, 0xA6, 0xB4, 0xC6, 0xE8, 0xDD, 0x74, 0x1F, 0x4B, 0xBD, 0x8B, 0x8A,
    0x70, 0x3E, 0xB5, 0x66, 0x48, 0x03, 0xF6, 0x0E, 0x61, 0x35, 0x57, 0xB9, 0x86, 0xC1, 0x1D, 0x9E,
    0xE1, 0xF8, 0x98, 0x11, 0x69, 0xD9, 0x8E, 0x94, 0x9B, 0x1E, 0x87, 0xE9, 0xCE, 0x55, 0x28, 0xDF,
    0x8C, 0xA1, 0x89, 0x0D, 0xBF, 0xE6, 0x42, 0x68, 0x41, 0x99, 0x2D, 0x0F, 0xB0, 0x54, 0xBB, 0x16,
)

inv_s_box = (
    0x52, 0x09, 0x6A, 0xD5, 0x30, 0x36, 0xA5, 0x38, 0xBF, 0x40, 0xA3, 0x9E, 0x81, 0xF3, 0xD7, 0xFB,
    0x7C, 0xE3, 0x39, 0x82, 0x9B, 0x2F, 0xFF, 0x87, 0x34, 0x8E, 0x43, 0x44, 0xC4, 0xDE, 0xE9, 0xCB,
    0x54, 0x7B, 0x94, 0x32, 0xA6, 0xC2, 0x23, 0x3D, 0xEE, 0x4C, 0x95, 0x0B, 0x42, 0xFA, 0xC3, 0x4E,
    0x08, 0x2E, 0xA1, 0x66, 0x28, 0xD9, 0x24, 0xB2, 0x76, 0x5B, 0xA2, 0x49, 0x6D, 0x8B, 0xD1, 0x25,
    0x72, 0xF8, 0xF6, 0x64, 0x86, 0x68, 0x98, 0x16, 0xD4, 0xA4, 0x5C, 0xCC, 0x5D, 0x65, 0xB6, 0x92,
    0x6C, 0x70, 0x48, 0x50, 0xFD, 0xED, 0xB9, 0xDA, 0x5E, 0x15, 0x46, 0x57, 0xA7, 0x8D, 0x9D, 0x84,
    0x90, 0xD8, 0xAB, 0x00, 0x8C, 0xBC, 0xD3, 0x0A, 0xF7, 0xE4, 0x58, 0x05, 0xB8, 0xB3, 0x45, 0x06,
    0xD0, 0x2C, 0x1E, 0x8F, 0xCA, 0x3F, 0x0F, 0x02, 0xC1, 0xAF, 0xBD, 0x03, 0x01, 0x13, 0x8A, 0x6B,
    0x3A, 0x91, 0x11, 0x41, 0x4F, 0x67, 0xDC, 0xEA, 0x97, 0xF2, 0xCF, 0xCE, 0xF0, 0xB4, 0xE6, 0x73,
    0x96, 0xAC, 0x74, 0x22, 0xE7, 0xAD, 0x35, 0x85, 0xE2, 0xF9, 0x37, 0xE8, 0x1C, 0x75, 0xDF, 0x6E,
    0x47, 0xF1, 0x1A, 0x71, 0x1D, 0x29, 0xC5, 0x89, 0x6F, 0xB7, 0x62, 0x0E, 0xAA, 0x18, 0xBE, 0x1B,
    0xFC, 0x56, 0x3E, 0x4B, 0xC6, 0xD2, 0x79, 0x20, 0x9A, 0xDB, 0xC0, 0xFE, 0x78, 0xCD, 0x5A, 0xF4,
    0x1F, 0xDD, 0xA8, 0x33, 0x88, 0x07, 0xC7, 0x31, 0xB1, 0x12, 0x10, 0x59, 0x27, 0x80, 0xEC, 0x5F,
    0x60, 0x51, 0x7F, 0xA9, 0x19, 0xB5, 0x4A, 0x0D, 0x2D, 0xE5, 0x7A, 0x9F, 0x93, 0xC9, 0x9C, 0xEF,
    0xA0, 0xE0, 0x3B, 0x4D, 0xAE, 0x2A, 0xF5, 0xB0, 0xC8, 0xEB, 0xBB, 0x3C, 0x83, 0x53, 0x99, 0x61,
    0x17, 0x2B, 0x04, 0x7E, 0xBA, 0x77, 0xD6, 0x26, 0xE1, 0x69, 0x14, 0x63, 0x55, 0x21, 0x0C, 0x7D,
)


def sub_bytes(s):
    for i in range(4):
        for j in range(4):
            s[i][j] = s_box[s[i][j]]


def inv_sub_bytes(s):
    for i in range(4):
        for j in range(4):
            s[i][j] = inv_s_box[s[i][j]]


def shift_rows(s):
    s[0][1], s[1][1], s[2][1], s[3][1] = s[1][1], s[2][1], s[3][1], s[0][1]
    s[0][2], s[1][2], s[2][2], s[3][2] = s[2][2], s[3][2], s[0][2], s[1][2]
    s[0][3], s[1][3], s[2][3], s[3][3] = s[3][3], s[0][3], s[1][3], s[2][3]


def inv_shift_rows(s):
    s[0][1], s[1][1], s[2][1], s[3][1] = s[3][1], s[0][1], s[1][1], s[2][1]
    s[0][2], s[1][2], s[2][2], s[3][2] = s[2][2], s[3][2], s[0][2], s[1][2]
    s[0][3], s[1][3], s[2][3], s[3][3] = s[1][3], s[2][3], s[3][3], s[0][3]

def add_round_key(s, k):
    for i in range(4):
        for j in range(4):
            s[i][j] ^= k[i][j]


# learned from https://web.archive.org/web/20100626212235/http://cs.ucsb.edu/~koc/cs178/projects/JT/aes.c
xtime = lambda a: (((a << 1) ^ 0x1B) & 0xFF) if (a & 0x80) else (a << 1)


def mix_single_column(a):
    # see Sec 4.1.2 in The Design of Rijndael
    t = a[0] ^ a[1] ^ a[2] ^ a[3]
    u = a[0]
    a[0] ^= t ^ xtime(a[0] ^ a[1])
    a[1] ^= t ^ xtime(a[1] ^ a[2])
    a[2] ^= t ^ xtime(a[2] ^ a[3])
    a[3] ^= t ^ xtime(a[3] ^ u)


def mix_columns(s):
    for i in range(4):
        mix_single_column(s[i])


def inv_mix_columns(s):
    # see Sec 4.1.3 in The Design of Rijndael
    for i in range(4):
        u = xtime(xtime(s[i][0] ^ s[i][2]))
        v = xtime(xtime(s[i][1] ^ s[i][3]))
        s[i][0] ^= u
        s[i][1] ^= v
        s[i][2] ^= u
        s[i][3] ^= v

    mix_columns(s)


r_con = (
    0x00, 0x01, 0x02, 0x04, 0x08, 0x10, 0x20, 0x40,
    0x80, 0x1B, 0x36, 0x6C, 0xD8, 0xAB, 0x4D, 0x9A,
    0x2F, 0x5E, 0xBC, 0x63, 0xC6, 0x97, 0x35, 0x6A,
    0xD4, 0xB3, 0x7D, 0xFA, 0xEF, 0xC5, 0x91, 0x39,
)


def bytes2matrix(text):
    """ Converts a 16-byte array into a 4x4 matrix.  """
    return [list(text[i:i+4]) for i in range(0, len(text), 4)]

def matrix2bytes(matrix):
    """ Converts a 4x4 matrix into a 16-byte array.  """
    return bytes(sum(matrix, []))

def xor_bytes(a, b):
    """ Returns a new byte array with the elements xor'ed. """
    return bytes(i^j for i, j in zip(a, b))

def inc_bytes(a):
    """ Returns a new byte array with the value increment by 1 """
    out = list(a)
    for i in reversed(range(len(out))):
        if out[i] == 0xFF:
            out[i] = 0
        else:
            out[i] += 1
            break
    return bytes(out)

def pad(plaintext):
    """
    Pads the given plaintext with PKCS#7 padding to a multiple of 16 bytes.
    Note that if the plaintext size is a multiple of 16,
    a whole block will be added.
    """
    padding_len = 16 - (len(plaintext) % 16)
    padding = bytes([padding_len] * padding_len)
    return plaintext + padding

def unpad(plaintext):
    """
    Removes a PKCS#7 padding, returning the unpadded text and ensuring the
    padding was correct.
    """
    padding_len = plaintext[-1]
    assert padding_len > 0
    message, padding = plaintext[:-padding_len], plaintext[-padding_len:]
    assert all(p == padding_len for p in padding)
    return message

def split_blocks(message, block_size=16, require_padding=True):
        assert len(message) % block_size == 0 or not require_padding
        return [message[i:i+16] for i in range(0, len(message), block_size)]


class AES:
    """
    Class for AES-128 encryption with CBC mode and PKCS#7.

    This is a raw implementation of AES, without key stretching or IV
    management. Unless you need that, please use `encrypt` and `decrypt`.
    """
    rounds_by_key_size = {16: 10, 24: 12, 32: 14}
    def __init__(self, master_key):
        """
        Initializes the object with a given key.
        """
        assert len(master_key) in AES.rounds_by_key_size
        self.n_rounds = AES.rounds_by_key_size[len(master_key)]
        self._key_matrices = self._expand_key(master_key)

    def _expand_key(self, master_key):
        """
        Expands and returns a list of key matrices for the given master_key.
        """
        # Initialize round keys with raw key material.
        key_columns = bytes2matrix(master_key)
        iteration_size = len(master_key) // 4

        i = 1
        while len(key_columns) < (self.n_rounds + 1) * 4:
            # Copy previous word.
            word = list(key_columns[-1])

            # Perform schedule_core once every "row".
            if len(key_columns) % iteration_size == 0:
                # Circular shift.
                word.append(word.pop(0))
                # Map to S-BOX.
                word = [s_box[b] for b in word]
                # XOR with first byte of R-CON, since the others bytes of R-CON are 0.
                word[0] ^= r_con[i]
                i += 1
            elif len(master_key) == 32 and len(key_columns) % iteration_size == 4:
                # Run word through S-box in the fourth iteration when using a
                # 256-bit key.
                word = [s_box[b] for b in word]

            # XOR with equivalent word from previous iteration.
            word = xor_bytes(word, key_columns[-iteration_size])
            key_columns.append(word)

        # Group key words in 4x4 byte matrices.
        return [key_columns[4*i : 4*(i+1)] for i in range(len(key_columns) // 4)]

    def encrypt_block(self, plaintext):
        assert len(plaintext) == 16

        plain_state = bytes2matrix(plaintext)

        add_round_key(plain_state, self._key_matrices[0])

        for i in range(1, self.n_rounds):
            sub_bytes(plain_state)
            shift_rows(plain_state)
            mix_columns(plain_state)
            add_round_key(plain_state, self._key_matrices[i])

        sub_bytes(plain_state)
        shift_rows(plain_state)
        add_round_key(plain_state, self._key_matrices[-1])

        return matrix2bytes(plain_state)

    def decrypt_block(self, ciphertext):
        assert len(ciphertext) == 16

        cipher_state = bytes2matrix(ciphertext)

        add_round_key(cipher_state, self._key_matrices[-1])
        inv_shift_rows(cipher_state)
        inv_sub_bytes(cipher_state)

        for i in range(self.n_rounds - 1, 0, -1):
            add_round_key(cipher_state, self._key_matrices[i])
            inv_mix_columns(cipher_state)
            inv_shift_rows(cipher_state)
            inv_sub_bytes(cipher_state)

        add_round_key(cipher_state, self._key_matrices[0])

        return matrix2bytes(cipher_state)

    def encrypt(self, plaintext, iv):
        assert len(iv) == 16
        plaintext = pad(plaintext)
        blocks = []
        prev_ciphertext = iv
        prev_plaintext = bytes(16)
        for plaintext_block in split_blocks(plaintext):
            ciphertext_block = self.encrypt_block(xor_bytes(plaintext_block, xor_bytes(prev_ciphertext, prev_plaintext)))
            blocks.append(ciphertext_block)
            prev_ciphertext = ciphertext_block
            prev_plaintext = plaintext_block
        return b''.join(blocks)

    def decrypt(self, ciphertext, iv):
        assert len(iv) == 16
        blocks = []
        previous = iv
        for ciphertext_block in split_blocks(ciphertext):
            blocks.append(xor_bytes(previous, self.decrypt_block(ciphertext_block)))
            previous = ciphertext_block
        return b''.join(blocks)
```
:::

Về cơ bản, ở đây challenge cho ta 3 option để tương tác với server. Khi nhìn vào source code `aes.py`, ta có thể thấy flag của chúng ta được mã hóa ở chế độ PCBC, và được giải mã ở chế độ CBC. Điều này có thể dẫn đến một lỗ hỏng bảo mật lớn mà ta sẽ khai thác.

Hãy bắt đầu thao tác với server. Khi chọn option3, server sẽ trả cho ta flag đã được mã hóa dưới chế độ **PCBC**. 
```python=
enc_pcbc = '323eaa80f217bd129db30f0e06e6691561d3221eeceedf18499f3b5ac1ffb7b5c1d9b6a9eaf806e64167b808c3fccdf653206dce0748956f2ce3aff88115ce559b2b6dcdb59bda945064edad364b6f24cda39ab0f3d9b2f8e7a81973d12c7edd8a5a8a265f0c53bdaac60ac4ec07e38094214a222b8b237eded6f426fc405139e85563e9817f2ba79561a62ac2bd71493b84c9e5684021029a49842fe4de238689920f08df775d051aee77628b84b29b1c0491a347ec15d93220d881ad15b932175041d0c74d6447e030e2d51b662c76a56f52602230fed074c22324141dc26ae6327e73286c673b509a6024c2b1e89122bc4bfd1663d930fe1395de720aa8c93edc802c8116a5428e3d31a6b4fc94580a4688ebe0dbc887400a9cd1e0e71fe9065b2ff9957cd2b9f8ff2a9f8c9c31b689bda25e417f935c1e2a1d0f5a3ff915159d3466ac51feb8ada5833afdf2528432a2ddd71a9aa30c529d8c8cd22aeec5e85245950454a8e94311e9db18284304cef50503145877b214c3f0600404858ce3d16f86cb7f4bc672f04115a911c0ea4433b3070335e31d15d661e3b445b65a02069e67fa271e1c0a330c3bc12c986218ca8fc945183797011bb3ddfc9a008cdcfff0dd68c02fd3a92d06a9158e6214432921dc27c64aabb2cb18b82cfdf9906c855c0a40342e4270025203b696141478a15eacf1bf3b671877957ad26ab932e2b1cda811340f7441eddbb46b1d1f4308d1c942949ec5b3a093e9d64769301ebc22bd07096a1d9601ab80b8551a8b58131015c3dbf1d33e7d00c8d47d14fad6bf038a9d12de3bdea4415891dbdf05834001def8585b56e005df7468d94352a5'
```
Và khi ta dùng `enc_pcbc` đưa vào hàm giải mã (option2) thì server sẽ trả về cho ta flag đã được giải mã ở mode **CBC** : 
```python=
dec_cbc = '4b4353437b50726f7061676174696e6714203a3313350030120d08021f360d0f3e0a071906022d77322f2d20420b0b0d3e191c061e063849242a2c247637011537150030120d08021f360d0f3e0a071906022d3010331f0f0a360d1c04111a360d0e2f07172d5d0e0d060d1f3a1b1c3e0a07190602291f5431300e043b063716081d360a02285b51333a0930100a001400062c013a00040602093b3c1c0e310404062c0c312c190909333c0a18090b151116271d312b1b37152d0c19110f0406113a111a3b1109361e1b1b150d1e3e030d3a07310000051b1719000c021e73280916312801090f2d18032b1e06024200041d3c051c1c18360f147128210b310f262c202d141f100c42384b3e2a0600322f2d20343200023a5c3304080d1c3a1a18300a180037290d15083e1e07000d2716301d1b002c27373a142d121f1d072a113004043e1d06113a08063a140500161800110205361a1c0027042d071a060c1e2c3e0d0d2f1c0d172b150a11290b031a10343c0b150e0a00113a1a11360c0b3006053c101c161b1701713d29000502001207171a263336263b4330280c473a3e19361d0636372b22330415051145300f31290c0d1d1b1b3e051a2514070c0b3136183a171c0a2d3900012c012c0708300a05712b3d0b330f02172c371d214030070e4d2d2c1e18000502001301016f00130c171a26041c0c310e31332c30313a141d2b21092b400c31001a0a0e1e1b1f0200362638005e0c0d1d1b1b48330a1b14070c1c313e1a093c16031d4a34103e1a153a03330005022b07141b3c1f1c000d03001303131b1b041156095c776c'
```

Nếu đọc kĩ code thì ở `enc_pcbc` nó dài hơn so với `dec_cbc` vì 16bytes đầu của `enc_pcbc` là **IV (Initialization Vector)**. Bây giờ ta sẽ phân tích để giải mã. 

Ta sẽ chia `enc_pcbc` và `dec_cbc` thành các **blockFlag** có độ dài là 16bytes. Nếu để ý kĩ thì **blockFlag** đầu tiên của `dec_cbc` là văn bản có nghĩa : `KCSC{Propagating`. Bởi vì quá trình giải mã block đầu tiên của PCBC giống với block CBC nên ta có ngay được Plaintext của **blockFlag1**.

Với **blockFlag2**, ta có thể để ý rằng : Quá trình giải mã của **CBC** chỉ thiếu đúng một bước là **XOR với plaintext đã giải mã trước đó** là thành bước giải mã của **PCBC**. Khi này, ta chỉ cần lấy : 
$$
\text{blockFlag1 } \oplus \text{dec_cbc[32:64]} = \text{blockFlag2}
$$

```python=
from pwn import xor
dec_cbc = '4b4353437b50726f7061676174696e6714203a3313350030120d08021f360d0f3e0a071906022d77322f2d20420b0b0d3e191c061e063849242a2c247637011537150030120d08021f360d0f3e0a071906022d3010331f0f0a360d1c04111a360d0e2f07172d5d0e0d060d1f3a1b1c3e0a07190602291f5431300e043b063716081d360a02285b51333a0930100a001400062c013a00040602093b3c1c0e310404062c0c312c190909333c0a18090b151116271d312b1b37152d0c19110f0406113a111a3b1109361e1b1b150d1e3e030d3a07310000051b1719000c021e73280916312801090f2d18032b1e06024200041d3c051c1c18360f147128210b310f262c202d141f100c42384b3e2a0600322f2d20343200023a5c3304080d1c3a1a18300a180037290d15083e1e07000d2716301d1b002c27373a142d121f1d072a113004043e1d06113a08063a140500161800110205361a1c0027042d071a060c1e2c3e0d0d2f1c0d172b150a11290b031a10343c0b150e0a00113a1a11360c0b3006053c101c161b1701713d29000502001207171a263336263b4330280c473a3e19361d0636372b22330415051145300f31290c0d1d1b1b3e051a2514070c0b3136183a171c0a2d3900012c012c0708300a05712b3d0b330f02172c371d214030070e4d2d2c1e18000502001301016f00130c171a26041c0c310e31332c30313a141d2b21092b400c31001a0a0e1e1b1f0200362638005e0c0d1d1b1b48330a1b14070c1c313e1a093c16031d4a34103e1a153a03330005022b07141b3c1f1c000d03001303131b1b041156095c776c'

byte1 = bytes.fromhex(dec_cbc[:32])
byte2 = bytes.fromhex(dec_cbc[32:64])
flag2 = xor(byte1, byte2)
print(flag2)
#output : b'_cipher_block_ch'
```
Và cứ lặp lại quy trình như vậy, ta có : 
```python=
from pwn import xor

enc_pcbc = '323eaa80f217bd129db30f0e06e6691561d3221eeceedf18499f3b5ac1ffb7b5c1d9b6a9eaf806e64167b808c3fccdf653206dce0748956f2ce3aff88115ce559b2b6dcdb59bda945064edad364b6f24cda39ab0f3d9b2f8e7a81973d12c7edd8a5a8a265f0c53bdaac60ac4ec07e38094214a222b8b237eded6f426fc405139e85563e9817f2ba79561a62ac2bd71493b84c9e5684021029a49842fe4de238689920f08df775d051aee77628b84b29b1c0491a347ec15d93220d881ad15b932175041d0c74d6447e030e2d51b662c76a56f52602230fed074c22324141dc26ae6327e73286c673b509a6024c2b1e89122bc4bfd1663d930fe1395de720aa8c93edc802c8116a5428e3d31a6b4fc94580a4688ebe0dbc887400a9cd1e0e71fe9065b2ff9957cd2b9f8ff2a9f8c9c31b689bda25e417f935c1e2a1d0f5a3ff915159d3466ac51feb8ada5833afdf2528432a2ddd71a9aa30c529d8c8cd22aeec5e85245950454a8e94311e9db18284304cef50503145877b214c3f0600404858ce3d16f86cb7f4bc672f04115a911c0ea4433b3070335e31d15d661e3b445b65a02069e67fa271e1c0a330c3bc12c986218ca8fc945183797011bb3ddfc9a008cdcfff0dd68c02fd3a92d06a9158e6214432921dc27c64aabb2cb18b82cfdf9906c855c0a40342e4270025203b696141478a15eacf1bf3b671877957ad26ab932e2b1cda811340f7441eddbb46b1d1f4308d1c942949ec5b3a093e9d64769301ebc22bd07096a1d9601ab80b8551a8b58131015c3dbf1d33e7d00c8d47d14fad6bf038a9d12de3bdea4415891dbdf05834001def8585b56e005df7468d94352a5'
dec_cbc = '4b4353437b50726f7061676174696e6714203a3313350030120d08021f360d0f3e0a071906022d77322f2d20420b0b0d3e191c061e063849242a2c247637011537150030120d08021f360d0f3e0a071906022d3010331f0f0a360d1c04111a360d0e2f07172d5d0e0d060d1f3a1b1c3e0a07190602291f5431300e043b063716081d360a02285b51333a0930100a001400062c013a00040602093b3c1c0e310404062c0c312c190909333c0a18090b151116271d312b1b37152d0c19110f0406113a111a3b1109361e1b1b150d1e3e030d3a07310000051b1719000c021e73280916312801090f2d18032b1e06024200041d3c051c1c18360f147128210b310f262c202d141f100c42384b3e2a0600322f2d20343200023a5c3304080d1c3a1a18300a180037290d15083e1e07000d2716301d1b002c27373a142d121f1d072a113004043e1d06113a08063a140500161800110205361a1c0027042d071a060c1e2c3e0d0d2f1c0d172b150a11290b031a10343c0b150e0a00113a1a11360c0b3006053c101c161b1701713d29000502001207171a263336263b4330280c473a3e19361d0636372b22330415051145300f31290c0d1d1b1b3e051a2514070c0b3136183a171c0a2d3900012c012c0708300a05712b3d0b330f02172c371d214030070e4d2d2c1e18000502001301016f00130c171a26041c0c310e31332c30313a141d2b21092b400c31001a0a0e1e1b1f0200362638005e0c0d1d1b1b48330a1b14070c1c313e1a093c16031d4a34103e1a153a03330005022b07141b3c1f1c000d03001303131b1b041156095c776c'

blocks = [bytes.fromhex(dec_cbc[i:i+32]) for i in range(0, len(dec_cbc), 32)]

result = blocks[0]
final_result = result  
for block in blocks[1:]:
    result = xor(result, block)
    final_result += result

print(final_result.decode(errors='ignore'))
# KCSC{Propagating_cipher_block_chaining_(PCBC)The_propagating_cipher_block_chaining_or_plaintext_cipher-block_chaining[26]_mode_was_designed_to_cause_small_changes_in_the_ciphertext_to_propagate_indefinitely_when_decrypting,_as_well_as_when_encrypting._In_PCBC_mode,_each_block_of_plaintext_is_XORed_with_both_the_previous_plaintext_block_and_the_previous_ciphertext_block_before_being_encrypted._Like_with_CBC_mode,_an_initialization_vector_is_used_in_the_first_block._Unlike_CBC,_decrypting_PCBC_with_the_incorrect_IV_(initialization_vector)_causes_all_blocks_of_plaintext_to_be_corrupt.}
```

## Cipher Feedback (CFB)
Tiếp theo là **Cipher Feedback (CFB)**. Nó khác với ECB và CBC ở chỗ : 
- Input là IV
- Sau khi IV đi qua thuật toán AES sẽ ra một thứ gọi là **[keystream](https://en.wikipedia.org/wiki/Keystream)**
- Sau khi có được **keystream** thì ta sẽ XOR nó với khối plaintext đầu tiên cho ra ciphertext đầu tiên.
- Ciphertext đầu tiên sẽ được sử dụng làm input cho mã hóa AES tiếp theo.

Hình ảnh minh họa : 
![image](https://hackmd.io/_uploads/H1-v8o0Ike.png)

Còn khi giải mã thì ta làm ngược lại : 
![image](https://hackmd.io/_uploads/r1hV_iALkg.png)

CFB có ưu điểm so với chế độ CBC là khối Plaintext không cần phải được đệm dữ liệu (padding) để phù hợp với kích thước khối của thuật toán mã hóa AES.

- Ví dụ : IV của chúng ta có độ dài là 16 bytes (128 bits), độ dài phù hợp với mã hóa AES khi chọn khóa `AES-128`. Mã hóa IV sẽ cho output có độ dài 128bits. Plaintext lúc này đã được chia nhỏ (8 bits), nên khi XOR với keystream thì sẽ cho ra ciphertext có độ dài 128bits. Độ dài này phù hợp cho quá trình mã hóa AES tiếp theo 

Ngoài ra, một ưu điểm nữa là đầu vào của chúng ta là IV, một giá trị được tạo ngẫu nhiên hoặc giả ngẫu nhiên, điều đó giúp chống lại các tấn công lặp lại (replay attack).

Tuy nhiên, CFB phụ thuộc vào cả plaintext và ciphertext trước đó. Nếu có bất kỳ lỗi nào xảy ra trong quá trình mã hóa, nó sẽ lan truyền qua các khối mã hóa sau đó. Điều này khiến CFB khó đồng bộ hóa lại nếu có lỗi hoặc mất dữ liệu, vì mỗi Ciphertext phụ thuộc vào Ciphertext trước đó. 

Code ví dụ : 
```python=
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes

key = get_random_bytes(16)  
iv = get_random_bytes(16)  
msg = "Block cipher modes of operation"

cipher_enc = AES.new(key, AES.MODE_CFB, iv)
encrypt = cipher_enc.encrypt(msg.encode())  
print(encrypt)
#b'\xe8\x82\xff\x01\xfc\xb0\xd1\x03\xb3\xc6\x1b\x16\xb4\xc2t9\x81\x88\x9cp\\\xa9\xae$\xcb\xb3\t\xb2V\x10\xca'

cipher_dec = AES.new(key, AES.MODE_CFB, iv)
decrypt = cipher_dec.decrypt(encrypt).decode()
print(decrypt)
#Block cipher modes of operation
```




## Output feedback (OFB)
**Output feedback (OFB)** cũng giống như **Cipher Feedback (CFB)**, nhưng khác ở chỗ : Input cho quá trình mã hóa tiếp theo lại là keystream của quá trình mã hóa phía trước. 

Hình ảnh minh họa : 
![image](https://hackmd.io/_uploads/HygGYSWDkx.png)

Và vì phép XOR có tính chất : $A \oplus B = B \oplus A$ nên ta có quá trình giải mã như sau: 
![image](https://hackmd.io/_uploads/HkLl5r-Dkg.png)

Nếu ở **Cipher Feedback (CFB)** quá trình mã hóa phụ thuộc vào cả plaintext và ciphertext trước đó, thì với **Output feedback (OFB)**, khối mã hóa đằng sau phụ thuộc vào keystream trước đó. Do đó, một lỗi trong quá trình mã hóa và giải mã sẽ chỉ ảnh hưởng đến khối mã hóa hiện tại và không lan truyền cho các khối sau.

Code mẫu : 
```python=
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes

key = get_random_bytes(16)  
iv = get_random_bytes(16)
msg = "Block cipher modes of operation"

cipher_enc = AES.new(key, AES.MODE_OFB, iv)
encrypt = cipher_enc.encrypt(msg.encode())  
print(encrypt)
#b'ix|@/G\xb3\x7f\xa2%\xe4\x82\xfb\x8e\xae\xd0Z\x86\x88{\xa6\x14\xde\xfd)o\x80\xd0\xa8\x92\xfa'

cipher_dec = AES.new(key, AES.MODE_OFB, iv)
decrypt = cipher_dec.decrypt(encrypt).decode()
print(decrypt)
#Block cipher modes of operation
```




## Counter (CTR)
**Counter (CTR)** cũng giống như **CFB** và **OFB** là biến mã hóa khối thành mã hóa dòng (stream cipher). Tuy nhiên, CTR khác với CFB và OFB ở chỗ là : 
- OFB, CFB sẽ sử dụng keystream hoặc là khối ciphertext trước đó làm input cho quá trình mã hóa tiếp theo.
- CTR sẽ có input riêng cho từng quá trình mã hoá. Các Input này hoàn toàn khác nhau và duy nhất

Cụ thể hơn về thành phần input của CTR : 
- **Nonce (Number Used Once)** : Là một giá trị duy nhất cho mỗi lần mã hóa, đảm bảo tính duy nhất của đầu vào cho AES. Nonce thường là ngẫu nhiên (ví dụ 8 bytes).
- **Counter (Bộ đếm)** : Là một giá trị thay đổi cho từng khối dữ liệu, thường tăng dần (increment). Counter có thể bắt đầu từ 0.

Từ hai thành phần Nonce và Counter sẽ cho ta Input có dạng:
$$
Input  = Nonce || Counter 
$$
> Tức là Chuỗi Nonce ghép liền với chuỗi Counter. Và chúng ta sẽ gọi Input là Keystream.

Hãy nhớ lại lý thuyết : **AES** sẽ mã hóa dữ liệu đầu vào có độ dài là $16bytes = 128bits$. Chính vì vậy, nếu chọn khóa `AES-128` thì Input của chúng ta sẽ có độ dài là $128bits$. Chia đều ra thì **Nonce** và **Counter** sẽ có độ dài bằng nhau và bằng $8bytes = 64bits$. 

Mô tả cách mã hóa : 
- Input sẽ là sự kết hợp giữa Nonce và Counter. Với Nonce sẽ là một giá trị ngẫu nhiên và duy nhất, trong khi Counter sẽ đảm bảo tính duy nhất cho mỗi khối dữ liệu trong một phiên mã hóa.
- Input đi qua thuật toán AES sẽ cho ra một keystream.
- XOR khối plaintext với keystream này sẽ cho ra khối ciphertext.

Hình ảnh minh họa : 
![image](https://hackmd.io/_uploads/BJ3-4IZvke.png)

Giải mã : 
![image](https://hackmd.io/_uploads/Sy_HE8Zvkl.png)

Code mẫu : 
```python=
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes
from Crypto.Util import Counter

key = get_random_bytes(16)  
iv = get_random_bytes(16)
ctr = Counter.new(128)

msg = "https://www.youtube.com/watch?v=0GeQVtZ6Rd4"

cipher_enc = AES.new(key, AES.MODE_CTR, counter=ctr)
encrypt = cipher_enc.encrypt(msg.encode())  # mã hóa
print(encrypt)
#b'\xcf\xbb\x08bO\x1bY&fx\xcd\x95\xa7(k\x91\x9d\xc4w\t\x1a\x85\x94\xaaoz\xb9P\xc4o=\x1dQg{\x80 /ym\x97\x9a\x0f'

cipher_dec = AES.new(key, AES.MODE_CTR, counter=ctr)
decrypt = cipher_dec.decrypt(encrypt).decode()
print(decrypt)
#https://www.youtube.com/watch?v=0GeQVtZ6Rd4
```

Ta có một challenge như sau : **Super Secure Encryption - ACECTF2025**
```python=
from Crypto.Cipher import AES
from Crypto.Util import Counter
import os

k = os.urandom(16) # Is it too short?

def encrypt(plaintext):
    cipher = AES.new(k, AES.MODE_CTR, counter=Counter.new(128)) # I was told, CTR can't be broken!
    ciphertext = cipher.encrypt(plaintext)
    return ciphertext.hex()

msg = b'This is just a test message and can totally be ignored.' # Just checking functionality
encrypted_msg = encrypt(msg)

with open('flag.txt', 'r') as f:
    flag = f.readline().strip().encode()

encrypted_flag = encrypt(flag)

with open('msg.txt', 'w+') as o:
    o.write(f"{encrypted_msg}\n")
    o.write(f"{encrypted_flag}")
    
'''output : 
#encrypted_msg : d71f4a2fd1f9362c21ad33c7735251d0a671185a1b90ecba27713d350611eb8179ec67ca7052aa8bad60466b83041e6c02dbfee738c2a3
#encrypted_flag : c234661fa5d63e627bef28823d052e95f65d59491580edfa1927364a5017be9445fa39986859a3'''
```
Nhìn vào Source code, ta có thể thấy ở đoạn này : 
```python=
k = os.urandom(16) # Is it too short?
cipher = AES.new(k, AES.MODE_CTR, counter=Counter.new(128)) # I was told, CTR can't be broken!
```
Một khóa AES được tạo bằng `os.urandom(16)`, điều này hoàn toàn ổn về mặt an toàn vì độ dài 16 bytes (128-bit) là đủ.

Tuy nhiên, khi tạo cipher object với `AES.new(k, AES.MODE_CTR, counter=Counter.new(128))` thì:

- Không có nonce tường minh, vì hàm `Counter.new(128)` mặc định tạo counter với giá trị ban đầu là 0.
- Mỗi khi hàm `encrypt()` được gọi, một instance mới của cipher được tạo, và counter được khởi tạo lại từ 0.

Điều này dẫn tới việc: **msg** và **flag** được mã hóa bằng cùng một keystream, vì quy trình tạo keystream bắt đầu từ 0 ở mỗi lần gọi.

Với công thức mã hóa của **CTR** là : 
$$
\text{CT} = \text{PT} \oplus \text{keystream}
$$

Vì `keystream` không đổi giữa mỗi lần mã hóa nên ta có : 
$$
\begin{cases}
\text{ct1} = \text{msg}\oplus \text{keystream}\\
\text{ct2} = \text{flag}\oplus \text{keystream}
\end{cases}
$$

$$
\Rightarrow \text{ct1}\oplus \text{ct2} = \text{msg}\oplus \text{flag}
$$

$$
\Rightarrow \text{flag}=\text{ct1}\oplus \text{ct2}\oplus \text{msg} 
$$

Với việc biết trước được `msg` nên khi này ta có thể tìm lại `flag` một cách dễ dàng.
```python=
from pwn import xor 

encrypted_msg = bytes.fromhex("d71f4a2fd1f9362c21ad33c7735251d0a671185a1b90ecba27713d350611eb8179ec67ca7052aa8bad60466b83041e6c02dbfee738c2a3")
encrypted_flag = bytes.fromhex("c234661fa5d63e627bef28823d052e95f65d59491580edfa1927364a5017be9445fa39986859a3")
msg = b'This is just a test message and can totally be ignored.'

keystream = xor(msg, encrypted_msg)
flag = xor(encrypted_flag, keystream)

print(flag)
# b'ACECTF{n07h1n6_15_53cur3_1n_7h15_w0rld}(\xf5j \xee7_\\~\x8a\x9d\x13\xa8X\x88\x18'
```




## Galois Counter Mode (GCM)

Tham khảo ở hai video này : **[VIDEO1](https://www.youtube.com/watch?v=V2TlG3JbGp0)** & **[VIDEO2](https://www.youtube.com/watch?v=C_cQIYPWTXs&t=869s)**

**[Galois Counter Mode (GCM)](https://en.wikipedia.org/wiki/Galois/Counter_Mode)** là một phiên bản nâng cấp hơn so với **AES_MODE-CTR**. Khác với thông thường như ta đã biết về CTR, GCM sẽ bổ sung thêm cho ta một chế độ mã hóa kết hợp xác thực **AEAD – Authenticated Encryption with Associated Data**, và ý nghĩa chính của nó như sau:

- Vừa mã hóa, vừa xác thực : 
    + ***Mã hóa (Encryption)***: AES‑GCM dùng cơ chế đếm (CTR mode) để biến plaintext thành ciphertext, đảm bảo kẻ tấn công không đọc được nội dung.
    + ***Xác thực (Authentication)***: Đồng thời nó tính thêm một “chữ ký” (tag) bằng **GHASH** để chắc chắn rằng ciphertext và bất kỳ dữ liệu phụ (header, metadata…) chưa bị sửa đổi.

- Bảo mật toàn diện : 
    + Giả sử nếu kẻ tấn công có thể chặn được ciphertext được trao đổi giữa hai người và họ có thể thay đổi bất kỳ bit nào của ciphertext hoặc dữ liệu phụ, khi đó người nhận sẽ chẳng thể nào biết được đó có phải là tin nhắn đúng hay không.
    + Điều đó đòi hỏi cần phải có tính xác thực (Authentication)
    + Nếu việc kiểm tra xác thực (so sánh tag) thất bại, nguời nhận hoàn toàn có thể biết được bản mã đã bị thay đổi nội dung.
    + Điều này ngăn chặn cả rủi ro “giả mạo” (tampering) lẫn rủi ro “tiết lộ” (eavesdropping).

- Vai trò của nonce (IV)
    + Mỗi lần mã hóa bắt buộc phải dùng một giá trị khởi tạo (nonce/IV) **không bao giờ trùng.**
    + Nếu vô tình tái sử dụng nonce với cùng khóa, kẻ tấn công có thể dễ dàng phá vỡ cả tính bí mật và tính toàn vẹn.


Có thể nói, AES‑GCM cho phép chúng ta mã hóa dữ liệu sao cho chỉ người có chìa khóa mới đọc được, và đồng thời bảo đảm dữ liệu đó không bị ai sửa đổi trên đường truyền. Yếu tố then chốt để đảm bảo an toàn là **không được tái sử dụng nonce.**

Ta sẽ tìm hiểu sâu hơn về cách nó **xác thực (Authentication)**

### Cơ sở toán học cần thiết
Trước khi bắt đầu, ta hãy tìm hiểu một số lý thuyết toán học liên quan trước : 
- Đầu tiên là về **Galois Field** : Trường $GF(2^{128})$ là một trường nhị phân (binary field), tức là : 
    + Một tập hợp gồm $2^{128}$ phần tử, mỗi phần tử có độ dài là 128 bits nhị phân.
    + Ta có thể gọi $a=a_0a_1...a_{127}$ là một khối nhị phân 128-bits. Khi này ta có thể biểu diễn khối này dưới dạng đa thức nhị phân như sau :
$$
a(x)=a_0+a_1x+a_2x^2+...+a_{127}x^{127}, \text{ }a_i\in [0,1]
$$

- Tiếp theo là về **Galois Field Multiplication** : Đối với trường hữu hạn $GF(2^{128})$ : 
    + Phép cộng của giữa hai đa thức nhị phân bất kì chính là phép **XOR ($\oplus$)**, hay nói dễ hiểu hơn thì đó chính là phép XOR giữa hai chuỗi nhị phân bất kì nào đó. Nó khá dễ.
    + Còn đối với phép nhân, nó sẽ hơi phức tạp hơn một xíu. Ta sẽ có một đa thức bất khả quy được  định nghĩa như sau : 
$$
f(x)=x^{128}+x^7+x^2+x+1
$$
Khi này, cho $a(x)$ và $b(x)$ là hai đa thức nhị phân bất kì. Để thực hiện phép nhân giữa hai đa thức này, ta sẽ tính như sau : 
$$
c(x)=a(x).b(x) \mod{f(x)}
$$
Và đó chính là phép tính nhân trong trường hữu hạn $GF(2^{128})$


### AAD, AEAD, MAC, GMAC, GHASH
**AAD (Additional Authenticated Data)**
- Đôi khi gọi là Associated Data, là phần dữ liệu không được mã hóa nhưng vẫn được xác thực tính toàn vẹn và nguồn gốc.
- Mục đích: bảo vệ thông tin phụ trợ (metadata, header, định tuyến…) khỏi bị giả mạo, trong khi vẫn để nguyên bản plaintext gốc dễ đọc (nếu cần).

**AEAD (Authenticated Encryption with Associated Data)**
- Về cơ bản thì đó là phương pháp mã hóa hiện đại, tích hợp luôn xác thực dữ liệu, và AAD là thành phần “dữ liệu phụ” được xác thực nhưng không mã hóa. 
- Khi dùng AEAD, bạn chỉ cần gọi một API duy nhất, truyền thêm AAD, là đã có cả tính bảo mật và toàn vẹn cho cả nội dung chính lẫn metadata.


**MAC - Message Authentication Code** 
- Được tạo ra với mục đích là đảm bảo tính toàn vẹn của dữ liệu (không bị sửa đổi) và đảm bảo tính xác thực (nguồn gốc của dữ liệu là đúng).
- Cách hoạt động:
    - MAC là một hàm toán học nhận vào thông điệp và khóa bí mật, rồi tạo ra một chuỗi mã (gọi là "tag").
    - Khi nhận dữ liệu, bên nhận dùng cùng một hàm và khóa để tạo lại tag, nếu trùng với tag gửi kèm → dữ liệu chưa bị sửa.


**GMAC - Galois Message Authentication Code** 
- Cũng giống như MAC thông thường, GMAC cũng tạo ra một chuỗi mã ("tag") để xác thực dữ liệu, nhưng khác ở chỗ là nó sẽ sử dụng các kiến thức toán học như đã nói ở trên. Ta sẽ tìm hiểu kĩ hơn về vấn đề này 
- Trước hết : 
    - Gọi $A=(A_0,A_1,...,A_v)$ là một chuỗi AAD đã được chia ra thành các block có độ dài là 128-bits. Nếu như block cuối có độ dài không phải là 16 thì có thể padding thêm các bytes $0$ vào để đảm bảo độ dài yêu cầu.
    - Gọi $L$ là độ dài của $A$ khi chưa padding 
    - Và $k$ là khóa bí mật có độ dài 128-bits được chia sẻ giữa hai bên truyền tin.

Để tính được **"tag"**, ta sẽ thực hiện các bước sau : 
- Tính $J_0=IV||0^{31}||1$, với $IV\in[0,1]^{96}$ là nonce. Tức là $J_0$ dài 128-bits 
- Tính $H=AES_k(0^{128})$, hay nói dễ hiểu hơn là mã hóa chuỗi $00...000$ dài 128-bits bằng khóa $k$.
- Đặt $f_A(x)=A_1x^{v+1}+A_2x^v+...+A_{v-1}x^3+A_vx^2+Lx\in GF(2^{128})[x]$
- **Authentication tag** $t=AES_k(J_0)\oplus f_A(H)$

Khi này, ta có thể gửi bộ ba $(IV, A, t)$ cho người nhận 

**GHASH - Galois Hash**
- **GHASH** là một hàm băm tuyến tính đặc biệt được xây dựng trên trường Galois $GF(2^{128})$.
- Nó có nhiệm vụ chính : Xử lý và kết hợp dữ liệu (plaintext, ciphertext, AAD, độ dài) → để sinh ra một giá trị gọi là digest (gọi là $X_n$), sau đó được kết hợp với AES để tạo **Authentication tag**.
- Dữ liệu đầu vào cho **GHASH** gồm:
    - AAD (dữ liệu xác thực bổ sung)
    - Ciphertext (trong GCM) hoặc rỗng (trong GMAC)
    - Độ dài của AAD và ciphertext gộp lại (mỗi cái 64-bit, tổng 128-bit)
- Khi tính được GHASH, ta sẽ đem chúng XOR với $AES_k(J_0)$ để ra **tag**

Về cơ bản thì nó cũng na ná như **GMAC**, có điều là ở đây **GHASH** sẽ nhận thêm đầu vào là ciphertext, cùng với đó là **len(AAD) || len(pt)**. Chính vì vậy ta sẽ có một số thay đổi nhỏ như sau : 
- $L=len(AAD)||len(pt)$ - nếu mã hóa
- $f_{A,C}(x) = A_1 x^{u+v+1} + A_2 x^{u+v} + \cdots + A_x x^{u+2} + C_1 x^{u+1} + C_2 x^u + \cdots + C_u x^2 + Lx$

Sau khi đã biết về các khái niệm liên quan đến Authentication, cũng như là Authentication tag, ta sẽ bắt đầu tìm hiểu về cách **AES_MODE-GCM** mã hóa


Ta có sơ đồ mã hóa như sau : 
![{8BEEA703-45E3-4598-923D-ED42BD57AE08}](https://hackmd.io/_uploads/rJUSw5-yxx.png)

Một sơ đồ khá trực quan và đầy đủ, trong đó : 
- $E_k$ là hàm encryption AES với key
- PT là plaintext
- AD là Additional Data, cái này không quan trọng lắm nhưng mà nó cũng sẽ ảnh hưởng tới tag. (Challenge dưới sẽ không dùng tới cái này).

Xét một ví dụ sau : Một challenge từ giải **UTCTF 2020**
```python=
import sys
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes


KEY = get_random_bytes(16)
NONCE = get_random_bytes(16)
flag = b'utflag{6cm_f0rb1dd3n_4774ck_777}'

def aes_gcm_encrypt(plaintext):
    cipher = AES.new(KEY, AES.MODE_GCM, nonce=NONCE)
    ciphertext, tag = cipher.encrypt_and_digest(plaintext)
    return ciphertext.hex(), tag.hex()


def aes_gcm_decrypt(ciphertext, tag):
    cipher = AES.new(KEY, AES.MODE_GCM, nonce=NONCE)
    plaintext = cipher.decrypt_and_verify(ciphertext, tag)
    return plaintext


if __name__ == '__main__':
    options = '''Welcome to the AES GCM encryption and decryption tool!
        1. Encrypt message
        2. Decrypt message
        3. Quit
    '''

    def encrypt_msg():
        print("Input a string to encrypt (must be at least 32 characters):")
        user_input = input()
        if len(user_input) < 32:
            sys.exit()
        output = aes_gcm_encrypt(user_input.encode())
        print("Here is your encrypted string & tag, have a nice day :)")
        print(output)


    def decrypt_msg():
        print("Input a hex string and its tag to decrypt:")
        user_input = bytearray.fromhex(input())
        tag = bytearray.fromhex(input())
        try:
            output = aes_gcm_decrypt(user_input, tag)
        except ValueError:
            print("Decryption failed :(")
            return
        print("Here is your decrypted string, have a nice day :)")
        print(output)


    def quit():
        sys.exit()

    menu = {
        '1' : encrypt_msg,
        '2' : decrypt_msg,
        '3' : quit
    }
    
    i = 0
    print('flag', aes_gcm_encrypt(flag)[0])
    while i < 10:
        print(options)
        print('Select option: ')
        choice = input()
        if choice not in menu.keys():
            print("Not a valid choice...")
            sys.exit()
        menu[choice]()
        i += 1
```
Về cơ bản thì flag của ta đã được mã hóa theo **AES_MODE-GCM** có trả về "tag" đầy đủ. Tuy nhiên, ở challenge này không yêu cầu ta phải gửi "tag" hay gì cả, chỉ cần gửi Plaintext dài ít nhất 32 kí tự là được. Server sẽ trả về cho ta `enc_flag` và `enc_msg`. 

Tưởng sẽ chẳng có vấn đề gì nhưng nó lại mắc sai lầm y chang như challenge ví dụ của CTR mà mình có ghi ở trên. Đó là **keystream** không đổi trong suốt quá trình mã hóa các block. 

Và thế là ta hòan toàn có thể áp dụng lại cách cũ đển tấn công được rồi!!
```python=
from pwn import xor
msg = b''
enc_msg = bytes.fromhex('')
enc_flag = bytes.fromhex('')

print("Flag is :", xor(msg, enc_msg, enc_flag))
# các giá trị trên các bạn tự lấy nhé :>
```



# Attack
## Meet in the middle
[**Meet in the middle**](https://en.wikipedia.org/wiki/Meet-in-the-middle_attack) là một kỹ thuật tấn công mật mã nhằm phá vỡ các thuật toán mã hóa có cấu trúc lặp hoặc kết hợp nhiều lớp mã hóa, chẳng hạn như Double DES. Phương pháp này khai thác việc có một điểm trung gian giữa quá trình mã hóa và giải mã, nơi mà kẻ tấn công có thể thực hiện việc tính toán từ hai hướng (mã hóa và giải mã) và so sánh kết quả để tìm khóa.

Meet in the middle có thể tấn công cả 2DES và 3DES hoặc kể cả AES nếu nó sử dụng **CHẴN** lần mã hóa, tuy nhiên 3DES sử dụng ba khóa và mã hóa ba lần, làm cho việc áp dụng tấn công phức tạp hơn và ít hiệu quả hơn so với 2DES. Do đó 3DES được coi là an toàn hơn trước các tấn công này. 

Chúng ta sẽ nghiên cứu về cách tấn công **Meet in the middle** đối với 2DES.

Ta có công thức mã hóa và giải mã 2DES như sau : 
$$
C = E_{K_2}(E_{K_1}(P))
$$

$$
P = D_{K_1}(D_{K_2}(C))
$$

Đầu tiên, ta sẽ giải mã $C$ lần thứ nhất với khóa $K_2$ : 
$$
D_{K_2}(C) = D_{K_2}(E_{K_2}(E_{K_1}(P))) = E_{K_1}(P)
$$

Khi này ta sẽ brute-force $K_1$ rồi thêm các $E_{K_1}(P)$ vào 1 list, sau đó brute-force $K_2$. Nếu như có một giá trị nào của $D_{K_2}(C)$ nằm trong list thì ta đã tìm được $K_2$.
- Khi brute-force $K_1$ thì ta cần $2^{56}$ bước, tương tự với $K_2$ là $2^{56}$ bước.
- Nếu không sử dụng MITM, khi này chúng ta sẽ phải brute-force lên tới $2^{112}$ bước, trong khi ta chỉ cần có $2 \times 2^{56} = 2^{57}$ bước brute-force khi dùng MITM.

Xét ví dụ sau : 
```python=
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad
from hashlib import md5
from os import urandom

FLAG = b"KCSC{???????????????????????????}"
assert len(FLAG) % 16 == 1 # hint

key1 = md5(urandom(3)).digest()
key2 = md5(urandom(3)).digest()
cipher1 = AES.new(key1, AES.MODE_ECB)
cipher2 = AES.new(key2,AES.MODE_ECB)

enc = cipher1.encrypt(pad(FLAG,16))
enc = cipher2.encrypt(enc)

print(enc.hex())

# 21477fac54cb5a246cb1434a1e39d7b34b91e5c135cd555d678f5c01b2357adc0c6205c3a4e3a8e6fb37c927de0eec95
```

Ở đây flag của ta được mã hóa hai lần **AES_MODE-ECB** với khóa có độ dài là `128 bits`.

Khóa của ta được tạo nên từ `md5(urandom(3)).digest()`, chỉ có 3 bytes nên không gian của khóa có thể brute-force được sẽ là : 
$$
2^{8\times 3}\times 2^{8\times 3}=2^{8\times 3\times 2}=2^{48}=281.474.976.710.656 \text{ khóa}
$$

Đây là một không gian khóa rất là lớn, việc brute-force trở nên bất khả thi. 

Có thể thấy, đây là một dạng mã hóa tương đương **2DES**, nhưng ở đây là dạng mã hóa **AES_MODE-ECB** được mã hóa hai lần. Điều đó là ta nghĩ ngay đến kiểu tấn công MITM ở trên (vì nó được mã hóa **CHẴN** lần)

Đề có cho ta `hint` của bài là :  
```python=
assert len(FLAG) % 16 == 1 # hint
```

Điều đó cho thấy `len(FLAG)` sẽ bị thừa ra **một byte** khi chia từng khúc dài **16 bytes** và ta đều có thể đoán được đó là byte `"}"`. Khi mã hóa lần đầu tiên, flag của ta sẽ được padding thêm **15 bytes** nữa cho đủ thành một khối dài **16 bytes** như ở dòng này : 
```python=
enc = cipher1.encrypt(pad(FLAG,16))
```
Ta biết được `len(enc) = 48 bytes`, trừ đi **15 bytes** padding nữa là `len(FLAG) = 33 bytes`. Khi này ta sẽ có flag có cấu trúc như sau : `flag = "KCSC{...." + "}\x0f\x0f..."`

Ta không biết được khối flag đầu tiên, nhưng ta biết được khối cuối cùng là `}\x0f\x0f...` (được padding theo chuẩn **PKCS#7**)

Như vậy, bây giờ ta sẽ brute-force khóa `key1` và mã hoá khối này, lưu tất cả vào một list, rồi sau đó sẽ brute-force `key2` để giải mã khối `enc[-16:]`. Nếu như : 
$$
D_{K_2}(C)=E_{K_1}(P)
$$

thì ta sẽ có được hai khóa `key1`, `key2`. Như vậy là giải mã được rồi!!

```python=
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad
from hashlib import md5
from os import urandom
from tqdm import trange

enc = bytes.fromhex("21477fac54cb5a246cb1434a1e39d7b34b91e5c135cd555d678f5c01b2357adc0c6205c3a4e3a8e6fb37c927de0eec95")
block2_enc = enc[-16:]
block2_know = b"}" + b'\x0f'*15
list = {}
key1 = b""
key2 = b""

#encrypt block2_know 
for test in trange(2**24):
    test = test.to_bytes(3, byteorder='big')
    key = md5(test).digest()
    cipher = AES.new(key, AES.MODE_ECB)
    encrypt_block2_know = cipher.encrypt(block2_know)
    list[encrypt_block2_know] = key


#decrypt block2_enc
for test in trange(2**24):
    test = test.to_bytes(3, byteorder='big')
    key = md5(test).digest()
    cipher = AES.new(key, AES.MODE_ECB)
    decrypt_block2_enc = cipher.decrypt(block2_enc)
    if decrypt_block2_enc in list:
        key1 = list[decrypt_block2_enc]
        key2 = key
        print("key1 :", key1)
        print("key2 :", key2)
        break
```
Có được `key1` và `key2` là ta có thể giải mã được rồi
```python=
from Crypto.Cipher import AES

enc = bytes.fromhex("21477fac54cb5a246cb1434a1e39d7b34b91e5c135cd555d678f5c01b2357adc0c6205c3a4e3a8e6fb37c927de0eec95")
key1 = b'/\xc5\xe63%\xac\x93\xc1\xaf\xd3\x94\xe5\n\xd3\xf3I'
key2 = b'\x8f\x06\x88\x17\x01\xd9\xd9j\xf5F\xe6\x08_z\xf4\xb1'
cipher1 = AES.new(key1, AES.MODE_ECB)
cipher2 = AES.new(key2, AES.MODE_ECB)
flag = cipher2.decrypt(enc)
flag = cipher1.decrypt(flag)
print(flag)
# b'KCSC{MeEt_In_tHe_mIdDLe_AttaCk__}\x0f\x0f\x0f\x0f\x0f\x0f\x0f\x0f\x0f\x0f\x0f\x0f\x0f\x0f\x0f'
```


## CBC Padding Oracle
Padding Oracle là một kiểu tấn công vào hệ thống mã hóa sử dụng chế độ CBC (Cipher Block Chaining), với mục tiêu khai thác lỗi trong việc xử lý padding của dữ liệu mã hóa. Tấn công này không cần biết khóa mã hóa mà chỉ cần hệ thống phản hồi liệu padding của dữ liệu sau khi giải mã có hợp lệ hay không.

Video về Padding Oracle : **[Video1](https://www.youtube.com/watch?v=lkPBTJ3yiCI)** và **[Video2](https://www.youtube.com/watch?v=uDHo-UAM6_4)**

Ở đây mình sẽ sử dụng **[trang web](https://paddingoracle.github.io/)** này để mô tả, các bạn có thể xem video2 để hiểu hơn ( nếu hiểu t.anh :)) ).

Khi vào trang web các bạn sẽ thấy được màn hình này : 
![{174A3E67-13DE-46E6-8B1F-E5DA9AF6D755}](https://hackmd.io/_uploads/r173wdgKyl.png)


Ở đây ta có được 2 thứ là Ciphertext và IV. Thêm vào đó là dòng chữ **Padding is Valid**. Điều đó có nghĩa Plaintext của ta đã được Pad thêm các byte để đảm bảo độ dài là bội của 16.
![{F5245BB6-56CB-4E41-8F63-CF8DE251B15C}](https://hackmd.io/_uploads/B1T0PuxKJl.png)

Đây là Plaintext của chúng ta. Có thể thấy ở những byte cuối của Plaintext toàn là byte `05`, đó chính là những byte đã padding vào cho phù hợp với độ dài yêu cầu. Bây giờ ta sẽ xem như không biết gì về DecryptedText và Plaintext để hiểu rõ hơn về các hoạt động của Padding Oracle. 

Đâu tiên ta sẽ biến đổi $IV$ ban đầu thành $IV$ mới, gọi là $IV'$.
![{8FB6D0BD-96DA-4DC8-92A3-D730D822A248}](https://hackmd.io/_uploads/HyP4u_lYye.png)

Ở đây ta có một "IV rỗng" và màn hình báo là **Padding is Invalid**. Ta sẽ bắt đầu với byte cuối cùng của plaintext, đặt là `p16`. Đầu tiên ta sẽ brute-force byte cuối cùng của $IV'$ sao cho : Một byte nào đó của $IV'$ XOR với byte cuối cùng (`d16`) của DecryptedText ra byte `01` thì thôi.
![{407C673A-1DBB-4606-A210-57C8D9D2DEA9}](https://hackmd.io/_uploads/S1J0__lYyx.png)


Ở đây ta có byte `3b` thì hệ thống hiện **Padding is valid**. Từ byte `3b`, ta XOR với byte `01` là sẽ ra được byte cuối của DecryptedText `d16` ($d16 = 01 \oplus 3b = 3a$)

Từ byte `3a` ta sẽ tính được byte cuối của Plaintext bằng cách lấy `3a` XOR với byte cuối của $IV$ ban đầu là `3f` với : $p16 = 3a \oplus 3f = 05$. Đối chiếu với kết quả bên trên thì ta đã đúng. (`p16` = `05`)

Bây giờ ta sẽ đi tìm byte kế tiếp của Plaintext. Để tìm được thì ta sẽ tiếp tục lợi dụng thông báo **Padding is Valid** để cho 2 byte cuối của Plaintext `p15` và `p16` là byte `02`, rồi từ đó tìm được `p15`.

Để `p16` là `02` thì ta phải thay đổi byte cuối của $IV'$. Bởi vì khi này byte cuối của $IV'$ là `3b`, khi đem XOR với `d16` thì ra byte `01`, không phải là `02` nên không nhận. 

Tính byte cuối của $IV'$ thì ta sẽ thế $p16 = 02$ và lấy $02 \oplus 3a = 38$ là ta sẽ có byte cuối của $IV'$ là `38`. Và giờ tìm byte kế tiếp của $IV'$ thì ta cũng sẽ brute-force như hồi nãy.

![{2053CF5E-031A-4296-A4AF-574536273F2B}](https://hackmd.io/_uploads/By1OCOlF1e.png)

Ta có byte thứ 15 của $IV'$ là `18`, từ đây ta tính được byte `d15` và `p15` theo như cách tính bên trên : 
$$
d15 = 18 \oplus 02 = 1a
$$

$$
p15 = 1a \oplus 1f = 05
$$

Và bây giờ để tính tiếp byte `p14` ta cũng sẽ làm tương tự : tìm 3 byte cuối của $IV'$ sao cho khi $\oplus$ với 3 byte cuối của **DecryptedText** ra được 3 byte `03`.

![{2D365A63-4172-4060-96F1-EF1349D3BBE3}](https://hackmd.io/_uploads/r1LONKeK1l.png)
$$
d14 = 68 \oplus 03 = 6b
$$

$$
p14 = 6b \oplus 6e = 05
$$

Và cứ như thế cho đến cuối cùng ta có kết quả sau : 
![{16302C2A-378D-428F-BB0F-950D05CA36DB}](https://hackmd.io/_uploads/rybhrKxFJe.png)

Dưới đây là một ví dụ về kiểu tấn công này : Challenge **Paper Plane - CryptoHack**

![{BEC4C461-E334-4C7A-8F00-EDAF5233F3D7}](https://hackmd.io/_uploads/H1rij1YCJe.png)
![{EAF38FDE-9E77-475A-94D1-F4E5A830D055}](https://hackmd.io/_uploads/B1iB31FCkg.png)

Vì note này chỉ dùng để ghi lý thuyết nên mình sẽ viết wu bài này bên note khác nha (tại tới giới hạn của HackMD rồi :smile:)


## Chosen-plaintext attack (CPA)
Chosen-plaintext attack (CPA) là một kiểu tấn công trong mật mã học, trong đó kẻ tấn công có khả năng chọn các bản rõ (plaintext) tùy ý và sau đó nhận được các bản mã (ciphertext) tương ứng do hệ thống mã hóa tạo ra. Mục tiêu của CPA là tìm hiểu cách hoạt động của thuật toán mã hóa, từ đó có thể suy ra khóa mã hóa, giải mã các bản mã khác, hoặc phá vỡ tính bảo mật của hệ thống.

**CPA** trong **AES** có thể tồn tại ở các **Block cipher modes of operation** khác nhau như : **ECB**, **CBC**,... Dưới đây là một vài dạng tấn công kiểu này : 
### ECB Padding Attack (Byte-at-a-time)
Như ta đã biết thì **AES_MODE-ECB** vốn là một chế độ mã hóa yếu của AES. Vì sao nó yếu ư? Vì nếu một khối dữ liệu giống nhau xuất hiện nhiều lần trong văn bản gốc, chúng sẽ được mã hóa thành cùng một khối Ciphertext giống nhau, mà không có sự thay đổi nào. Điều này làm cho chế độ ECB có thể dễ bị tấn công bởi những kẻ tấn công có khả năng phân tích các mẫu trong văn bản mã hóa.

- **[ECB Padding Attack ](https://exploit-notes.hdks.org/exploit/cryptography/algorithm/aes-ecb-padding-attack/)** là một loại tấn công nhắm vào chế độ ECB, khi mà chế độ này có sự yêu cầu bắt buộc về kích thước của khối văn bản đầu vào. Thông thường sẽ là một khối văn bản có độ dài là **16bytes**.
- Khi một khối văn bản không đảm bảo đúng độ dài yêu cầu thì sẽ có sự bổ sung dữ liệu (gọi là padding) để đảm bảo rằng độ dài dữ liệu đầu vào là bội số của kích thước khối mã hóa. (**16 bytes**)
- **ECB Padding Attack** thường xảy ra khi một kẻ tấn công có thể thay đổi các khối Ciphertext mà không biết rõ nội dung của văn bản gốc, nhưng có thể tận dụng các sự thay đổi trong dữ liệu được mã hóa để đoán các giá trị của văn bản gốc hoặc làm lộ thông tin.


Tìm hiểu lại về ECB : 
- Hãy giả sử ta có một đoạn văn bản cần được mã hóa là `aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa`. Đoạn văn bản này có độ dài là **32 bytes**, nên khi mã hóa bằng ECB, ta có thể dễ dàng chia đoạn văn bản này ra làm 2 phần có độ dài bằng nhau và bằng **16 bytes**. 
- Tuy nhiên, nếu như độ dài của nó chỉ có **31 bytes** thôi thì sao??? `aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa`. Khi này muốn mã hóa nó bằng ECB thì ta sẽ phải Padding thêm **1 byte** vào để đủ độ dài **32 bytes** rồi mới mã hóa được. Ở đây mình sẽ thêm vào byte `\x01` và ta có `aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa\x01`. Như vậy là có thể mã hóa được rồi. 

Được rồi, đó là hình dung lại các ECB hoạt động. Bây giờ ta sẽ vào phần chính : **How to attack?**. Cho source code : 
```python=
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad

key = b'unknown' 
FLAG = b'crypto{ECB}' #hãy giả sử rằng len(flag) = len("crypto{ECB}") = 11

def encrypt(plaintext):
	padded = pad(plaintext + FLAG, 16)
	cipher = AES.new(key, AES.MODE_ECB)

	ciphertext = cipher.encrypt(padded)
	return ciphertext.hex()

plaintext = input()
print(encrypt(plaintext))
```
Nhìn vào Source code ta có thể thấy rằng : Khi ta nhập vào một **Input (Plaintext)** bất kì nào đó thì nó sẽ được ghép chung với Flag của chúng ta, rồi sẽ được đem đi mã hóa ECB, trả về cho ta một Ciphertext của 2 thứ là $Input + Flag$.

Nếu như Input nhập vào $+$ với Flag mà không đủ độ dài là bội của **16 bytes** thì chúng sẽ được Padding thêm. 

**Phân tích :**

Bây giờ ta chưa biết flag của chúng ta trông như nào thì mình tạm lấy ví dụ flag là : `crypto{ECB}`

Khi ta nhập vào một Input nào đó thì nó sẽ được ghép với Flag, đồng thời nếu như độ dài của chuỗi mới không phải là bội của 16 thì sẽ tự động Padding thêm một vài kí tự sao cho đúng với độ dài yêu cầu. 

Ví dụ mình nhập vào **16 bytes** ">" này đi. Khi này ta có : `>>>>>>>>>>>>>>>>crypto{ECB}`

Tách nó ra làm các khối có độ dài **16 bytes** ta có : 
`>>>>>>>>>>>>>>>>` `crypto{ECB}`

Khi này do khối thứ 2 chỉ dài **11 bytes** nên ta sẽ Padding thêm **5 bytes** bất kì nữa vào. Ta sẽ có : `>>>>>>>>>>>>>>>>` `crypto{ECB}11111`

Đó là cách hoạt động của ECB, vậy nếu như mình **thay đổi một chút về Input** như : 
![{F688ADCF-9FC6-4FEB-B170-255678A57854}](https://hackmd.io/_uploads/SJtFUDmFyg.png)

>Ở đây thay vì mình ghi thành dòng thì mình sẽ xếp nó thành một khối 3 dòng, 1 dòng như vậy sẽ là một khối dữ liệu dài **16 bytes**. 

Ta có thấy trong khối này có 3 thứ : 
- Input : `>>>>>>>>>crypto{>>>>>>>>`
- Flag : `crypto{ECB}`
- Padding : `1111111111111`

Có thể thấy rằng, dòng 1 và dòng 2 của chúng ta khá là giống nhau, chỉ khác nhau ở chữ $E$. Vậy nếu như bây giờ ta thay đổi Input lại thêm lần nữa thành : 
![{0C0968C9-46FE-46B6-B3A5-A2782CEB0252}](https://hackmd.io/_uploads/ryDiLD7t1e.png)

Byte **"?"** ở đây là byte mà ta sẽ nhập vào và kiểm tra xem ở byte nào thì dòng 1 sẽ bằng dòng 2. Tức là Brute-force byte **"?"** cho tới khi nào khối 1 = khối 2 
>thay từ "dòng" thành từ "khối" cho giống với AES :))

Khi mà tìm thấy được byte **"?"** sao cho khối 1 = khối 2, ta sẽ giữ byte đó lại (tức là + byte đó vào input của chúng ta)

Sau khi Brute-force như vậy, ta sẽ **dịch trái toàn bộ khối**, tức là thay đổi Input để thực hiện việc Brute-force tiếp theo : 
![{6D72461C-50B9-4A32-95E0-B0A4195078E1}](https://hackmd.io/_uploads/HJGjxi7YJe.png)


Cứ như thế, cho đến khi nào không thể tìm ra được thêm kí tự nào trong Flag nữa thì sẽ dừng lại và in ra Flag. 
![{62F70417-89D6-40ED-9622-53D384BFDEBF}](https://hackmd.io/_uploads/HJ0EWjmF1g.png)

Tới đây là có vẻ ổn rồi đấy. Thế nhưng có một vấn đề được đặt ra thế này : Nếu như Flag của chúng ta có độ dài bằng **16 bytes** hoặc hơn **16 bytes** thì sao ???

Thật ra, các bytes **">"** tồn tại như vậy là có mục đích của nó. Việc ta nhập các bytes này mục đích là để tạo ra khoảng trống đủ giữa flag và byte mà ta sẽ brute-force. Hãy nhìn vào khối này : 
![{C9B3FD78-1591-4617-A322-6212231F393F}](https://hackmd.io/_uploads/BkgG0o7tJl.png)

Flag của chúng ta là `crypto{abcdefgh}` có độ dài là **16 bytes**. Khi ta brute-force bytes thì ta sẽ có : 
![{199D2DFD-4A00-450D-BD61-E657D73034B9}](https://hackmd.io/_uploads/ryuFAjQK1x.png)

Nó có vẻ chả có vấn đề gì cả. Cả 2 khối đều bằng nhau và bằng đúng **16 bytes**. Thế nhưng, giả sử flag của chúng ta là : `crypto{ECBmakesmecry}` có độ dài **21 bytes**, ta sẽ có khối dữ liệu như sau : 
![{66567AC6-C27D-4418-9238-D795761DC467}](https://hackmd.io/_uploads/BkC-k27Ykx.png)

Vẫn là cách làm như trên, ta sẽ có : 
![{2F8C5339-6BFC-4D55-A4E6-D010176E646F}](https://hackmd.io/_uploads/B16vy2QFyx.png)

Có thể thấy rằng, khi flag dài hơn **16 bytes** ta không thể brute-force tiếp được vì khối 1 không thể lùi thêm được nữa, trong khi đó vẫn còn một phần của flag nằm ở khối 3. Điều đó đã cho ta thấy ý nghĩa tồn tại cuả byte **">"**. Chính vì vậy ta cần phải thay đổi Input lại sao cho có một khoảng trống đủ nhiều giữa 2 khối brute-force và flag. 
![{B7E8B21B-CF59-47EC-AC8F-DFD9C7AE99BC}](https://hackmd.io/_uploads/HkBZM3XKye.png)

Như vậy là có đủ khoảng trống để ta brute-force, việc còn lại là brute tới chếc thôi.
![{63C36D84-DC15-4948-BE39-E76B03D5D7AA}](https://hackmd.io/_uploads/HJEnzh7YJl.png)
```python=
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad
import os
import string

key = os.urandom(16) 
FLAG = b'crypto{ECB}' 
plaintext = b'\xaa' * 16 


def encrypt(plaintext):
	padded = pad(plaintext + FLAG, 16)
	cipher = AES.new(key, AES.MODE_ECB)

	ciphertext = cipher.encrypt(padded)
	return ciphertext.hex()

def bruteforce():
    flag = ''
    total = 32 - 1
    chars = string.ascii_letters + string.digits + string.punctuation
    
    while True:
        payload = '1' * (total - len(flag))
        ciphertext_1 = encrypt(payload.encode())
        
        for c in chars:
            ciphertext_2 = encrypt((payload + flag + c).encode())
            # Compare the middle blocks ([32:64]) of each encrypted text
            if ciphertext_2[32:64] == ciphertext_1[32:64]:
                flag += c
                print(flag)
                break
        else:
            break

bruteforce()
```




## Key-Recovery Attacks on GCM with Repeated Nonces

Có một vấn đề là ta không bao giờ được phép sử dụng lại những **nonces** mà được dùng đề tạo ra **"tag"** hoặc là mã hóa, bởi lẽ việc dùng lại những **nonce** đó vô hình chung sẽ tạo ra những **keystream** giống nhau dẫn đến việc : 
$$
\begin{cases}
ct1=pt1\oplus keystream\\
ct2=pt2\oplus keystream
\end{cases}
$$

$$
\Rightarrow ct1\oplus ct2=pt1\oplus pt2
$$

Ta sẽ có thể dễ dàng khôi phục lại Flag nếu như ta biết được `enc_flag`, `chosen_Pt`, `chosen_ct`. Điều đó đã được thể hiện rõ ở hai ví dụ trong phần **Block cipher modes of operation**

Ngoài ra, bên cạnh việc mã hóa có thể bị phá thì nó còn gây mất bảo mật về mặt xác thực (Authentication) và phục hồi khóa con $H$ cho hàm băm **GHASH** : 
- Ta có công thức tính **"tag"** là : $t=AES_k(J_0)\oplus f_{A,C}(H)$
- Khi nonce bị tái sử dụng giữa hai Plaintext $(A_1, C_1, T_1)$ và $(A_2, C_2, T_2)$, ta biết rằng giá trị $AES_k(J_0)$ là không đổi nên khi thực hiện phép XOR ta có : 
$$
t_1\oplus t_2=f_{A_1,C_1}(H)\oplus f_{A_2,C_2}(H)
$$

Phương trình này trở thành một đa thức tuyến tính theo $H$, và kẻ tấn công có thể thu thập đủ các đa thức như vậy để giải tìm được $H$

Hậu quả của việc bị lộ $H$ là rất nghiêm trọng. Ta biết rằng $H=AES_k(0^{128})$, với khóa $k$ là bí mật, kẻ tấn công có thể lợi dụng việc bị lộ $H$ để tạo ra các **"tag"** giả mạo với công thức tính "tag" ở trên, từ đó khiến cho tính xác thực (Authentication) trở nên vô dụng. 

Ta có một ví dụ như sau : 
```python=
import sys
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes
flag = b"utflag{6cm_f0rb1dd3n_4774ck_777}"

KEY = get_random_bytes(16)
NONCE = get_random_bytes(16)

admin = b'iamadministrator'

def aes_gcm_encrypt_admin(plaintext):
    cipher = AES.new(KEY, AES.MODE_GCM, nonce=NONCE)
    ciphertext, tag = cipher.encrypt_and_digest(plaintext)
    return ciphertext.hex()

def aes_gcm_encrypt(plaintext):
    cipher = AES.new(KEY, AES.MODE_GCM, nonce=NONCE)
    ciphertext, tag = cipher.encrypt_and_digest(plaintext)
    return ciphertext.hex(), tag.hex()


def aes_gcm_decrypt(ciphertext, tag):
    cipher = AES.new(KEY, AES.MODE_GCM, nonce=NONCE)
    plaintext = cipher.decrypt_and_verify(ciphertext, tag)
    return plaintext


if __name__ == '__main__':
    options = '''Welcome to the AES GCM encryption and decryption tool!
        1. Encrypt message
        2. Decrypt message
        3. Quit
    '''

    def encrypt_msg():
        print("Input a plaintext (hex)")
        user_input = bytes.fromhex(input())
        if b"admin" in user_input:
            print(aes_gcm_encrypt_admin(user_input))
        else:
            output = aes_gcm_encrypt(user_input)
            print("Here is your encrypted string & tag, have a nice day :)")
            print(output)


    def decrypt_msg():
        print("Input a hex string and its tag to decrypt:")
        user_input = bytearray.fromhex(input())
        tag = bytearray.fromhex(input())

        try:
            output = aes_gcm_decrypt(user_input, tag)
            if admin in output:
                print(flag)
                return
        except ValueError:
            print("Decryption failed :(")
            return
        print("Here is your decrypted string, have a nice day :)")
        print(output)


    def quit():
        sys.exit()

    menu = {
        '1' : encrypt_msg,
        '2' : decrypt_msg,
        '3' : quit
    }
    
    i = 0
    while i < 10:
        print(options)
        print('Select option: ')
        choice = input()
        if choice not in menu.keys():
            print("Not a valid choice...")
            sys.exit()
        menu[choice]()
        i += 1
```

Về cơ bản nó là phiên bản nâng cấp của ví dụ bên trên khi mà lần này nó sẽ không cho ta `enc_flag` nữa, mà thay vào đó ta phải nhập `user + tag` của admin thì nó mới cho ta lại Flag.

Vẫn là theo một cách cũ, **nonce** của chúng ta lại được xài lại trong các quá trình mã hóa. 

Chúng ta sẽ thực hiên cách tấn công như ý tưởng ở bên trên. Nếu như bạn vẫn còn chưa thể hiểu hết cách tấn công này thì các bạn có thể tham khảo thêm **[ở đây](https://toadstyle.org/cryptopals/63.txt)**.

Mình sẽ dựa vào file đó để mô tả lại thử cách tấn công này : 
- Ta có : $H=AES_k(0^{128})$
- Đệm các byte 0 cho AD và Ciphertext sao cho chia hết cho chiều dài khối thì ta sẽ thu được 1 đoạn như thế này `AD0 || AD1 || C0 || C1 || C2`
- Ngoài ra thì ta sẽ thêm phải có cả `len(AD)||len(PT)` nữa, thế nên tổng dữ liệu của ta sẽ như sau: `AD0 || AD1 || C0 || C1 || C2 || len(AD)||len(PT)`
- Từ $H$ và lần lượt các khối data ở trên, ta sẽ có hàm băm **GHASH** với thuật toán sau : 
```python=
g := 0
for b in bs:
    g := g + b
    g := g * h
```
- Tạo một giá trị $s=AES_k(nonce^{92}||1)$
- Khi đó ta sẽ có **tag** là : $t=g+s$


Tag của chúng ta sẽ đuợc biểu diễn dưới dạng đa thức sau : 
$$
t_1=(((((((((((H * AD0) + AD1) * H) + C0) * H) + C1) * H) + C2) * H) + len) * H) + s
$$

$$
\Leftrightarrow t_1=AD0.H^6+AD1.H^5+C0.H^4+C1.H^3+C2.H^2+len.H+s
$$

Đặt $f(x)=AD0.x^6+AD1.x^5+C0.x^4+C1.x^3+C2.x^2+len.x+s$, với $t=f(H)$
>Hãy nhớ rằng : Khi là kẻ tấn công, ta không biết toàn bộ đa thức này. Chúng ta biết tất cả các hệ số liên quan đến AD (dữ liệu liên kết), ciphertext, $t=f(H)$, và ta sẽ không biết giá trị của $s$ 

Khi ta truyền vào thêm một dữ liệu có dạng `B0||D0||D1` (một block AD và hai block ciphertext) thì ta có **tag** mới như sau :

$$
t_2=(((((((H * B0) + D0) * H) + D1) * H) + len') * H) + s
$$

$$
\Leftrightarrow t_2=B0.H^4+D0.H^3+D1.H^2+len'.H+s
$$

Khi XOR $t_1$ và $t_2$ lại ta có :

$$
t_1\oplus t_2=AD0.H^6+AD1.H^5+(B0+C0).H^4+(D0+C1).H^3+(D1+C2).H^2+(len+len').H
$$

Chuyển vế nhưng không đổi dấu, đặt :

$$
f(x) = AD0 x^6 + AD1 x^5 + (C_0 + B_0) x^4 + (C_1 + D_0) x^3 + (C_2 + D_1) x^2 + (len + len') x + (t_1 + t_2)
$$

Rõ ràng, $H$ chính là nghiệm của đa thức trên, $f(H)=0$. Và khi đã có được $H$, ta hoàn toàn có thể làm giả **tag** để có thể vượt qua được lớp xác thực của Challenge. 

Quay trở lại với challenge trên, đầu tiên, mình sẽ gửi một block có độ dài 32 bytes là `b"iamadministratoriamadministrator".hex()` để ta thu được ciphertext : 
![{9C8BF0D9-BF71-4D22-B822-04BAE6F93E34}](https://hackmd.io/_uploads/SywlEsGygg.png)


Tiếp theo, ta cần phải biến đổi Input đầu vào sao cho không có bytes **"admin"**, vì khi có bytes đó thì server sẽ không trả về cho ta **"tag"** được.

Ta có thể biến đổi như này : 
- `b"iamadmilistrator".hex()`
- `b"iamaaministrator".hex()`

Gửi hai Plaintext đó vào và ta có : 
![{83D3D562-702F-4991-98CF-4E9D7ADEF472}](https://hackmd.io/_uploads/HJIMVjfJlx.png)


```
ct1 = '928685ee33780a759e8cd7f6444f6027a032690944674b4ccefdc9f6f7a8baee'
t1 = '831972774cc98a8ccd9d892b70f0ceae'

ct2 = '928685ee36780a779e8cd7f6444f6027a032690941674b4ecefdc9f6f7a8baee'
t2 = '6bdc0bb90ff2607e1a979429b7a09248'

c = '928685ee33780a779e8cd7f6444f6027a032690944674b4ecefdc9f6f7a8baee'
```

Khi này ta chỉ cần truyền các Input trên vào code sau : 
```python
from sage.all import *   # import sage library
from Crypto.Util.number import long_to_bytes as lb
from Crypto.Util.number import bytes_to_long as bl
from binascii import unhexlify, hexlify
from sage.all import *
import struct

def bytes_to_polynomial(block, a):
    poly = 0 
    # pad to 128
    bin_block = bin(bl(block))[2 :].zfill(128)
    # reverse it to count correctly, wrong don't reverse it lol
    # bin_block = bin_block[::-1]
    for i in range(len(bin_block)):
        poly += a^i * int(bin_block[i])
    return poly

def polynomial_to_bytes(poly):
    return lb(int(bin(poly.integer_representation())[2:].zfill(128)[::-1], 2))

def convert_to_blocks(ciphertext):
    return [ciphertext[i:i + 16] for i in range(0 , len(ciphertext), 16)]

def xor(s1, s2):
    if(len(s1) == 1 and len(s1) == 1):
        return bytes([ord(s1) ^^ ord(s2)])
    else:
        return bytes(x ^^ y for x, y in zip(s1, s2))

F, a = GF(2^128, name="a", modulus=x^128 + x^7 + x^2 + x + 1).objgen()
R, x = PolynomialRing(F, name="x").objgen()

# Set correct values
C1 = convert_to_blocks(bytes.fromhex("928685ee33780a759e8cd7f6444f6027a032690944674b4ccefdc9f6f7a8baee"))
T1 = bytes.fromhex("831972774cc98a8ccd9d892b70f0ceae")
C2 = convert_to_blocks(bytes.fromhex("928685ee36780a779e8cd7f6444f6027a032690941674b4ecefdc9f6f7a8baee"))
T2 = bytes.fromhex("6bdc0bb90ff2607e1a979429b7a09248")
C = convert_to_blocks(bytes.fromhex("928685ee33780a779e8cd7f6444f6027a032690944674b4ecefdc9f6f7a8baee"))

L = struct.pack(">QQ", 0 * 8, len(C1) * 8)
C1_p = [bytes_to_polynomial(C1[i], a) for i in range(len(C1))]
C2_p = [bytes_to_polynomial(C2[i], a) for i in range(len(C2))]
C_p = [bytes_to_polynomial(C[i], a) for i in range(len(C))]
T1_p = bytes_to_polynomial(T1, a)
T2_p = bytes_to_polynomial(T2, a)
L_p = bytes_to_polynomial(L, a)
# Here G_1 is already modified to include the tag

G_1 = (C1_p[0] * x^3) + (C1_p[1] * x^2) + (L_p * x) + T1_p
G_2 = (C2_p[0] * x^3) + (C2_p[1] * x^2) + (L_p * x) + T2_p
G_3 = (C_p[0] * x^3) + (C_p[1] * x^2) + (L_p * x)

P = G_1 + G_2
auth_keys = [r for r, _ in P.roots()]
for H, _ in P.roots():
    EJ = G_1(H)
    T3 = G_3(H) + EJ
    print("H: " + str(H) + "\tT3: " + str(polynomial_to_bytes(T3).hex()))
```
>Đoạn code trên là mình có đi Osint được của một ông người nước ngoài, đây là **[trang web](https://meowmeowxw.gitlab.io/ctf/utctf-2020-crypto/)** giải bài của ổng. Lưu ý là hãy chạy đoạn code trên trong phiên bản SageMath 9.5, cài trên Ubuntu 22.04 bằng `sudo apt install sagemath` để chạy mượt mà :penguin:

Chạy bằng Sage, ta có : 
![{1F279A64-9CAD-499B-8718-C9B1794FE8B0}](https://hackmd.io/_uploads/HJ76NoMyle.png)

Như vậy là ta đã có được Tag của admin, giờ chỉ cần gửi cho Server là xong rồi!!
![{B2947786-E640-4867-9E52-68B1BA23BBFC}](https://hackmd.io/_uploads/r1ilBjfJlx.png)



Dưới đây là một ví dụ về kiểu tấn công này : Challenge **Forbidden Fruit - CryptoHack**

![{358FD91E-E7F6-4D64-AB03-ACA1EF7E32AB}](https://hackmd.io/_uploads/ry8zcqZ1xg.png)
![image](https://hackmd.io/_uploads/HyzH5qbJxl.png)


Vì note này chỉ dùng để ghi lý thuyết nên mình sẽ viết wu bài này bên note khác nha (tại tới giới hạn của HackMD rồi :smile:)

## Weak Key
Đối với DES, khóa yếu **[(Weak Key)](https://en.wikipedia.org/wiki/Weak_key)** là một dạng khóa mà khi ta dùng nó để mã hóa DES thì vô tình chung nó lại trở thành giải mã DES. 

Nó thường được chia làm 2 loại là : 
- Khóa yếu (weak keys)
- Khóa bán yếu (semi-weak keys)

**Nhắc lại về quá trình sinh khóa DES :** Từ một khóa **64 bits** ban đầu sẽ rút xuống còn **56 bits** ở bước **Permuted Choice 1 (PC-1)** (bỏ đi các bits chẵn lẻ), rồi sau đó trải qua 16 vòng lặp, mỗi vòng sẽ trả về 1 khóa con. Từ đó, ta sẽ có tất cả là 16 khóa con từ khóa ban đầu tham gia vào 16 vòng mã hóa DES.

**Khóa yếu trong DES** là những khóa có tính chất đặc biệt: cả 16 khóa con (subkeys) được tạo ra đều giống hệt nhau trong quá trình sinh khóa. Điều này xảy ra do cách thuật toán DES xử lý lịch trình khóa (key schedule) khi gặp những giá trị khóa đặc biệt. 

Một số khóa yếu ở dạng hex : 
- Xen kẽ $0$ và $1$ : `0x0101010101010101`
- Xen kẽ $F$ và $E$ : `0xFEFEFEFEFEFEFEFE`
- Full $0$ : `0x0000000000000000`
- Full $F$ : `0xFFFFFFFFFFFFFFFF`

Ngoài ra cũng có thể xen kẽ $0$, $1$, $F$, $E$ : 
- `0xE0E0E0E0F1F1F1F1`
- `0x1F1F1F1F0E0E0E0E`
- `0xE1E1E1E1F0F0F0F0`
- `0x1E1E1E1E0F0F0F0F`

Khóa bán yếu trong DES là các khóa chỉ tạo ra **hai khóa** con khác nhau, mỗi khóa con được sử dụng lặp lại 8 lần trong thuật toán. Chúng thường tồn tại theo một cặp $(K1, K2)$ với tính chất :

$$
E_{K_2}(E_{K_1}(Plaintext)) = Plaintext
$$

Có 6 cặp khóa bán yếu:
- `0x011F011F010E010E` và `0x1F011F010E010E01`
- `0x01E001E001F101F1` và `0xE001E001F101F101`
- `0x01FE01FE01FE01FE` và `0xFE01FE01FE01FE01`
- `0x1FE01FE00EF10EF1` và `0xE01FE01FF10EF10E`
- `0x1FFE1FFE0EFE0EFE` và `0xFE1FFE1FFE0EFE0E`
- `0xE0FEE0FEF1FEF1FE` và `0xFEE0FEE0FEF1FEF1`

Ngoài ra, còn có 48 khóa có thể yếu (possibly weak keys) chỉ tạo ra bốn khóa con khác nhau (thay vì 16 khóa con như thông thường). 

Những khóa yếu và bán yếu này không được coi là lỗ hổng nghiêm trọng của DES, bởi vì:
- DES có tổng cộng : $2^{56}$ khóa khả thi (khoảng 72 nghìn tỷ khóa)
- Trong đó chỉ có 4 khóa yếu và 12 khóa bán yếu, chiếm một phần cực kỳ nhỏ trong không gian khóa.
- Người dùng có thể dễ dàng kiểm tra và loại bỏ các khóa yếu khi tạo khóa DES.

Tuy nhiên, hiện nay DES không còn được khuyến nghị sử dụng vì toàn bộ không gian khóa có thể bị bẻ khóa bằng brute-force.

# SYMMETRIC CIPHERS : Challenge CryptoHack
Coming soon...






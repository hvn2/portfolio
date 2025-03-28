---
layout: default
title: QUAN SÁT PHỔ TÍN HIỆU
---

**Trong bài này sẽ hướng dẫn cách quan sát thành phần tần số (phổ) của một tín hiệu hình sin bằng biến đổi Fourier thực hiện trong Matlab.**


Trong chương trình môn học Xử lý tín hiệu số ở bậc Đại học, một số giáo trình truyền thống ( ví dụ: Xử lý tín hiệu và lọc số - PGS.TS Nguyễn Quốc Trung) bắt đầu bằng việc giới thiệu tín hiệu và hệ thống rời rạc. Định nghĩa và những tính chất của tín hiệu và hệ thống rời rạc tương đối dễ hiểu xét về mặt toán học. Tuy nhiên, từ kinh nghiệm của tôi khi học môn học này với việc tiếp cận môn học như vậy hơi nhàm chán vì không hiểu học những kiến thức đơn thuần về toán học như vậy để làm gì. Có lẽ cần một cách tiếp cận có tính trực quan và hấp dẫn hơn.

**Xét ví dụ sau:** Cho một tín hiệu là tổng của hai tín hiệu hình sin tần số 100 Hz và 200 Hz, cộng với nhiễu. Hãy vẽ phổ của tín hiệu để thấy được hai vạch phổ tại tần số 100 Hz và 200 Hz.

Chắc chắn những người từng học về tín hiệu và hệ thống đều biết để tìm phổ của một tín hiệu liên tục thì chỉ cần tìm biến đổi Fourier liên tục (Continuous Fourier Transform: CFT) của nó. Hoàn toàn đúng, trong trường hợp này dựa vào định nghĩa của biến đổi Fourier liên tục (CFT) dùng các biến đổi toán học có thể tìm được biến đổi Fourier của nó và vẽ ra trên trục tần số. Trong trường hợp cụ thể này tìm CFT của tín hiệu hình sin thì đơn giản, nhưng dùng biến đổi toán học để tìm CFT của những tín hiệu tổng quát, phức tạp hơn hoặc tìm CFT của nhiễu cũng không phải là dễ. Vậy nên đối với những tín hiệu phức tạp hơn, dùng cách này không khả thi.

Bây giờ hãy dùng công cụ biến đổi Fourier rời rạc (Discrete Fourier Transform: DFT) của Matlab để giải quyết. (DFT trong Matlab là hàm ```fft()```).

Ok, hãy tạo một tín hiệu như đề bài đã cho
~~~~matlab
Fs = 500;             % Sampling frequency                    
T = 1/Fs;             % Sampling period = 2 ms    
L = 1500;             % Length of signal 3 seconds
t = (0:L-1)*T;        % Time vector
% generate signal with noise
f1=100; f2=200;
S = 0.7*sin(2*pi*f1*t) + sin(2*pi*f2*t);
X = S + 2*randn(size(t));
plot(100*t(1:50),X(1:50))
title('Signal Corrupted with Zero-Mean Random Noise')
xlabel('t (milliseconds)')
ylabel('X(t)')
% Take DFT of X
Y=fft(X);
plot(abs(Y))
title('Biến đổi Fourier của x(t)')
xlabel('Sample')
ylabel('Magnitude')
~~~~
Đây là đồ thị

![hinh1](/images/bai-01/noisysinwave.png)  
Hình 1. Hình dạng tín hiệu trong miền thời gian

![hinh2](/images/bai-01/fftsinwave.png)  
Hình 2. Biến đổi Fourier của tín hiệu

Từ đồ thị của biến đổi DFT của tín hiệu (phổ biên độ) thấy rằng có 4 vạch phổ đối xứng nhau ở các vị trí mẫu khoảng gần 350, 550, 850 và 1250 chứ không phải là 2 vạch phổ tại hai tần số 100 Hz và 200 Hz như ta mong đợi. Lý do ở đây là chúng ta đã sử dụng biến đổi DFT chứ không phải là CFT, bây giờ hãy xét mối quan hệ giữa các biến đổi này với nhau, sau đó chúng ta sẽ điều chỉnh lại đoạn code Matlab ở trên để quan sát được đúng 2 vạch phổ tại vị trí 100 Hz và 200 Hz.

Vì trong máy tính không biểu diễn được tín hiệu liên tục, nên đầu tiên chúng ta xét quá trình chuyển đổi từ tương tự sang số, hay đơn giản hơn là chuyển đổi từ liên tục sang rời rạc. Quá trình chuyển đổi tương tự sang số bao gồm lấy mẫu, lượng tử hóa và mã hóa, trong đó quá trình lấy mẫu là quá trình rời rạc hóa về mặt thời gian, hay nói cách khác biến từ biến $t$ thành biến $n$. Trong miền thời gian, lấy mẫu là nhân tín hiệu liên tục với một xung lấy mẫu có chu kỳ $T_s$, giả sử quá trình lấy mẫu là lý tưởng (xung lấy mẫu là các xung lý tưởng). Gọi $x_s(t)$ là tín hiệu sau khi lấy mẫu. Ta có

$$x_s(t) = x_c(t).s(nT_s)$$

Biến đổi Fourier liên tục của $x_s(t)$ là tích chập của biến đổi Fourier liên tục của $x(t)$ và $s(t)$. Tham khảo tài liệu về Xử lý tín hiệu số để có kết quả như sau:

$$
\begin{equation}
\label{eq:1}
X_s(j\Omega)=\frac{1}{T_s}\sum_{k=-\infty}^{+\infty}{X_c(j(\Omega-k\Omega_s))}
\end{equation}
$$

Với $\Omega$ là tần số liên tục và $\Omega _s =2\pi F_s = 2\pi/T_s$ là tần số lấy mẫu. Công thức \ref{eq:1} trên có nghĩa là phổ của tín hiệu lấy mẫu là vô số những phổ của tín hiệu liên tục lặp lại ở các tần số bằng một số nguyên lần tần số lấy mẫu $\Omega_s$, như hình dưới đây. Lưu ý rằng ở đây chúng ta sử dụng $\Omega$ để chỉ tần số góc liên tục, $\omega$ để chỉ tần số chuẩn hóa (tần số của phép biến đổi DTFT), dấu ngoặc đơn (.) để chỉ biến liên tục, dấu ngoặc vuông [.] để chỉ biến rời rạc.

![hinh3](/images/bai-01/sampling.png)  
Hình 3. Biến đổi CFT của tín hiệu liên tục và tín hiệu lấy mẫu

Từ hình vẽ trên cũng suy ra được định lý lấy mẫu **Nyquist** nổi tiếng: Tần số lấy mẫu phải lớn hơn hoặc bằng 2 lần tần số lớn nhất của tín hiệu. Rõ ràng để khôi phục được tín hiệu tương tự từ các mẫu một cách chính xác nhất thì phổ lặp lại tại các tần số bằng số nguyên lần $\Omega_s$ không được xen lẫn vào nhau - như hình b, nếu không sẽ bị xen lẫn vào nhau và không khôi phục được - như hình c. Để thỏa mãn điều đó thì:

$$2\Omega_H \leqslant \Omega_s$$

Gọi $x[n]$ là dãy rời rạc từ dãy lấy mẫu $x[n]=x_c(nT_s)$, biến đổi Fourier thời gian rời rạc của $x[n]$ có dạng:

$$
\begin{equation}
\label{eq:2}
X(e^{j\omega})=\sum_{n=-\infty}^{+\infty}{x[n]e^{j\omega n}}
\end{equation}
$$

Đối chiếu công thức \ref{eq:2} với công thức biến đổi Fourier liên tục của $x_s(t)$ (định nghĩa như sau: $X_s(j\Omega)=\sum_{n=-\infty}^{+\infty} x_c(nT_s)e^{-j\Omega Tn}$) và kết quả $X_s(j\Omega)$ ở công thức \ref{eq:1} có thể thấy rằng nó chính là công thức \ref{eq:2} với $\omega = \Omega T_s$

$$
\begin{equation}
\label{eq:3}
X(e^{j\omega})=\frac{1}{T_s}\sum_{k=-\infty}^{+\infty}X_c(j(\frac{\omega}{T_s}-\frac{2\pi k}{T_s}))
\end{equation}
$$

Công thức này cho thấy rằng $X(e^{j\omega})$ chính là $X_s(j\Omega)$ với trục tần số được tỷ lệ hóa với $T_s$, hay $\omega=\Omega T_s$. Hoặc mối quan hệ giữa tần số vật lý $F$ và tần số góc chuẩn hóa $\omega$ là $F=\frac{\Omega}{2\pi} = \frac{\omega}{2\pi T_s}=\frac{\omega}{2\pi}F_s$. Nếu tần số tín hiệu bằng đúng tần số Nyquist ($F=\frac{F_s}{2}$) thì $\omega=\pi$. Điều thú vị này chỉ cho bạn rằng đối với tín hiệu có băng thông giới hạn (band limited signal), khi biểu diễn nó trong miền $\omega$ thì phổ của nó không bao giờ vượt quá $\pi$, đó là vì tần số lớn nhất của nó không được vượt quá tần số Nyquist $F_s/2 \equiv \omega = \pi$. Đó cũng chính là lý do đáp ứng tần số của bộ lọc thông thấp (lý tưởng) thì bằng 1 ở vùng $\omega$ gần 0 và đáp ứng tần số của bộ lọc thông cao thì bằng 1 ở vùng $\omega$ gần $\pi$.

**Ví dụ:** Nếu tín hiệu hình sin tương tự tần số 200 Hz được lấy mẫu với tần số 1000 Hz ($T_s = 1 ms$) thì $\Omega_H = 2\pi 200 = 400\pi$, khi đó $\omega_H = \Omega_H T_s = 0.4\pi$. Nếu tần số bằng 500 Hz thì $\omega_H=\Omega_H T_s=\pi$.

Đến đây mọi việc đã tương đối sáng tỏ, chúng ta đã tìm ra mối liên hệ giữa tần số tín hiệu tương tự $F$ và tần số chuẩn hóa $\omega$. Trong Matlab chúng ta có 2 cách để tìm phổ của tín hiệu. Cách thứ nhất dựa vào lệnh ```freqz()``` để tìm đáp ứng tần số ($H(e^{j\omega})\overset{\mathcal{DFT}}{\leftrightarrow}h[n]$). Cách thứ 2 là tìm biến đổi DFT để tìm trực tiếp phổ của tín hiệu. Các bạn có thể thử cách thứ nhất. Ở đây chúng ta tiếp cận theo cách thứ 2.

Chúng ta biết rằng DFT N-điểm là rời rạc hóa tần số của DTFT với $\omega = 2\pi/N$ và DTFT tuần hoàn với chu kỳ $2\pi$ nên trong Matlab DTFT chỉ thể hiện trong 1 chu kỳ $(-\pi, \pi)$ (có thể dùng lệnh ```freqz()``` để kiểm tra). Do vậy, DFT cũng tuần hoàn với chu kỳ $2\pi$, trong Matlab thể hiện  trong 1 chu kỳ $(0,2\pi)$ (có dùng lệnh ```fft()``` để kiểm tra) với phần từ $\pi$ đến $2\pi$ tương ứng với phần $-\pi$ đến 0 của DTFT. Hơn nữa DTFT (DFT) của một tín hiệu thực là hàm chẵn - đối xứng qua gốc tọa độ. Nên chúng ta chỉ cần xét một nửa chu kỳ là đủ. Tóm lại, để quan sát phổ của tín hiệu chúng ta chỉ cần xét DFT trong 1 nửa chu kỳ và chuyển đổi tương ứng tần số chuẩn hóa thành tần số vật lý. Đoạn code ở trên sẽ thay đổi một chút như sau:

```matlab
% Take DFT of X
Y=T*fft(X); % Y tuan hoan voi chu ky 0 den $2\pi$
Flength=L/2; % Do Y doi xung qua $\pi$ nen chi khao sat 1 nua 
Yhalf=Y(1:Flength); 
f=Fs/L*(1:Flength); % Fs/2 ung voi $\pi$
plot(f, abs(Yhalf))
title('Biến đổi Fourier của x(t)')
xlabel('Tần số')
ylabel('Magnitude')
```
Và đây là kết quả

![hinh4](/images/bai-01/sinespectra.png)  
Hình 4. Tần số tín hiệu tại vị trí 100 Hz và 200 Hz

*Như vậy bài này đã giúp bạn quan sát được thành phần tần số một cách trực quan dựa vào biến đổi Fourier rời rạc. Đồng thời cũng trình bày một cách ngắn gọn mối quan hệ giữa biến đồi Fourier liên tục CFT, biến đổi Fourier thời gian rời rạc DTFT và biến đổi Fourirer rời rạc DFT. Mấu chốt của vấn đề là phải hiểu được mối quan hệ giữa tần số vật lý của tín hiệu $F$ và tần số của phép biến đổi, hay tần số chuẩn hóa $\omega$ và tính đối xứng của phổ đối với tín hiệu thực.*
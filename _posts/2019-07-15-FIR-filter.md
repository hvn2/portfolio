---
layout: default
title: BỘ LỌC SỐ CÓ CHIỀU DÀI ĐÁP ỨNG XUNG HỮU HẠN (FIR)
---

**Bài này trình bày ngắn gọn và cơ bản nhất về bộ lọc số nói chung và bộ lọc số có chiều dài đáp ứng xung hữu hạn. Ví dụ thiết kế một bộ lọc FIR (Finite Impulse Response) thông thấp theo phương pháp cửa sổ.**

Một trong những ứng dụng chủ yếu và quan trọng nhất của DSP là bộ lọc số. Trong các giáo trình Xử lý tín hiệu số, thường để đảm bảo tính logic, thống nhất nên thường trình bày rất dài (nhưng đầy đủ về mặt toán học) từ kiến trúc bộ lọc, đến đặc điểm bộ lọc rồi mới đến thiết kế làm người học đôi khi chưa hiểu và thiết kế được bộ lọc số đã nản chí rồi. Bài viết này cố gắng trình bày một số kiến thức về bộ lọc sao cho đơn giản nhất có thể.

**Hãy bắt đầu bằng một ví dụ:** Thiết kế bộ lọc số thông thấp để có thể lọc được những tần số dưới 1000 Hz của một tín hiệu âm thanh đầu vào (giả sử âm thanh có tần số từ 0 đến 20 kHz). Để đơn giản, trong ví dụ này chỉ tìm ra đáp ứng xung của bộ lọc mà không trình bày việc cài đặt bộ lọc.

Đây là bài toán thiết kế bộ lọc thông thấp. Bộ lọc ở đây được hiểu là bộ lọc chọn lọc tần số, vì nó cho những tần số dưới một tần số nhất định - tần số cắt (trong trường hợp này bằng $\omega_c =2\pi F_c$ với $F_c= 1000\ Hz$) đi qua và chặn những tần cao hơn tần số cắt đó. Các bộ lọc khác như bộ lọc thông cao, bộ lọc thông dải, bộ lọc chắn dải cũng định nghĩa tương tự. Đáp ứng tần số của bộ lọc thông thấp được mô tả như sau:

$$H(e^{j\omega}) =

\begin{cases} 1, \ với \ |\omega|<\omega_c \\ 0 \ còn\ lại

\end{cases}$$

Sử dụng biến đổi ngược DTFT sẽ tìm được đáp ứng xung có dạng:

$$h[n]=\frac{sin(n\omega _c)}{n\pi}$$

Đồ thị đáp ứng tần số và đáp ứng xung như hình dưới đây
![hinh1](/images/bai-02/lpfresponses.png)  
Hình 1. Đáp ứng tần số và đáp ứng xung của bộ lọc thông thấp lý tưởng.

Từ đồ thị ở Hình 1 có thể thấy đáp ứng xung của bộ lọc thông thấp lý tưởng là không nhân quả (khác 0 với những giá trị $n$ nhỏ hơn 0) và có chiều dài vô hạn, do đó không thể thực hiện được về mặt vật lý. Để hiện thực được bộ lọc thì chúng ta phải thay đổi một chút đối với đáp ứng xung:
- Giới hạn lại chiều dài đáp ứng xung của bộ lọc, như chúng ta thấy trên hình vẽ các hệ số bộ lọc càng cao thì cảng nhỏ lại, do vậy chúng ta có thể coi đến một mức nào đó các hệ số đó triệt tiêu. Mức đó gọi là bậc của bộ lọc (order of filter).
- Dịch đáp ứng xung của bộ lọc đi một số mẫu để nó không còn các hệ số khác 0 ở phần $n$ nhỏ hơn 0 nữa.

Khi  thực hiện 2 việc này sẽ dẫn đến 2 vấn đề:
- Giới hạn chiều dài đáp ứng xung làm cho đáp ứng tần số không còn dạng lý tưởng như hình trên nữa mà sẽ bị méo mó đi. Cụ thể là ở vùng dải thông và dải chắn không còn bằng phẳng mà có độ gợn sóng, biên độ giữa phần dải thông và dải chắn không thể thay đổi một cách đột ngột (từ 1 về 0) được mà phải có một sự chuyển tiếp dần dần. Việc giới hạn chiều dài đáp ứng xung giống như là nhân với đáp ứng xung với một cửa sổ có chiều dài hữu hạn - trong trường hợp này là cửa sổ hình chữ nhật có biên độ bằng 1. Hình dạng, kích thước của sổ sẽ ảnh hưởng đến các thông số (độ gợn sóng, thời gian chuyển tiếp) của bộ lọc. Đặc điểm của các loại cửa sổ sẽ đề cập đến ở phần sau của bài. Các đặc tả của bộ lọc thực tế được cho như ở Hình 2. Chính vì điều này nên giả thiết ở ví dụ bên trên là chưa đủ khi thiết kế bộ lọc thực tế, cần phải có những chỉ tiêu đặc tả bộ lọc như ở Hình 2 mới đủ để thiết kế, chúng ta sẽ quay lại điều này ở phần cuối của bài.
- Dịch $n_0$ mẫu trong miền thời gian thì tương ứng trong miền tần số sẽ là $h[n-n_0] \overset{\mathcal{DTFT}}{\leftrightarrow} e^{-j\omega n_0}H(e^{j\omega})$. Nghĩa là đã làm dịch pha đi $n_0$. Điều này dẫn đến ràng buộc là bộ lọc có pha tuyến tính (pha là hàm số bậc nhất của $\omega$). Quá trình dịch này không làm ảnh hưởng đến đáp ứng biên độ - tần số (magnitude response, tức là $\vert H(e^{j\omega}) \vert$, mà chỉ ảnh hưởng đến đáp ứng pha, cụ thể là độ trễ nhóm (group delay). Độ trễ nhóm đặc trưng cho độ trễ của tín hiệu có tần số khác nhau khi đi qua bộ lọc. Tuy nhiên trong phần lớn trường hợp thì ảnh hưởng của pha không quan trọng.

![hinh2](/images/bai-02/thamsoboloc.png)  
Hình 2. Các tham số của bộ lọc

Với những ràng buộc như trên thì bộ lọc của chúng ta cần thiết kế sẽ giới hạn lại là bộ lọc số có đáp ứng xung chiều dài hữu hạn và pha tuyến tính (bộ lọc FIR pha tuyến tính). Đáp ứng tần số của bộ lọc $H(e^{j\omega})$ nói chung là một số phức và nếu $h[n]$ là thực thì đáp ứng biên độ - tần số (Magnitude response) $\vert H(e^{j\omega}) \vert$ là hàm chẵn, đáp ứng pha - tần số $arg(H(e^{j\omega})$ là một hàm lẻ. Đáp ứng tần số có thể biểu diễn dưới dạng độ lớn và pha (biểu diễn số phức dưới dạng tọa độ cực): $H(e)^{j\omega}=A(e^{j\omega})e^{j\theta(\omega)}$ với $\theta(\omega)=\beta-\alpha \omega$ là pha tuyến tính, $A(e^{j\omega})$ là một số thực có thể âm hoặc dương. Với bộ lọc FIR pha tuyến tính người ta chứng minh được có 4 loại :

- Loại 1: $h[n]$ đối xứng, N lẻ (N là chiều dài đáp ứng xung bộ lọc)
- Loại 2: $h[n]$ đối xứng, N chẵn
- Loại 3: $h[n]$ phản đối xứng, N lẻ
- Loại 4: $h[n]$ phản đối xứng, N chẵn

Trong đó loại 2 không thể là bộ lọc thông cao, loại 3 và loại 4 không thể là bộ lọc thông thấp và thông cao. Tính chất của đáp ứng tần số và đáp ứng xung của các loại này bạn có thể đọc thêm trong tài liệu (đọc thêm Xử lý tín hiệu số, chương 5, tập 1 - Nguyễn Quốc Trung).

Với những phân tích như trên, để thiết kế bộ lọc FIR thông thấp theo phương pháp cửa sổ chính là giới hạn độ dài của đáp ứng xung và dịch nó thành nhân quả. Do tính chất của bộ lọc FIR pha tuyến tính chúng ta sẽ thường chọn bộ lọc FIR loại 1. Nói cách khác chúng ta dịch đáp ứng xung của bộ lọc đi $\alpha=\frac{N-1}{2}$ mẫu và nhân với cửa sổ chiều dài $N$ là một số lẻ và đối xứng qua $\frac{N-1}{2}$. Gọi $h[n]$ là đáp ứng xung của bộ lọc, $h_d[n]$ là đáp ứng xung của bộ lọc lý tưởng và $w[n]$ là cửa sổ. Thì:

$$h[n] =
\begin{cases}h_d[n-\alpha]w[n], \ với \ 0 \leq n \leq N \\ 0 \ còn\ lại
\end{cases}$$

Trong miền tần số phép nhân thành phép tích chập (chưa dịch)

$$ H(e^{j\omega}) = H_d(e^{j\omega}) \ast W(e^{j\omega}) =\frac{1}{2\pi}\int_{-\pi}^{\pi}W(e^{j\omega})H_d(e^{j(\omega - \lambda)})d\lambda$$

Hình vẽ dưới đây minh họa ảnh phép tích chập
![hinh3](/images/bai-02/window.png)  
Hình 3. Tích chập trong miền tần số

Dựa vào Hình 3 và tính chất của các loại cửa sổ, rút ra một số nhận xét như sau:

  - Độ rộng của búp chính (main lobe) của cửa sổ tỷ lệ nghịch với N, điều này cũng phù hợp vì tín hiệu càng rộng trong miền thời gian sẽ càng hẹp trong miền tần số và ngược lại. Tỉ lệ độ cao (biên độ) của búp sóng chính và búp sóng phụ (gần nhất) gần như không phụ thuộc vào N, điều này dẫn đến suy hao ở dải chắn $A_s$ không phụ thuộc vào N. 
  - Độ rộng búp chính tỷ lệ thuận với độ rộng vùng chuyển tiếp giữa dải thông và dải chắn. Do vậy nếu N (bậc bộ lọc)  càng lớn thì vùng chuyển tiếp giữa dải thông và dải chắn càng nhỏ.
  - Búp phụ (side lobe) ảnh hưởng đến độ nhấp nhô (ripples) ở cả dải thông và dải chắn, các búp phụ càng nhấp nhô thì dải thông, dải chắn càng nhấp nhô.
  - Phép tích chập tạo ra sự dao động ở vùng tấn số cắt $\omega_c$ (hiện tượng Gibbs)

Như vậy rõ ràng lựa chọn cửa sổ như thế nào sẽ ảnh hưởng đến chất lượng của bộ lọc. Với mong muốn bộ lọc có đáp ứng xấp xỉ tốt nhất với đáp ứng của bộ lọc lý tưởng, chúng ta phải chọn cửa sổ với hình dạng và chiều dài N sao cho độ rộng búp chính rất nhỏ và biên độ búp phụ rất nhỏ và bằng phẳng. Các loại cửa sổ và tính chất của nó đã được nghiên cứu và tổng kết ở các giáo trình Xử lý tín hiệu số, một số loại cửa sổ phổ biến và tính chất của nó được cho như Bảng 1 dưới đây.

![bang1](/images/bai-02/windowproperties.png)  
Bảng 1. Tính chất của các loại cửa sổ (cửa sổ tam giác còn gọi là cửa sổ Bartlett)

Sau khi đã phân tích tính chất của bộ lọc FIR pha tuyến tính và các loại cửa sổ (hi vọng chưa nản khi đọc đến phần này). Chúng ta sẽ quay lại nhiệm vụ chính đặt ra ở đầu bài. Với những gì đã phân tích, chúng ta sẽ phát biểu lại bài toán một cách chặt chẽ như sau:  
**Ví dụ:** Thiết kế bộ lọc số FIR pha tuyến tính bằng phương pháp cửa sổ với các điều kiện sau:
- Lọc thông thấp tín hiệu âm thanh được lấy mẫu ở tần số $F_s = 44.1$ kHz, tần số cắt $F_c = 1$ kHz.
- Mức suy hao dải chặn không nhỏ hơn 50 dB, độ rộng vùng chuyển tiếp 100 Hz

**_Giải:_**  
Tính các thông số đặc tả bộ lọc. Lưu ý đọc lại bài trước để biết mối quan hệ giữa tần số vật lý $F$ và tần số chuẩn hóa $\omega$ ($\omega = 2\pi \frac{F}{F_s}$)
$$\omega_c = 2\pi F_c/F_s = 0.045\pi \\   \Delta\Omega = 2\pi \Delta F/F_s = 0.0045\pi$$
- Dựa vào bảng tra các thông số của cửa sổ để đảm bảo suy hao dải chặn không nhỏ hơn 50 dB thì phải chọn cửa sổ Hamming hoặc Blackman. Giả sử chọn cửa sổ Hamming có $A_s = 53 dB$.
- Số bậc bộ lọc: $N = 6.6\pi /0.0045\pi = 1467$ và $\alpha = \frac{N-1}{2} = 733$
- Phương trình đáp ứng xung: $h[n]=h_d[n-\alpha]w[n]$, với
$$w[n] =
\begin{cases}
0.54 - 0.46 cos(\frac{2\pi n}{N}), \ với \ 0 \leq n \leq N \\
0 \ còn \ lại
\end{cases} $$
- Và:
$$h_d[n-\alpha]= \frac{sin \omega_c [n-\alpha]}{\pi[n-\alpha]} \ với
\begin{cases}
\omega_c = 0.045\pi \\
\alpha = 733
\end{cases} $$

Qua ví dụ này cho thấy với yêu cầu cao của bộ lọc (độ chuyển tiếp nhỏ) thì bậc của bộ lọc phải rất lớn, đó cũng là nhược điểm của bộ lọc FIR. Ví dụ này mới chỉ trình bày thiết kế bộ lọc FIR pha tuyến tính thông thấp (LPF). Để thiết kế bộ lọc thông cao, thông dải cần phải biến đổi một chút, và sẽ được trình bày ở một bài khác.

Trình tự thiết kế bộ lọc FIR pha tuyến tính bằng phương pháp cửa sổ có thể tóm tắt như sau:

1. Tính toán các thông số đặc tả của bộ lọc, nếu các thông số chưa ở dạng tương đối (đơn vị $dB$) thì chuyển về dạng tương đối.
2. Dựa vào yêu cầu suy hao dải chắn để chọn loại cửa phù hợp
3. Dựa vào độ rộng vùng chuyển tiếp dải thông dải chắn để tính số bậc của bộ lọc N
4. Tìm ra đáp ứng xung bằng tích giữa đáp ứng xung lý tưởng (dịch đi $\frac{N-1}{2}$ mẫu) nhân với cửa sổ
5. Kiểm tra lại xem có thỏa mãn các thông số đề bài đặt ra

*Việc tính toán bằng tay có vẻ khá phức tạp, nhưng nếu sử dụng Matlab thì rất đơn giản. Bạn có thể sử dụng sẵn Filter Design Apps, Filter Design Toolbox hoặc tự viết chương trình để tính các hệ số đáp ứng xung bộ lọc. Bài tiếp theo sẽ trình bày việc thiết kế bộ lọc bằng Matlab.*

# MATLAB: THIẾT KẾ BỘ LỌC FIR THEO PHƯƠNG PHÁP CỬA SỔ

Nhắc lại về trình tự thiết kế bộ lọc FIR theo phương pháp cửa sổ:

- Tính toán các thông số đặc tả của bộ lọc, nếu các thông số chưa ở dạng tương đối (đơn vị $dB$ ) thì chuyển về dạng tương đối
- Dựa vào yêu cầu suy hao dải chắn để chọn loại cửa phù hợp
- Dựa vào độ rộng vùng chuyển tiếp dải thông dải chắn để tính số bậc của bộ lọc N
- Tìm ra đáp ứng xung bằng tích giữa đáp ứng xung lý tưởng (dịch đi $n_0$ mẫu) nhân với cửa sổ
- Kiểm tra lại xem có thỏa mãn các thông số đề bài đặt ra.

Signal Processing Toolbox của Matlab đã cung cấp sẵn các cửa sổ (Đặc điểm chi tiết của các loại cửa sổ có thể xem thông qua Apps Window Design), ví dụ:
```
    w = rectwin(M): Cửa sổ chữ nhật chiều dài M
    w = bartlett(M): Cửa sổ tam giác chiều dài M
    w = hann(M): Cửa sổ Hanning chiều dài M
    w = hamming(M): Cửa sổ Hamming chiều dài M
    w = blackman(M): Cửa sổ Blackman chiều dài M
    w = kaiser (M,beta): Cửa sổ Kaiser chiều dài M, hệ số $\beta$
```
Đáp ứng xung của bộ loc FIR được tính bằng tích giữa cửa sổ và đáp ứng xung của bộ lọc FIR lý tưởng (dịch $\alpha=\frac{M-1}{2}$). Do vậy cần viết một hàm tính đáp ứng xung lý tưởng của bộ lọc:
```matlab
%%%% Hàm tính đáp ứng xung bộ lọc lý tưởng%%%
function hd = ideal_lp(wc,M)
% Ideal LowPass filter computation
% Ham tao ra dap ung xung ly tuong chieu dai M
% --------------------------------
% Input:
    % wc = Tan so cat, don vi radians
    % M = Chieu dai dap ung xung 0 - (M-1)
% Output
    % hd = fc*sinc(fc*m), ideal impulse response between 0 to M-1
alpha = (M-1)/2; n = [0:1:(M-1)];
m = n - alpha; fc = wc/pi; hd = fc*sinc(fc*m);
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
```
Để quan sát các thông số của bộ lọc (VD: Đáp ứng tần số, đáp ứng pha, trễ nhóm,...) xem có thỏa mãn các yêu cầu thiết kế hay không, trong Matlab có thể dùng lệnh ```freqz()``` và ```phasez()```. Tuy nhiên để thuận tiện cho việc quan sát các thông số bộ lọc thuận tiện hơn, chúng ta sẽ viết một hàm tính đồng thời các thông số của bộ lọc:
```matlab
%%%Hàm tính các tham số (tần số) bộ lọc%%%
function [db,mag,pha,grd,w] = freqz_m(b,a)
% Modified version of freqz subroutine
% Tinh cac thong so cau dap ung tan so bo loc so
% ------------------------------------
% Output
    % db = Dap ung bien do tuong doi |H(e^jw)|, don vi dB tinh tu 0 den pi radians
    % mag = Dap ung bien do tuyet doi |H(e^jw)| khong co don vi tinh tu 0 den pi radians
    % pha = Dap ung pha, don vi radians tinh tu 0 den pi radians
    % grd = Tre nhom (group delay) tinh tu 0 den pi radians
    % w = Lay mau tan so 501 diem tu 0 den pi radians
% Input
    % b = tu so cua da thuc H(z) (Đối với bộ lọc FIR thì b = h)
    % a = mau so cua da thuc H(z) (Đối với bộ lọc FIR thì a = [1])
[H,w] = freqz(b,a,1000,'whole');
H = (H(1:1:501))'; w = (w(1:1:501))';
mag = abs(H); db = 20*log10((mag+eps)/max(mag));
pha = angle(H); grd = grpdelay(b,a,w);
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
```
Bây giờ vận dụng các kiến thức và hàm ở trên để thiết kế bộ lọc với các yêu cầu sau:  
**Ví dụ:** Thiết kế bộ lọc FIR thỏa mãn các thông số sau:  
$$ \omega_p = 0.2\pi, R_p=0.25 dB \\
\omega_s = 0.3\pi, A_s= 50 dB $$
*Giải*:  
- Tần số cắt: $\omega_c = (\omega_s + \omega_p)/2=0.25\pi$
- Độ rộng dải chuyển tiếp: $\Delta \Omega = \omega_s - \omega_p=0.1\pi$
- Suy hao ở dải chắn $A_s=50dB$ nên có thể chọn cửa sổ Hamming hoặc Blackman, ở đây chọn cửa sổ Hamming, độ dài cửa sổ được tính dựa vào công thức độ rộng dải chuyển tiếp: $\Delta\Omega = 6.6\pi/N$
```matlab
%%%%%Ví dụ thiết kế bộ lọc FIR%%%%%
 wp = 0.2*pi; ws = 0.3*pi; 
 Omega_S = ws - wp; %Dai chuyen tiep
 M = ceil(6.6*pi/Omega_S) + 1; % Cua so Hamming
 n=[0:1:M-1];
 wc = (ws+wp)/2; % Ideal LPF cutoff frequency
 hd = ideal_lp(wc,M);
 w_ham = (hamming(M))';
 h = hd .* w_ham;
 [db,mag,pha,grd,w] = freqz_m(h,1);
 delta_w = 2*pi/1000; % sample of frequency
 Rp = -(min(db(1:1:wp/delta_w+1))) % Actual Passband Ripple
 As = -round(max(db(ws/delta_w+1:1:501))) % Min Stopband attenuation
% plots
 subplot(2,2,1); stem(n,hd); title('Ideal Impulse Response')
 axis([0 M-1 -0.1 0.3]); xlabel('n'); ylabel('hd(n)')
 subplot(2,2,2); stem(n,w_ham);title('Hamming Window')
 axis([0 M-1 0 1.1]); xlabel('n'); ylabel('w(n)')
 subplot(2,2,3); stem(n,h);title('Actual Impulse Response')
 axis([0 M-1 -0.1 0.3]); xlabel('n'); ylabel('h(n)')
 subplot(2,2,4); plot(w/pi,db);title('Magnitude Response in dB');grid
 axis([0 1 -100 10]); xlabel('frequency in pi units'); ylabel('Decibels')
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
```
Lưu ý trong đoạn code trên có tính suy hao tối thiểu ở dải chắn $A_s$ và độ gợn sóng ở dải thông $R_p$, cả hai thông số này đều thỏa mãn điều kiện thiết kế nên thiết kế ở trên đạt yêu cầu. Giả sử nếu $R_p$ không đạt yêu cầu thì có thể thử loại cửa sổ khác (lưu ý: Độ gợn sóng phụ thuộc vào độ gợn sóng của các búp sóng phụ của cửa sổ).
Kết quả thiết kế như hình 4.

![hinh4](/images/bai-02/fir_example.png)  
Hình 4. Kết quả thiết kế
$$A_s=52dB, \ R_p = 0.0394 dB$$

*Như vậy bài này đã trình bày thiết kế bộ lọc FIR bằng phương pháp cửa sổ dựa vào các loại cửa sổ đã được cung cấp sẵn bởi Matlab. Đồng thời qua một số dòng code đơn giản có thể quan sát trực quan các thông số của bộ lọc.*

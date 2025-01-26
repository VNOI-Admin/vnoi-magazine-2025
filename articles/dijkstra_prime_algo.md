---
layout: article
meta:
  title: Thuật toán tìm số nguyên tố của Dijkstra
  author: Võ Đức Đoàn
  authorInfo: 11 Tin, Trường THPT Chuyên Nguyễn Tất Thành
---

Edsger W. Dijkstra là một nhà khoa học máy tính có nhiều đóng góp trong lĩnh vực ngôn ngữ lập trình và khoa học máy tính, nổi tiếng nhất chính là thuật toán Dijsktra - một thuật toán tìm đường đi ngắn nhất, được ông nghĩ ra trong vòng _20 phút_ khi đi cafe cùng vị hôn thê của mình.

Thế nhưng, bạn có biết rằng Dijsktra cũng phát minh ra một thuật toán tìm số nguyên tố không?

## Số nguyên tố

Nhắc lại định nghĩa: Số nguyên tố là một số tự nhiên có đúng hai ước nguyên dương: $1$ và chính nó. Các số tự nhiên còn lại được gọi là hợp số.

Ví dụ:

- $7$ là số nguyên tố vì nó chỉ có $2$ ước là $1$ và $7$
- $12$ không là số nguyên tố vì nó có $6$ ước là $1$, $2$, $3$, $4$, $6$ và $12$

Vì các số nguyên tố phân bố theo một quy luật không xác định nên việc xác định các số nguyên tố trong một khoảng số là một thử thách lớn. Các nhà toán học thích sự thử thách, và giờ đây ta có vô số cách xác định số nguyên tố.

Ta có bài toán:

> _Cho một số tự nhiên $N$. Hãy tìm và in ra tất cả các số nguyên tố từ $1$ đến $N$._

Trước khi giới thiệu về cách giải quyết bài toán này bằng thuật toán tìm số nguyên tố của Dijkstra, ta sẽ điểm qua một số thuật toán tìm số nguyên tố nhằm giải quyết được bài toán trên.

### Phương pháp thử sai

Một trong những cách đơn giản nhất để kiểm tra một số $x$ có phải là một số nguyên tố không chính là xét tất cả các số từ $2$ đến $x - 1$ và kiểm tra nếu $x$ có chia hết cho số nào trong khoảng này không. Nếu như không tồn tại số nào thỏa mãn thì $x$ số nguyên tố, không thì ngược lại.

```C++
bool isPrime(int x){
    if(x <= 1) return 0; // số 1 không phải số nguyên tố
    for(int i = 2; i < x; ++i){
        if(x % i == 0) return 0;
    }
    return 1;
}
```

Độ phức tạp của thuật toán này sẽ là $O(n)$ với $n = x$.

Ta có thể giảm bớt số lượng thao tác cần thực hiện bằng cách chỉ xét các số nằm trong khoảng từ $2$ đến $\sqrt{x}$.

Tại sao chỉ cần xét đến $\sqrt{x}$ ? Ví dụ với số $12$. Ta có $\sqrt{12} > 3$ và các ước của $12$ nhỏ hơn $\sqrt{12}$ chính là các số $1$, $2$ và $3$. Dễ thấy, khi lấy $12$ chia cho các ước này, ta dễ dàng tìm được các ước số còn lại của $12$:

- $4 = \frac{12}{3}$
- $6 = \frac{12}{2}$
- $12 = \frac{12}{1}$

Khi này, độ phức tạp của phương tháp thử sai được giảm xuống còn $O(\sqrt{n})$.

```cpp
bool isPrime(int x){
    if(x <= 1) return 0;
    for(int i = 2; i * i <= x; ++i){ // i <= sqrt(x) <=> i * i <= x
        if(x % i == 0) return 0;
    }
    return 1;
}
```

Sau khi đã có thuật toán xác định $x$ có phải số nguyên tố không, ta có thể giải quyết bài toán trên: Ta xét các số từ $1$ đến $n$, nếu số đó là số nguyên tố, ta in số đó ra.

```cpp
for(int i = 1; i <= n; ++i){
    if(isPrime(i)) cout << i << '\n';
}
```

Độ phức tạp của chương trình sẽ là $O(n \sqrt{n})$.

Vì chương trình của ta không cần lưu bất kì thông tin gì thêm nên độ phức tạp bộ nhớ sẽ là $O(1)$.

### Tối ưu

Nhìn lại bài toán, ta nhận thấy chương trình có thể được tối ưu hơn nữa cho dạng bài này bằng cách tận dụng các số nguyên tố tìm được những bước trước đó để kiểm tra số $x$.

```cpp
vector<int> primes;

bool isPrime(int x){
    if(x <= 1) return 0;
    for(int i : primes){
        if(i * i > x) break;
        if(x % i == 0) return 0;
    }
    return 1;
}

for(int i = 1; i <= n; ++i){
    if(isPrime(i)) {
        cout << i << '\n';
        primes.push_back(i);
    }
}
```

Thuật toán bây giờ có độ phức tạp $O(n \times \pi{(n)})$, với $\pi{(n)}$ là số lượng số nguyên tố từ $1$ đến $n$. Theo [định lý số nguyên tố](https://vi.wikipedia.org/wiki/%C4%90%E1%BB%8Bnh_l%C3%BD_s%E1%BB%91_nguy%C3%AAn_t%E1%BB%91), ta có: $\pi{(n)} \approx \frac{n}{\log_e{n}}$, nên độ phức tạp trên sẽ tương đương: $O(n \times \pi{(n)}) \approx O(n\frac{n}{\log_e{n}})$.

Độ phức tạp bộ nhớ bây giờ tăng lên thành $O(\pi{(n)}) \approx O(\frac{n}{\log_e{n}})$ vì chương trình giờ đây phải lưu thêm danh sách các số nguyên tố từ $1$ đến $n$.

### Thuật toán sàng số nguyên tố

Thuật toán sàng số nguyên tố cho phép ta xác định các số nguyên tố trong khoảng từ $1$ đến $N$ với độ phức tạp nhanh ngất ngưỡng: $O(n \log{\log{n}})$.

Thuật toán được thực hiện dựa trên nhận định sau: nếu $p$ là một số nguyên tố thì các bội số khác $p$ của $p$ như $2p, 3p, 4p,...$ không phải là một số nguyên tố.

Từ đây ta có mô tả thuật toán:

Ta có một mảng đánh dấu `notPrime` có $n$ phần tử được đánh số từ $1$. Ban đầu, `notPrime[1] = 1` vì $1$ không phải là số nguyên tố.

Ta duyệt các số từ $2$ đến $n$, với mỗi số $x$ được duyệt, ta có 2 trường hợp:

- `notPrime[x] = 0`: số $x$ không được đánh dấu $\rightarrow$ $x$ là số nguyên tố. Khi này, bắt đầu từ bội số thứ $x$ của $x$, ta đánh dấu `notprime[x*x] = 1`, `notprime[(x + 1)*x] = 1`, ..., cho tới `notprime[(n/x)*x] = 1`.
- `notPrime[x] = 1`: số $x$ được đánh dấu nên $x$ không phải số nguyên tố. Khi này ta không làm gì cả.

Sau khi kết thúc thuật toán, vị trí của các `notPrime` không được đánh dấu sẽ là một số nguyên tố.

```cpp
const int N = 1e6 + 10; // N > n
bool notPrime[N];
for (int i = 2; i <= n; ++i) {
    if (notPrime[i] == 0) {
        for (int j = i * i; j <= n; j += i){
            notPrime[j] = 1;
        }
    }
}
for(int i = 2; i <= n; ++i){
    if(notPrime[i] == 0) cout << i << '\n';
}
```

**Câu hỏi:** không phải ta nên bắt đầu đánh dấu từ bội số thứ hai của mỗi số nguyên tố sao?

**Trả lời:** Đây là phương án tối ưu khi sàng số nguyên tố. Dễ thấy, khi xóa các bội số của các số nguyên $p$, các bội số từ $2$ đến $p - 1$ đều đã được đánh dấu ở các lần trước đó. Ví dụ, ta không cần đánh dấu bội số thứ $3$ của số nguyên tố $7$, tức số $21$, bởi vì số này đã được đánh dấu khi số nguyên tố $3$ thực hiện việc đánh dấu các bội số.

Một cách để tối ưu hơn nữa thuật toán sàng chính là ta chỉ cần duyệt đến $\sqrt{n}$ với cách giải thích tương tự với phần trước.

```cpp
for (int i = 2; i * i <= n; ++i) {
    if (notPrime[i] == 0) {
        for (int j = i * i; j <= n; j += i){
            notPrime[j] = 1;
        }
    }
}
```

Mặc dù thuật toán sàng tối ưu hơn nhiều so với phương pháp thử sai, thuật toán lại sử dụng lượng bộ nhớ lớn, có độ phức tạp là $O(n)$, vì phải đánh dấu các số trong khoảng.

## Thuật toán của Dijkstra

Thuật toán tìm số nguyên tố của Dijkstra là một thuật toán đầy "thanh lịch" trong việc tìm số nguyên tố.

Thuật toán áp dụng $2$ tính chất:

- $2$ là số nguyên tố đầu tiên và nhỏ nhất trong danh sách số nguyên tố
- Theo [định đề Bertrand](https://vi.wikipedia.org/wiki/%C4%90%E1%BB%8Bnh_%C4%91%E1%BB%81_Bertrand), với $P_n$ là số nguyên tố thứ $n$, ta có nhận xét:
  $$P_{n + 1} < 2 \times P_n \le P_n^2$$

Ta có mô tả thuật toán như sau:

Ta có một danh sách lưu các số nguyên tố dưới dạng cặp giá trị $(P, tP)$, với $P$ là một số nguyên tố, và $tP$ là bội số thứ $t$ của số nguyên tố ấy ($t > 1$).

Ta lưu cặp số nguyên tố $2$ và bội số thứ $2$ của nó vào danh sách: $(2, 4)$.

Xét các số $x$ từ $3$ đến $n$:

- Nếu $x$ nhỏ hơn bội số nhỏ nhất trong tất cả các bội số được lưu trong danh sách, $x$ là một số nguyên tố. Khi này thêm cặp $x$ và bội số thứ $x$ của nó vào danh sách: $(x, x^2)$.
  Ví dụ: khi ta xét số $3$, danh sách hiện tại lưu cặp giá trị $(2, 4)$. Vì $3 < 4$, $3$ là số nguyên tố. Ta in $3$ và thêm cặp giá trị $(3, 9)$ vào danh sách.
- Nếu $x$ bằng một hoặc nhiều bội số nhất trong danh sách thì $x$ không là một số nguyên tố. Khi này, tăng giá trị của (các) bội số một khoảng bằng số nguyên tố $x$ tương ứng trong cặp giá trị.
  Ví dụ như khi ta xét tới số $4$, danh sách của ta hiện tại đang lưu $2$ cặp giá trị $(2, 4)$ và $(3, 9)$. Vì $4$ bằng bội số của $2$ nên $4$ không phải số nguyên tố. Khi đó cặp $(2, 4)$ sẽ được cập nhật lại thành $(2, 6)$.

Sau khi duyệt xong, các số trong danh sách sẽ lưu tất cả các số nguyên tố từ $1$ đến $n$.

Ta có cài đặt trên C++, sử dụng `priority_queue`:

```cpp
// lưu các cặp (tP, P), pq-min theo tP
priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq;
pq.push({4, 2});

if(n <= 2) cout << 2 << '\n';

for(int i = 3; i <= n; ++i){
	if(i < pq.top().first) {
		pq.push({i * i, i}); // Thêm (tP, P) vào danh sách
		cout << i << '\n';
	} else if(i == pq.top().first){
		while(i == pq.top().first){
			pair<int, int> a = pq.top(); pq.pop();
			a.first += a.second; // thay đổi (tP, P) thành ((t + 1)P, P)
			pq.push(a);
		}
	}
}
```

Việc duyệt các số từ $1$ đến $n$ mất $O(n)$ thao tác, mỗi thao tác lại việc tìm bội số nhỏ nhất có độ phức tạp $O(\log{n})$, nên ta có độ phức tạp $O(n\log{n})$ cho thuật toán tìm số nguyên tố theo kiểu Dijkstra.

Thụât toán cũng phải lưu các số nguyên tố trong khoảng từ $1$ đến $n$ nên độ phức tạp bộ nhớ sẽ là $O(\pi{(n)}) \approx O(\frac{n}{\log_e{n}})$.

Ta tối ưu thuật toán bằng cách loại bỏ các số nguyên tố $P$ khỏi danh sách nếu bội số hiện tại của nó $tP$ lớn hơn $n$.

```cpp
if(i < pq.top().F) {
	if(i * i <= n) pq.push({i * i, i});
	cout << i << '\n';
} else {
	while(pq.size() && i == pq.top().F){
		auto a = pq.top(); pq.pop();
		a.first += a.second;
		if(a.first < n) pq.push(a);
	}
}
```

## So sánh với thuật toán Dijsktra

### Thuật toán sàng số nguyên tố với thuật toán Dijkstra

So sánh với thuật toán sàng, thuật toán của Dijsktra sử dụng ít bộ nhớ hơn một cách đáng kể $(O(\pi{(n)}) \le O(n))$. Đấy là bởi thay vì phải lưu tất cả các bội của một số nguyên tố giống như thuật toán sàng, thuật toán của Dijkstra chỉ lưu một bội số cần thiết.

Tuy nhiên, vì cách lưu trữ và quản lí các bội số này khá phức tạp (phải sử dụng `priority_queue`), do đó độ phức tạp thời gian cũng lớn hơn so với thuật toán sàng.

### Thử sai với thuật toán Dijkstra

Thuật toán của Dijkstra có độ phức tạp thời gian nhỏ hơn so với phương pháp thử sai. Tuy nhiên, phương pháp thử sai lại sử dụng ít bộ nhớ hơn so với thuật toán của Dijkstra.

Có thể thấy, thuật toán Dijkstra là một sự cân bằng giữa thời gian và bộ nhớ, với thuật toán chạy nhanh hơn phương pháp thử sai và sử dụng ít bộ nhớ hơn thuật toán sàng số nguyên tố.

## Có nên sử dụng thuật toán tìm số nguyên tố của Dijkstra không?

Thuật toán của Dijkstra tuy "không có điểm yếu" nhưng chính vì sự cân bằng đó cũng khiến thuật toán "không có điểm mạnh" nếu so với hai thuật toán còn lại - hai thuật toán có điểm nổi trội rõ rệt: thử sai sử dụng rất ít bộ nhớ, và thời gian chạy của sàng là khá nhỏ.

Ngay cả trong bài viết của Dijkstra nói về thuật toán này cũng viết:

> _"But everybody knows that the most effienct way to generate prime numbers is by using the Sieve of Erathosthenes."_

_Tạm dịch: Ai cũng biết cách hiệu quả nhất để sinh số nguyên tố chính là sử dụng thuật thuật toán sàng Eratosthenes._

Tuy nhiên, đây cũng là một thuật toán rất hay ho mà bạn có thể học!

## Tham khảo

[Notes on Structured Programming](https://www.cs.utexas.edu/~EWD/ewd02xx/EWD249.PDF) - Edsger W. Dijkstra.

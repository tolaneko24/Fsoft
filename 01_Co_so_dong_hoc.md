# 01 — Cơ sở động học xe: giải thích từng công thức từ đầu

> **Nguồn:** Section 3.1, Figure 2 và công thức (1)–(6), tr. 5–6 của bài báo Chen và cộng sự (2026).
>
> **Mục tiêu:** tự tính được một bước di chuyển của xe từ `(x, y, θ)`, hiểu được vì sao Hybrid A* không được vẽ đường tùy ý.

## 1. “Động học” là gì? Khác “động lực học” thế nào?

- **Động học (kinematics):** Nếu biết xe đang ở đâu, tốc độ và góc lái, nó sẽ **đi về đâu**? Không hỏi lực đẩy/mô-men chính xác từ động cơ.
- **Động lực học (dynamics):** Bổ sung lực, quán tính, trượt bánh, khối lượng, lực ma sát...

**[BÀI BÁO]** Khi đỗ ở tốc độ thấp, tác giả giả sử bánh xe **không trượt ngang** đáng kể. Bởi vậy họ dùng mô hình xe đạp động học (*kinematic bicycle model / single-track model*): thay hai bánh trước thành một bánh trước lý tưởng, hai bánh sau thành một bánh sau lý tưởng.

## 2. Hệ tọa độ: trục x,y nằm ở đâu?

Trước khi tính, ta phải vẽ hai trục trên mặt đất:

- `x`: hướng ngang trên bản đồ; `y`: hướng dọc; đơn vị mét (m).
- Gốc tọa độ `(0,0)`: một điểm cố định tùy chọn, có thể là góc bãi đỗ.
- `θ` hoặc `φ`: hướng của **đầu xe** so với chiều dương trục x.
  - `θ = 0°`: đầu xe nhìn theo +x;
  - `θ = 90°`: đầu xe nhìn theo +y;
  - `θ = 180°`: đầu xe nhìn theo -x.

Bài báo dùng `(x_r,y_r,φ)` là vị trí **tâm trục sau** (rear-axle center) và hướng thân xe. Vì sao dùng tâm trục sau? Với mô hình xe đạp, công thức quay trở nên đơn giản khi đặt điểm tham chiếu ngay tại đó.

### Phân biệt ba loại “góc”

- **`θ` (heading/yaw):** góc thân xe trên **bản đồ**.
- **`δ` (steering):** góc bánh trước lệch so với thân xe.
- **`Δθ` (change in heading):** xe đã quay thêm bao nhiêu sau một quãng đường.

Ví dụ `θ = 90°` và `δ = 0°` nghĩa là xe đang nhìn thẳng lên trên và **không đánh lái**, chứ không phải xe đánh lái 90°.

## 3. Tại sao công thức xuất hiện `cos θ` và `sin θ`?

### 3.1. Cơ sở toán: tách một mũi tên thành hai thành phần

Nếu đi đoạn đường dài `s = 1 m` theo hướng tạo góc `θ` với trục x, thì:

\[
\Delta x=s\cos\theta,\qquad \Delta y=s\sin\theta.
\]

Đây là phép chiếu từ hình tam giác vuông: `cos = cạnh kề / cạnh huyền`, `sin = cạnh đối / cạnh huyền`.

Ví dụ `θ=30°`, đi `s=1 m`:

- `cos 30° ≈ 0,866` nên `Δx≈0,866 m`;
- `sin 30° =0,5` nên `Δy=0,5 m`.

Bạn không cần nhớ bí ẩn nào: xe đi theo đường xiên nên phải chia thành hai phần ngang và dọc.

### 3.2. Từ quãng đường sang vận tốc

**Vận tốc** là quãng đường thay đổi trong một đơn vị thời gian. Nếu `v=1 m/s` và góc thân xe `θ`, thì:

\[
\dot x_r=v\cos\theta,\qquad \dot y_r=v\sin\theta.
\]

Dấu chấm trên chữ `x` là đạo hàm theo thời gian: `\dot x = dx/dt` (đọc: tốc độ thay đổi tọa độ x theo giây).

**[BÀI BÁO]** Đây là **công thức (1)**. Nó không phải quy tắc riêng Hybrid A* mà xuất phát từ lượng giác.

## 4. Tại sao xe không thể trượt ngang?

Bánh xe thông thường lăn theo hướng mặt bánh, không trượt sang bên trong điều kiện lái tốc độ thấp. Đây là **ràng buộc không toàn phương** (*nonholonomic constraint*).

Hiểu theo trải nghiệm: đặt xe quay ngang so với ô đỗ, bạn **không thể** bấm một nút để xe tự tịnh tiến sang trái 1 m mà vẫn giữ nguyên góc thân xe như một quân cờ.

**[BÀI BÁO]** Công thức (2)–(3) mô tả điều kiện không trượt ngang tại bánh trước và bánh sau. Đặc biệt `(3)` ghi `\dot y_r=0` phải được hiểu theo **hệ trục gắn với bánh sau/thân xe**; nếu hiểu là `y` trong bản đồ luôn không đổi thì mâu thuẫn với công thức (1). Đây là điểm cần tránh nhầm khi đọc ký hiệu trong bài.

## 5. Bán kính quay: tại sao đánh lái lớn hơn thì quay gắt hơn?

Gọi:

- `L`: **chiều dài cơ sở** = khoảng cách giữa tâm trục bánh trước và tâm trục bánh sau; không phải chiều dài toàn thân xe;
- `δ`: góc lái bánh trước;
- `R`: bán kính quỹ đạo của tâm trục sau khi xe quay đều.

Từ hình học xe đạp (tam giác với cạnh đối `L`, cạnh kề `R`):

\[
\tan\delta=\frac{L}{R} \quad\Longrightarrow\quad
\boxed{R=\frac{L}{\tan\delta}}.
\]

**`tan` là gì?** Trong tam giác vuông: `tan δ = cạnh đối / cạnh kề`. **Dấu `⇒`** nghĩa là chuyển vế được biểu thức bên phải.

Ví dụ `[DIỄN GIẢI]`:

- `L = 2,5 m`, `δ=30°`, `tan30°≈0,577`;
- `R = 2,5 / 0,577 ≈ 4,33 m`.

Nếu giữ nguyên `L` nhưng chỉ đánh lái `δ=10°`, `tan10°≈0,176`, `R≈14,2 m`. **Góc lái nhỏ → vòng quay rộng; góc lái lớn → vòng quay hẹp.**

**[BÀI BÁO]** Đây là công thức (5). Với thông số mô phỏng ở Table 1: `L=2,94 m`, `δ_max=0,4556 rad`, suy ra `R≈2,94/tan(0,4556)≈6,0 m`, phù hợp `R_min=6,00 m` công bố. `rad` là radian; đổi sang độ bằng `độ = rad × 180/π`, nên `0,4556 rad ≈ 26,1°`.

## 6. Công thức quay đầu xe `θ̇ = v tanδ / L` từ đâu ra?

Bạn đã có:

1. Khi xe đi quanh một đường tròn bán kính `R`, độ dài cung `s` và góc quay `θ` (radian) thỏa `s=Rθ`.
2. Chia hai vế theo thời gian: `ds/dt = R × dθ/dt`.
3. Vì `ds/dt=v`, vậy `v=R θ̇`, hay `θ̇ = v/R`.
4. Thay `R=L/tanδ` vừa suy ra:

\[
\boxed{\dot\theta=\frac{v}{L}\tan\delta}.
\]

**[BÀI BÁO]** Đây là công thức (4), xuất hiện trong mô hình tổng hợp (6).

### Ví dụ số

`L=2,5m`, `v=1m/s`, `δ=30°`:

\[
\dot\theta=\frac{1}{2,5}\times0,577\approx0,231\ \mathrm{rad/s}.
\]

Nếu giữ tốc độ và góc lái khoảng 1 giây, hướng thân xe đổi khoảng `0,231 rad ≈ 13,2°`. Đây chỉ là xấp xỉ quay đều, chưa có chuyển động điều khiển chuyển tiếp.

## 7. Gộp thành mô hình xe đạp đầy đủ

**[BÀI BÁO, công thức (6)]**

\[
\boxed{\begin{cases}
\dot x_r=v\cos\theta\\
\dot y_r=v\sin\theta\\
\dot\theta=\dfrac{v}{L}\tan\delta
\end{cases}}
\]

Mỗi dòng có một nhiệm vụ:

- Dòng 1: xe thay đổi tọa độ x nhanh bao nhiêu;
- Dòng 2: xe thay đổi tọa độ y nhanh bao nhiêu;
- Dòng 3: đầu xe quay nhanh bao nhiêu.

**Đầu vào:** `v, δ`; **trạng thái hiện tại:** `x_r, y_r, θ`; **đầu ra:** vận tốc thay đổi trạng thái.

## 8. Tính tư thế kế tiếp bằng các bước nhỏ

Đạo hàm chỉ nói tốc độ thay đổi **ngay lúc này**. Máy tính muốn dự đoán “sau 0,1 giây” thì cần *tích phân số* (cộng dồn thay đổi theo từng bước).

**[ĐỀ XUẤT TRIỂN KHAI]** Dùng phương pháp Euler đơn giản với bước `Δt`:

\[
\begin{aligned}
x_{k+1}&=x_k+v_k\cos\theta_k\,\Delta t,\\
y_{k+1}&=y_k+v_k\sin\theta_k\,\Delta t,\\
\theta_{k+1}&=\theta_k+\frac{v_k}{L}\tan\delta_k\,\Delta t.
\end{aligned}
\]

Chỉ số `k` nghĩa là thời điểm hiện tại; `k+1` nghĩa là bước ngay sau đó. `Δt` là khoảng thời gian của **một bước giả lập**. Đây là phép rời rạc hóa (6), **không phải công thức riêng đánh số trong bài báo**.

Ví dụ `x=0`, `y=0`, `θ=0`, `v=0,5 m/s`, `L=2,5 m`, `δ=20°`, `Δt=0,1 s`:

- `Δx=0,5×cos0×0,1=0,05m`;
- `Δy=0,5×sin0×0,1=0m`;
- `Δθ=(0,5/2,5)×tan20°×0,1≈0,00728rad≈0,417°`.

Vậy tư thế sau một bước xấp xỉ `(0,05 m, 0 m, 0,417°)`. Sau nhiều bước mới tạo thành **đường cong**.

## 9. Dấu âm khi đi lùi có ý nghĩa gì?

**[DIỄN GIẢI]** Chọn `v>0` là tiến, `v<0` là lùi. Giữ cùng `δ>0`:

\[
\dot\theta=\frac{v}{L}\tan\delta.
\]

Đổi từ tiến sang lùi (`v` đổi dấu), `θ̇` cũng đổi dấu. Nghĩa là **cùng góc đánh lái, hướng thân xe thay đổi ngược khi đang lùi**. Đây là lý do đường đỗ có thể cần nhiều đoạn tiến/lùi.

## 10. Ràng buộc áp dụng vào thuật toán

- **Giới hạn góc lái:** `|δ| ≤ δ_max`;
- **Giới hạn bán kính quay:** `R ≥ R_min = L/tan(δ_max)` (với `0<δ_max<π/2`);
- **Không chạy ngang:** chỉ sinh các bước phù hợp mô hình (6);
- **Tốc độ thấp:** mô hình giả định không trượt ngang đáng kể;
- **Thân xe có chiều dài và chiều rộng:** mô hình (6) chỉ dự đoán **điểm tham chiếu**, còn kiểm tra va chạm phải xét toàn bộ thân xe.

## 11. Những nhầm lẫn phổ biến

1. **Nhầm `L` với chiều dài thân xe.** Xe dài 4,86 m trong Table 1 nhưng chiều dài cơ sở chỉ 2,94 m.
2. **Nhầm `θ` và `δ`.** Một cái là góc thân xe trên bản đồ; một cái là góc bánh trước so với thân.
3. **Nhầm `v<0` là thuật toán tìm kiếm ngược.** `v<0` là xe vật lý đang lùi; tìm kiếm ngược là chiều thiết kế quỹ đạo.
4. **Nhầm đường cong hình học là xe theo được ở mọi tốc độ.** Mô hình chỉ giả thiết lái tốc độ thấp, không kiểm chứng động lực học tốc độ cao.

## 12. Tự kiểm tra có đáp án

**Bài 1:** `L=2m`, `δ=45°`. Bán kính quay? → `tan45°=1` nên `R=2m`.

**Bài 2:** Xe nhìn theo `+y`, `θ=90°`, đi thẳng `1m` thì thay đổi tọa độ? → `Δx≈0`, `Δy≈1m`.

**Bài 3:** Tăng `δ` từ `10°` lên `20°` có làm vòng quay lớn ra? → Không, bán kính quay nhỏ đi.

**Bài 4:** Vì sao phải học file sau? → Vì dù tâm trục sau không chạm vật cản, **mũi và đuôi xe vẫn có thể va chạm**.

**Đi tiếp:** [02_Kiem_tra_va_cham.md](02_Kiem_tra_va_cham.md).

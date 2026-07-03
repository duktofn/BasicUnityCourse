## 1. Course Overview

Khóa học này được thiết kế cho người mới hoàn toàn với Unity, với mục tiêu sau 10 buổi học (khoảng 1 tháng), học viên không chỉ nắm được kiến thức nền tảng của Unity Engine mà còn **hoàn thiện được một game nhỏ do chính mình tự tay xây dựng từ đầu đến cuối**.

**Hình thức học:** 
- Học Online qua Discord
- Sau mỗi buổi, sẽ có bài tập về nhà sử dụng các kiến thức vừa được dạy để làm ra các features trong game của bản thân có sử dụng kiến thức đó

**Phiên bản Unity sử dụng:** Unity 6000.3.18f1 (Unity 6.3 LTS).

**Lý do chọn phiên bản này:**
- Tuy ở trên trường sử dụng nhiều phiên bản 2022.3.62f2 do đó là một phiên bản ổn định nhưng đã hết vòng đời được hỗ trợ.
- Đây là bản **LTS (Long-Term Support)** mới nhất tại thời điểm xây dựng khóa học.
- Tài liệu chính thức ([Unity 6.3 User Manual](https://docs.unity3d.com/6000.3/Documentation/Manual/UnityManual.html)) được cập nhật đầy đủ và đồng bộ với phiên bản đang dạy, giảm thiểu tình trạng học viên tra cứu nhầm tài liệu của bản cũ/mới gây sai lệch UI hoặc API.

---

## 1.2. Requirements

### Kiến thức nền tảng cần có trước khi học

- Biết những khái niệm lập trình cơ bản: biến, kiểu dữ liệu, vòng lặp, điều kiện if/else, hàm — không yêu cầu biết C# trước, nhưng cần đã từng lập trình ở bất kỳ ngôn ngữ nào (Python, Java, JavaScript...).
- Không yêu cầu kiến thức về game development hay OOP nâng cao — khóa học sẽ giới thiệu các khái niệm Unity-specific (MonoBehaviour, Component, Prefab...) từ đầu.
- Cần biết về OOP cơ bản (class, object, access modifier, khác nhau giữa data type và reference type)

### Phần mềm cần cài đặt trước buổi 1
1. **Unity Hub** (tải tại [unity.com/download](https://unity.com/download)) — dùng để quản lý và cài các phiên bản Unity Editor.
2. **Unity Editor 6000.3.18f1** 
3. **Code editor:** Visual Studio Code hoặc JetBrains Rider (Khuyến khích sử dụng VSCode nếu đã có setup cho Unity từ trước, khì chọn Rider).
4. **Git** — cài Git CLI (hoặc Fork).
5. **Tài khoản GitHub** — tạo sẵn trước buổi 1 để dùng ngay trong phần setup repo.
6. **Tài khoản Unity ID** — bắt buộc để kích hoạt Unity Hub/Editor lần đầu, đăng ký miễn phí tại [id.unity.com](https://id.unity.com).

### Mục tiêu cá nhân cần chuẩn bị

Học viên nên nghĩ trước (không cần hoàn thiện) một ý tưởng game đơn giản, thuộc thể loại dễ hoàn thành trong 1 tháng với kiến thức cơ bản — ví dụ: endless runner, platformer 2D đơn giản, top-down survival nhỏ, hoặc game kiểu Flappy Bird. Ý tưởng nên đơn giản và dễ hoàn thiện.

# 2. Course Contents

#### **Buổi 1 — Làm quen Unity Editor, GameObject, Component**
Nội dung: tour Editor (Scene/Game/Hierarchy/Inspector/Project), khái niệm GameObject-Component, Transform, tạo project mới, setup git repo + .gitignore cho Unity.  

#### **Buổi 2 — MonoBehaviour, First Script, New Input System**
Nội dung: vòng đời script (Awake/Start/Update). Các tính chất cơ bản của Transform và các hàm + properties trong scripting.

#### **Buổi 3 — Prefabs & Instantiate**
Nội dung: tạo Prefab, Instantiate/Destroy object lúc runtime, dùng Prefab Variant cơ bản.  
.

#### **Buổi 4 — Rigidbody, Collider, Physics cơ bản**  
Nội dung: Rigidbody 2D/3D, các loại Collider, sự khác nhau Trigger vs Collision, gravity/force cơ bản. 

#### **Buổi 5 — Xử lý va chạm (Collision/Trigger events), Tag & Layer**  
Nội dung: OnCollisionEnter/OnTriggerEnter, dùng Tag/Layer để phân loại object, viết logic phản ứng khi va chạm (mất máu, ăn điểm, thua...).

#### **Buổi 6 — Raycast & Overlap**  
Nội dung: Raycast 2D/3D. Overlap Box/Sphere/Circle...

#### **Buổi 7 — Coroutine**
Nội dung: Coroutine, IEnumerator, StartCoroutine, cách hoạt động và các chú ý

#### **Buổi 8 — Camera & UI cơ bản (HUD)**  
Nội dung: Camera follow/setup, Canvas, UI Text/Image, hiển thị score/health lên màn hình, update UI từ script. 

#### **Buổi 9 — UI nâng cao (Menu, Button, Game Over screen)**  
Nội dung: Button + OnClick, chuyển đổi giữa các UI panel (Main Menu, Pause, Game Over), Canvas Group để ẩn/hiện UI. 

#### **Buổi 10 — Scene Manager & Game State**  
Nội dung: SceneManager.LoadScene, quản lý trạng thái game (Playing/Paused/GameOver) bằng biến đơn giản (không cần state pattern phức tạp), persist data đơn giản (PlayerPrefs cho high score). 

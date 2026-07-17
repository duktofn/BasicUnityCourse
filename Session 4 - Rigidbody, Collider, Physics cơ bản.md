**Lưu ý:** Buổi này giả định học viên đã biết cách gắn Component vào GameObject qua Inspector (Session 1), viết được MonoBehaviour Script và phân biệt rõ `Update()` với `FixedUpdate()` trong vòng đời script (Session 2). Từ buổi này, `FixedUpdate()` — vốn chỉ mới được nhắc tên ở Session 2 — sẽ chính thức được dùng thật, vì đây là buổi mở đầu phần **Physics**: nền tảng cho mọi chuyển động và va chạm trong game từ giờ trở đi.

---

## 1. Physics Engine trong Unity là gì

Tham khảo: [Introduction to collision](https://docs.unity3d.com/6000.3/Documentation/Manual/CollidersOverview.html)

Unity có sẵn một **physics engine** — hệ thống tự động mô phỏng các quy luật vật lý (trọng lực, lực, va chạm, ma sát...) mà không cần tự viết code tính toán từ đầu. Muốn một GameObject "tham gia" vào hệ thống này, cần tối thiểu 2 thành phần phối hợp với nhau:

- **Rigidbody** — cho biết object đó _chịu_ tác động của vật lý (rơi theo trọng lực, bị đẩy khi va chạm...).
- **Collider** — cho biết _hình dạng_ dùng để phát hiện va chạm của object đó.

Hai component này tách biệt vai trò rõ ràng: Rigidbody quyết định "vật này có di chuyển theo vật lý hay không", Collider quyết định "vật này va chạm được ở đâu, hình dạng nào".

⚠️ **WARNING:** Unity dùng **2 physics engine hoàn toàn tách biệt** cho 3D và 2D — 3D dùng PhysX, 2D dùng Box2D. Thế nên: `Rigidbody` (3D) chỉ tương tác được với `Collider` 3D (Box/Sphere/Capsule/Mesh Collider), còn `Rigidbody2D` chỉ tương tác được với `Collider2D` (Box/Circle/Capsule/Polygon Collider 2D). Gắn lẫn lộn (ví dụ `Rigidbody2D` + `BoxCollider` 3D) sẽ **không** sinh ra va chạm nào cả. Cần xác định ngay từ đầu project của mình là 3D hay 2D để dùng đúng bộ component tương ứng, không nên râu ông nọ cắm cằm bà kia nhé.

---

## 2. Rigidbody — component khiến GameObject "có vật lý"

Tham khảo: [Rigidbody component reference](https://docs.unity3d.com/6000.3/Documentation/Manual/class-Rigidbody.html), [Rigidbody (Scripting API)](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Rigidbody.html)

Gắn `Rigidbody` vào một GameObject là cách nói với physics engine rằng vị trí và góc quay sẽ thông qua Rigidbody để thay đổi (dù rằng vẫn có thể can thiệp trực tiếp vào transform để thay đổi). 
### 2.1. Các thuộc tính quan trọng trên Inspector (Rigidbody 3D)

- **Mass** — khối lượng ảo (kg), ảnh hưởng tới việc vật cần bao nhiêu lực để bị đẩy đi hoặc dừng lại.
- **Linear Damping** — lực cản chuyển động thẳng (tương tự lực cản không khí), giá trị càng cao object càng nhanh dừng lại khi không còn lực tác động.
- **Angular Damping** — lực cản chuyển động xoay, tương tự Linear Damping nhưng áp dụng cho rotation.
- **Use Gravity** — bật/tắt việc object có chịu ảnh hưởng trọng lực toàn cục hay không.
- **Is Kinematic** — bật lên thì object **không** còn bị điều khiển bởi physics engine (không rơi, không bị các va chạm khác đẩy), nhưng vẫn có thể tự di chuyển bằng code và vẫn sinh ra va chạm cho các object khác. Dùng cho platform di chuyển, cửa tự động, hoặc object cần tạm thời chuyển sang điều khiển bằng animation.
- **Collision Detection** — mặc định **Discrete** (nhanh, đủ dùng cho hầu hết trường hợp), chuyển sang **Continuous** khi object di chuyển rất nhanh (đạn, xe tốc độ cao...) để tránh hiện tượng "tunneling" — object bay xuyên qua vật cản mỏng do bước tính toán vật lý bỏ lỡ khoảnh khắc va chạm.
- **Constraints** — khoá riêng từng trục Position/Rotation (X/Y/Z) không cho physics thay đổi, phổ biến nhất là Freeze Rotation X/Z cho nhân vật 3D để tránh bị ngã lật khi va chạm.

> **Tips:** Interpolate (trong mục Interpolation) giúp làm mượt hình ảnh khi camera theo dõi một object di chuyển bằng Rigidbody — vì Rigidbody cập nhật theo nhịp `FixedUpdate` cố định trong khi khung hình render có thể ở tần số khác, không bật Interpolate đôi khi thấy object hơi "giật" nhẹ. Chưa cần dùng ngay ở buổi này, nhưng nên biết tên khi gặp hiện tượng giật hình sau này.

### 2.2. Rigidbody2D — điểm khác so với bản 3D

Tham khảo: [Rigidbody2D component reference](https://docs.unity3d.com/6000.3/Documentation/Manual/class-Rigidbody2D.html), [Rigidbody2D (Scripting API)](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Rigidbody2D.html)

Game 2D dùng `Rigidbody2D` — cùng vai trò với `Rigidbody` nhưng thiết kế riêng cho mặt phẳng XY, có vài điểm khác biệt cần lưu ý:

| Rigidbody (3D)                           | Rigidbody2D                                                                                                                                                          | Ghi chú                                                                                                                                |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| Mass                                     | Mass                                                                                                                                                                 | Giống nhau                                                                                                                             |
| Linear Damping / Angular Damping         | Linear Damping / Angular Damping                                                                                                                                     | Giống nhau                                                                                                                             |
| Use Gravity (checkbox bật/tắt)           | Gravity Scale (hệ số nhân, mặc định 1)                                                                                                                               | 2D linh hoạt hơn: có thể chỉnh mức độ trọng lực riêng cho từng object (0 = không rơi, số âm = trọng lực đảo ngược) thay vì chỉ bật/tắt |
| Is Kinematic (checkbox)                  | Body Type: <br>**Dynamic** (Chịu ảnh hưởng bình thường bởi vật lý)  <br>**Kinematic** (Như đã nói phía trên)<br>**Static** (Hoàn toàn không bị ảnh hưởng bởi vật lý) | 2D tách hẳn 3 chế độ thay vì chỉ 2, rõ ràng hơn khi đọc Inspector                                                                      |
| Freeze Position/Rotation từng trục X/Y/Z | Freeze Position X/Y, Freeze Rotation Z                                                                                                                               | 2D chỉ có 2 trục di chuyển và 1 trục xoay khả dụng                                                                                     |

> **Why:** Vì sao game 2D vẫn cần chọn "Static" ở Body Type thay vì đơn giản là không gắn Rigidbody2D (không gắn = không bị ảnh hưởng bởi vật lý, trong khi đó Static cũng tương tự là không ảnh hưởng bởi vật lý)? Cả hai cách đều ok, nhưng khai báo tường minh Body Type = Static giúp người đọc code/scene sau này hiểu ngay ý đồ (object này cố định vĩnh viễn) mà không cần đoán, đồng thời physics engine 2D có thể gộp nhóm tối ưu các static collider hiệu quả hơn.

⚠️ **WARNING — đổi tên API ở Unity 6:** Kể từ Unity 6 (bao gồm bản 6000.3 đang dùng), `Rigidbody.velocity` đã đổi tên thành **`Rigidbody.linearVelocity`**, và `Rigidbody.drag` / `Rigidbody.angularDrag` đổi thành **`Rigidbody.linearDamping`** / **`Rigidbody.angularDamping`**. Điều này áp dụng cho cả `Rigidbody2D` (`velocity` → `linearVelocity`). Rất nhiều code mẫu, video hướng dẫn cũ trên mạng (trước Unity 6) vẫn dùng tên cũ `.velocity`/`.drag` — nếu copy nguyên sang project 6000.3 sẽ gặp lỗi biên dịch hoặc cảnh báo obsolete. Toàn bộ code trong tài liệu buổi này đều đã dùng đúng tên mới.

---

## 3. Collider — hình dạng vật lý của GameObject

Tham khảo: [Introduction to collision](https://docs.unity3d.com/6000.3/Documentation/Manual/CollidersOverview.html)

Collider định nghĩa **hình dạng** mà physics engine dùng để phát hiện va chạm — hoàn toàn tách biệt với hình dạng hiển thị (Mesh Renderer/Sprite Renderer). Ví dụ, model nhân vật phức tạp thường được bao bởi một Capsule Collider đơn giản thay vì Collider khớp y hệt từng chi tiết, để việc tính toán va chạm nhẹ và nhanh hơn.

### Các loại Collider phổ biến

**3D:**

- **Box Collider** — hình hộp, rẻ nhất về hiệu năng.
- **Sphere Collider** — hình cầu.
- **Capsule Collider** — hình viên nang (2 nửa cầu nối bằng trụ), thường dùng cho nhân vật.
- **Mesh Collider** — bám theo đúng hình dạng mesh, tốn hiệu năng nhất, dùng khi thật sự cần độ chính xác cao (địa hình tĩnh).

**2D:**

- **Box Collider 2D**, **Circle Collider 2D**, **Capsule Collider 2D** — tương ứng bản 2D của 3 loại trên.
- **Polygon Collider 2D** — vẽ theo đường viền đa giác tuỳ ý, linh hoạt cho sprite hình dạng phức tạp.

> **Tips:** Có thể gắn **nhiều Collider** trên cùng một GameObject (hoặc rải trên các GameObject con) để tạo ra một hình dạng va chạm phức tạp hơn từ nhiều primitive đơn giản cộng lại — gọi là **Compound Collider**. Cách này gần như luôn tốt hơn (nhanh hơn) so với dùng một Mesh Collider duy nhất bám sát toàn bộ hình dạng phức tạp.

⚠️ **WARNING:** Nếu gắn **Mesh Collider** vào một GameObject có **Rigidbody** không tĩnh (không phải static collider), bắt buộc phải tick ô **Convex** trong Inspector của Mesh Collider. Physics engine (PhysX) không tính toán chính xác va chạm giữa hai Mesh Collider không-lồi (non-convex) đang di chuyển với nhau — Unity sẽ báo lỗi hoặc bỏ qua va chạm nếu thiếu Convex. Lưu ý thêm: khi bật Convex, Mesh Collider bị giới hạn tối đa 255 triangle.

---

## 4. Is Trigger — sự khác nhau giữa Collision và Trigger

Tham khảo: [Introduction to collision](https://docs.unity3d.com/6000.3/Documentation/Manual/CollidersOverview.html)

Mỗi Collider đều có một checkbox tên **Is Trigger** trên Inspector, quyết định object đó thuộc loại nào:

- **Collision** (mặc định, Is Trigger = false) — hai Collider chạm nhau sẽ có **phản ứng vật lý thật**: object bị chặn lại, bật ra, trượt theo bề mặt... giống thế giới thật.
- **Trigger** (Is Trigger = true) — hai Collider vẫn **xuyên qua nhau** như không có gì cản, hoàn toàn không có phản ứng vật lý, nhưng Unity vẫn ghi nhận được sự kiện "đã đi vào / đang ở trong / đã rời khỏi" vùng đó. Rất hữu ích để tạo vùng phát hiện (detection zone) — ví dụ: cổng thu thập vật phẩm (không cần cản đường người chơi), vùng kích hoạt cutscene, vùng nhận biết kẻ địch tới gần.

> **Why:** Vì sao không dùng Collision cho mọi trường hợp rồi tự viết code "hủy" phản ứng vật lý? Vì phản ứng vật lý (đẩy, bật, chặn) được physics engine tính toán và áp dụng ngay trong bước mô phỏng nội bộ — can thiệp để huỷ nó sau đó sẽ chậm hơn, dễ sai lệch, và tốn công không cần thiết. Is Trigger cho physics engine biết ngay từ đầu để bỏ qua bước tính phản ứng, chỉ còn báo sự kiện.

Việc viết code để **phản ứng lại** hai loại sự kiện này (`OnCollisionEnter`, `OnTriggerEnter`...) sẽ học chi tiết ở **Session 5**. Buổi này chỉ cần nắm chắc: bật/tắt Is Trigger ở đâu, và hiểu đúng bản chất khác nhau về mặt hành vi vật lý giữa hai loại.

> **Tips (biết trước, chưa cần nhớ kỹ):** để một cặp Collider — dù là Collision hay Trigger — sinh ra được sự kiện trong code, **ít nhất một** trong hai object tham gia phải có Rigidbody (không tính hai Collider tĩnh chạm nhau, vì physics engine không theo dõi va chạm giữa hai vật không bao giờ di chuyển). Chi tiết đầy đủ về quy tắc này sẽ nhắc lại kỹ ở Session 5.

---

## 5. Gravity — trọng lực trong Unity

Tham khảo: [Physics.gravity (Scripting API)](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Physics-gravity.html)

- **3D:** giá trị trọng lực toàn cục nằm ở `Edit > Project Settings > Physics`, mặc định là `(0, -9.81, 0)` — áp dụng cho **mọi** Rigidbody có `Use Gravity = true` trong project. Có thể đọc/chỉnh qua code bằng `Physics.gravity`.
- **2D:** giá trị trọng lực toàn cục nằm ở `Edit > Project Settings > Physics 2D`, chỉnh qua code bằng `Physics2D.gravity`. Điểm khác biệt: mỗi `Rigidbody2D` còn có thêm **Gravity Scale** riêng — hệ số nhân thêm vào trọng lực toàn cục cho đúng object đó, mặc định là `1`. Ví dụ hai object cùng nằm trong world gravity như nhau, nhưng object A đặt Gravity Scale = `2` sẽ rơi nhanh hơn hẳn object B đang giữ `1`.

```csharp
// Chỉnh trọng lực toàn cục bằng code (3D)
Physics.gravity = new Vector3(0f, -20f, 0f);
```

---

## 6. Force & Velocity — di chuyển vật thể bằng code

Tham khảo: [Rigidbody.AddForce](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Rigidbody.AddForce.html), [ForceMode](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/ForceMode.html), [Rigidbody.linearVelocity](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Rigidbody-linearVelocity.html)

### AddForce — tác động lực

`AddForce()` là cách "đúng chuẩn vật lý" để tác động lực lên một Rigidbody — physics engine sẽ tự tính ra chuyển động hợp lý dựa trên Mass, va chạm hiện có, thay vì object đột ngột đổi hướng bất chấp mọi thứ xung quanh.

```csharp
rb.AddForce(Vector3.up * jumpForce, ForceMode.Impulse);
```

`ForceMode` có 4 lựa chọn, khoá học cơ bản này chỉ cần nắm chắc 2 loại thường dùng nhất:

- **`ForceMode.Force`** — lực tác động liên tục theo thời gian, có tính đến Mass. Dùng khi cần cộng dồn lực mỗi `FixedUpdate` (ví dụ gió thổi liên tục, động cơ đẩy liên tục).
- **`ForceMode.Impulse`** — lực tức thời, có tính đến Mass. Dùng cho hành động xảy ra một lần (nhảy, đấm, đá bóng).

> **Tips:** Object Mass càng lớn thì cùng một lực `AddForce` sẽ tạo ra chuyển động càng ít rõ rệt — đúng với vật lý thật (vật nặng khó đẩy hơn vật nhẹ).

### linearVelocity — gán trực tiếp vận tốc

Thay vì cộng lực, có thể gán thẳng vận tốc hiện tại của Rigidbody — dùng khi cần kiểm soát tốc độ chính xác ngay lập tức, phổ biến nhất cho di chuyển ngang của nhân vật (player movement):

```csharp
rb.linearVelocity = new Vector3(moveInput.x * moveSpeed, rb.linearVelocity.y, moveInput.y * moveSpeed);
```

Lưu ý dòng trên giữ nguyên `rb.linearVelocity.y` — để không ghi đè mất vận tốc rơi/nhảy theo trục dọc đang có sẵn do trọng lực hoặc `AddForce` tạo ra trước đó.

⚠️ **WARNING:** Tuyệt đối **không** di chuyển một Rigidbody (không kinematic) bằng cách gán trực tiếp `transform.position` mỗi frame — cách này bỏ qua hoàn toàn việc tính toán va chạm của physics engine, khiến object có thể xuyên qua vật cản hoặc gây hiện tượng giật/rung. Với Rigidbody thường, dùng `AddForce`/`linearVelocity`; với Rigidbody đang **Is Kinematic**, dùng `Rigidbody.MovePosition()` thay cho gán `transform.position` trực tiếp, để physics engine vẫn tính đúng va chạm với các object khác trên đường đi.

---

## 7. FixedUpdate — vì sao code physics phải đặt ở đây

Tham khảo: [MonoBehaviour.FixedUpdate](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/MonoBehaviour.FixedUpdate.html)

Session 2 đã nhắc: `FixedUpdate()` chạy theo khoảng thời gian **cố định** (mặc định 0.02s), đồng bộ với bước tính toán nội bộ của physics engine — vì vậy mọi thao tác đọc/ghi lên Rigidbody (`AddForce`, gán `linearVelocity`, `MovePosition`...) nên đặt trong `FixedUpdate()`, **không phải** `Update()`.

> **Why:** `Update()` chạy theo framerate — con số này thay đổi liên tục và khác nhau giữa các máy. Physics engine lại tính toán theo bước cố định, độc lập với framerate. Nếu ghi giá trị lên Rigidbody trong `Update()`, có thể ghi đè hoặc bỏ lỡ đúng ngay giữa lúc physics engine đang thực hiện một bước tính toán, gây ra chuyển động giật hoặc không nhất quán giữa các máy có framerate khác nhau — đúng nguyên nhân tương tự lý do phải nhân `Time.deltaTime` đã học ở Session 2, nhưng ở đây là do bước tính toán vật lý, không phải do di chuyển bằng transform.

Vậy input thì đọc ở đâu? Vẫn đọc trong `Update()` như Session 2 đã học — vì `Update()` chạy đúng mỗi frame nên không bỏ lỡ lần nhấn phím nào, trong khi `FixedUpdate()` có thể chạy 0, 1, hoặc nhiều lần trong một frame tuỳ framerate máy. Pattern chuẩn và phổ biến nhất: **đọc/cache input ở `Update()`, áp dụng lực/vận tốc dựa trên input đó ở `FixedUpdate()`** — ví dụ thực hành bên dưới áp dụng đúng pattern này.

---

### Ví dụ thực hành: Di chuyển nhân vật bằng Rigidbody kết hợp Input System

Kết hợp `PlayerControls` đã tạo ở Session 2 (bổ sung thêm một Action `Jump` kiểu **Button**, tạo tương tự cách tạo Action `Move`) với Rigidbody vừa học hôm nay:

```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class PlayerRigidbodyMovement : MonoBehaviour
{
    [SerializeField] private float moveSpeed = 5f;
    [SerializeField] private float jumpForce = 8f;

    private Rigidbody _rb;
    private PlayerControls _controls;
    private Vector2 _moveInput;
    private bool _jumpRequested;

    private void Awake()
    {
        _rb = GetComponent<Rigidbody>();
        _controls = new PlayerControls();
    }

    private void OnEnable()
    {
        _controls.Player.Enable();
    }

    private void OnDisable()
    {
        _controls.Player.Disable();
    }

    private void Update()
    {
        // Đọc input mỗi frame để không bỏ lỡ lần nhấn nào
        _moveInput = _controls.Player.Move.ReadValue<Vector2>();

        if (_controls.Player.Jump.WasPressedThisFrame())
        {
            _jumpRequested = true;
        }
    }

    private void FixedUpdate()
    {
        // Áp dụng chuyển động ngang đúng theo nhịp tính toán vật lý,
        // giữ nguyên vận tốc trục Y (rơi/nhảy) đang có sẵn
        Vector3 horizontalVelocity = new Vector3(_moveInput.x * moveSpeed, _rb.linearVelocity.y, _moveInput.y * moveSpeed);
        _rb.linearVelocity = horizontalVelocity;

        // Consume input nhảy trong FixedUpdate sau khi đã nhảy xong, tránh nhảy nhiều lần cho một lần nhấn
        if (_jumpRequested)
        {
            _rb.AddForce(Vector3.up * jumpForce, ForceMode.Impulse);
            _jumpRequested = false;
        }
    }
}
```

Vài điểm đáng chú ý trong ví dụ:

- Input được **cache** vào `_moveInput`/`_jumpRequested` ở `Update()`, rồi mới **consume** ở `FixedUpdate()` — đúng pattern đã giải thích ở mục 7.
- Nhảy dùng `AddForce` với `ForceMode.Impulse` (lực tức thời một lần), trong khi di chuyển ngang dùng gán thẳng `linearVelocity` (cần phản hồi tức thì theo input, không cần độ trễ tự nhiên của lực cộng dồn).
- `WasPressedThisFrame()` chỉ trả về true đúng một frame duy nhất tại thời điểm vừa nhấn — phù hợp để phát hiện "vừa bấm nút" thay vì `ReadValue` (dùng cho giá trị liên tục như trục di chuyển).

---

## Tài liệu tham khảo (Unity Docs chính thức — bản 6000.3 LTS)

| Chủ đề                                            | Link                                                                                          |
| ------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| Introduction to collision                         | https://docs.unity3d.com/6000.3/Documentation/Manual/CollidersOverview.html                   |
| Rigidbody component reference (Manual)            | https://docs.unity3d.com/6000.3/Documentation/Manual/class-Rigidbody.html                     |
| Rigidbody (Scripting API)                         | https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Rigidbody.html                  |
| Rigidbody2D component reference (Manual)          | https://docs.unity3d.com/6000.3/Documentation/Manual/class-Rigidbody2D.html                   |
| Rigidbody2D (Scripting API)                       | https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Rigidbody2D.html                |
| Rigidbody.linearVelocity                          | https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Rigidbody-linearVelocity.html   |
| Rigidbody2D.linearVelocity                        | https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Rigidbody2D-linearVelocity.html |
| Rigidbody.linearDamping / angularDamping          | https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Rigidbody-linearDamping.html    |
| Box Collider                                      | https://docs.unity3d.com/6000.3/Documentation/Manual/class-BoxCollider.html                   |
| Sphere Collider                                   | https://docs.unity3d.com/6000.3/Documentation/Manual/class-SphereCollider.html                |
| Capsule Collider                                  | https://docs.unity3d.com/6000.3/Documentation/Manual/class-CapsuleCollider.html               |
| Mesh Collider                                     | https://docs.unity3d.com/6000.3/Documentation/Manual/class-MeshCollider.html                  |
| Box Collider 2D                                   | https://docs.unity3d.com/6000.3/Documentation/Manual/class-BoxCollider2D.html                 |
| Circle Collider 2D                                | https://docs.unity3d.com/6000.3/Documentation/Manual/class-CircleCollider2D.html              |
| Polygon Collider 2D                               | https://docs.unity3d.com/6000.3/Documentation/Manual/class-PolygonCollider2D.html             |
| Physics.gravity (Scripting API)                   | https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Physics-gravity.html            |
| Physics2D (Scripting API)                         | https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Physics2D.html                  |
| Rigidbody.AddForce                                | https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Rigidbody.AddForce.html         |
| ForceMode                                         | https://docs.unity3d.com/6000.3/Documentation/ScriptReference/ForceMode.html                  |
| Rigidbody.MovePosition                            | https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Rigidbody.MovePosition.html     |
| MonoBehaviour.FixedUpdate                         | https://docs.unity3d.com/6000.3/Documentation/ScriptReference/MonoBehaviour.FixedUpdate.html  |
| Upgrade Guide to Unity 6.3 (velocity/drag rename) | https://docs.unity3d.com/6000.3/Documentation/Manual/UpgradeGuideUnity63.html                 |
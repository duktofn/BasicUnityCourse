## 1. Scripting trong Unity

### 1.1. Script và vai trò của nó

Tham khảo: [Introduction to programming in Unity](https://docs.unity3d.com/6000.3/Documentation/Manual/intro-to-scripting.html), [Creating scripts](https://docs.unity3d.com/6000.3/Documentation/Manual/creating-scripts.html)

Script trong Unity là các file C# (`.cs`) — về bản chất cũng là một loại asset, được lưu trong folder Assets và là một phần của asset database giống các asset khác (texture, model, audio...).

Vai trò chính của script là giúp **tuỳ biến và mở rộng** khả năng của game/ứng dụng bằng code — vì gần như mọi thứ trong Unity đều có thể được điều khiển hoặc mở rộng thông qua script ở một mức độ nào đó. Cụ thể, script cho phép:

- Tạo ra các **Component** tuỳ biến để điều khiển hành vi của GameObject (khi kế thừa từ `MonoBehaviour`).
- Lưu trữ dữ liệu hiệu quả trong project (khi kế thừa từ `ScriptableObject`).
- Định nghĩa các class/logic C# thông thường không gắn với hệ thống Unity, miễn là tương thích với .NET profile đang dùng trong project.

Điểm cần nhấn mạnh cho học viên: một class C# thông thường (không kế thừa gì từ Unity) vẫn chạy được, nhưng nó sẽ có thêm những khả năng đặc biệt khi kế thừa từ một type có sẵn của Unity. Ví dụ: kế thừa từ `UnityEngine.Object` thì các field của class đó mới gán/kéo-thả được trên Inspector; kế thừa từ `MonoBehaviour` thì script mới gắn được vào GameObject như một Component và nhận được các event function (`Awake`, `Start`, `Update`...) — nội dung này sẽ học chi tiết ở mục 2.

---

### 1.2. Tạo Script đầu tiên

Để tạo script trong Unity ta có 2 cách:

**Cách 1: Thông qua Project Tab**

- Chuột phải vào trong Project Tab > Create > Scripting > Chọn loại scripts muốn tạo (tham khảo hình ảnh)
- Script được tạo ra sẽ nằm trong folder đang được mở trong Project Tab thời điểm đó.

![](Images/Pasted%20image%2020260630212123.png)

**Cách 2: Thông qua Inspector**

- Chọn GameObject trong scene > Add Component trong Inspector > New Script > Đặt tên cho Script > Create and Add > Chọn điểm lưu
- Script được tạo ra theo cách này mặc định là MonoBehaviour Script

![](Images/Pasted%20image%2020260630214500.png)

---

## 1.3. Cấu trúc của một MonoBehaviour Script

Tham khảo: [Creating and Using Scripts](https://docs.unity3d.com/540/Documentation/Manual/CreatingAndUsingScripts.html), [MonoBehaviour Manual](https://docs.unity3d.com/6000.3/Documentation/Manual/class-MonoBehaviour.html)

Một script kết nối được với hệ thống bên trong Unity bằng cách định nghĩa một class kế thừa từ class có sẵn tên **MonoBehaviour**. Có thể hình dung class này như một bản thiết kế (blueprint) để tạo ra một loại Component mới có thể gắn vào GameObject — mỗi lần gắn script vào một GameObject, Unity sẽ tạo ra một instance mới dựa trên blueprint đó.

⚠️**WARNING**: **tên class phải trùng khớp với tên file script**. Đây là yêu cầu bắt buộc để Unity có thể gắn được script đó làm component lên GameObject — nếu đổi tên file mà quên đổi tên class (hoặc ngược lại), script sẽ báo lỗi và không gắn được.

Một script C# mặc định trong Unity (mở từ template) có dạng:

```csharp
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    // Start is called once before the first frame update
    void Start()
    {

    }

    // Update is called once per frame
    void Update()
    {

    }
}
```

`MonoBehaviour` cung cấp framework để gắn script vào GameObject và mang lại các hàm vòng đời (life cycle functions) như `Start`, `Update` giúp việc lập trình với Unity trở nên dễ dàng hơn — thay vì phải tự xây dựng vòng lặp game (game loop) từ đầu.

Lưu ý: bản thân script chỉ là một blueprint định nghĩa Component — chưa có dòng code nào thực sự chạy cho đến khi một instance của script được gắn vào một GameObject trong scene.

---

## 2. Vòng đời của Script (Event Functions)

Tham khảo: 
- [Order of execution for event functions](https://docs.unity3d.com/6000.3/Documentation/Manual/execution-order.html)
- [Script initialisation in Unity doesn't work how you think](https://youtu.be/XpcFvfUYANs?si=YTTvx6Golxomf0R_)

Các "event function" (`Awake`, `OnEnable`, `Start`, `FixedUpdate`, `Update`, `LateUpdate`, `OnDisable`, `OnDestroy`...) là tập hợp những callback có sẵn mà MonoBehaviour script có thể implement để Unity tự động gọi đúng thời điểm trong vòng đời của object, tương ứng với các sự kiện như khởi tạo, kích hoạt, cập nhật theo từng frame, vô hiệu hoá, và huỷ object.

### Awake
- Document chính thức: [MonoBehaviour.Awake](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/MonoBehaviour.Awake.html).
-  `Awake()`được gọi một lần duy nhất khi Scripts được Instantiate (khởi tạo).
- Unity không đảm bảo tất cả các hàm Awake chạy cùng thời điểm với nhau.
- Chạy trước tất cả các hàm Start nào.
- Thường được sử dụng để lấy tham chiếu

> **Tips:** Chỉ sử dụng `Awake` để thiết lập những gì thuộc về **chính GameObject hoặc Script đó**. 

> **Why:**
> Như đã đề cập, Unity không đảm bảo thứ tự chạy `Awake()` giữa các script khác nhau. Nếu script A cố gắng truy xuất dữ liệu từ script B trong `Awake()`, rất có thể script B lúc đó chưa kịp khởi tạo, dẫn đến lỗi `NullReferenceException`. Mọi logic cần sự tương tác chéo giữa các object với nhau nên được được đặt ở hàm `Start()` để đảm bảo an toàn.

### OnEnable
- Document chính thức: [MonoBehaviour.OnEnable](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/MonoBehaviour.OnEnable.html), 
- `OnEnable()` được gọi khi GameObject (hoặc một GameObject cha đang inactive của nó) chuyển sang trạng thái active tại runtime (có thể hiểu là khi game đang chạy dù không được chính xác) 
- Với điều kiện Scripts đó đang ở trạng thái enabled. 
- `OnEnable()` luôn được gọi **sau** `Awake()` và **trước** `Start()` (nếu đây là lần chạy đầu tiên) khi vào Play Mode. 
- Khác với `Awake` (chỉ gọi đúng 1 lần trong đời script), `OnEnable` có thể được gọi **lại nhiều lần** — mỗi lần GameObject được active lại (ví dụ qua `gameObject.SetActive(true)`) hoặc component được enable lại (`enabled = true`).

> **Tips:** `OnEnable()` thường được sử dụng để đăng ký sự kiện đi cặp với hủy đăng ký sự kiện tại `OnDisable()` hoặc reset lại thông số, và trạng thái của Object/Script/Component

>**Why:**
>Sử dụng để đăng ký sự kiện cặp với `OnDisable()` để hủy do khi không active thì ta không có nhu cầu để script này bắt sự kiện nữa (thật ra khi disable dù có bắt sự kiện được cũng không thể gọi hàm)
>Sử dụng để Reset trạng thái thì sẽ áp dụng nhiều trong kỹ thuật Object Pooling (một Design Pattern phổ biến, nhưng thuộc về phần nâng cao nên không đề cập trong khóa học này)

### Start

- Document chính thức: [MonoBehaviour.Start](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/MonoBehaviour.Start.html).
- `Start()` được gọi đúng một lần trong vòng đời của script, vào frame đầu tiên mà script được enable, ngay trước lần gọi `Update()` đầu tiên — và luôn chạy sau toàn bộ các hàm `Awake()` của mọi object trong scene. 

> **Tips:** Được sử dụng để khởi tạo các giá trị ban đầu hoặc lấy reference từ Object khác.

> **Why:**
> Lấy reference từ Object khác do khi này tất cả `Awake()` đã chạy xong nên sẽ không xảy ra NullReferenceException

### Update

- Document chính thức: [MonoBehaviour.Update](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/MonoBehaviour.Update.html). 
- `Update()` được gọi mỗi frame, miễn là MonoBehaviour đang ở trạng thái enabled — đây là hàm được dùng phổ biến nhất để implement logic gameplay, vì hầu hết hành vi game (di chuyển, kiểm tra input, đếm thời gian...) cần chạy liên tục theo từng frame.

### FixedUpdate

- Document chính thức: [MonoBehaviour.FixedUpdate](https://docs.unity3d.com/2020.3/Documentation/ScriptReference/MonoBehaviour.FixedUpdate.html)
- `FixedUpdate` được gọi theo một khoảng thời gian **cố định** (mặc định 0.02s, có thể chỉnh sửa tại Project Settings > Time > Fixed Timestep). 

> **Tips:** Sử dụng cho các công việc tính toán liên quan đến vật lý do hoạt động dựa trên thời gian thực chứ không phải thời gian in-game.

### LateUpdate
- Document chính thức: [MonoBehaviour.LateUpdate](https://docs.unity3d.com/2020.3/Documentation/ScriptReference/MonoBehaviour.LateUpdate.html)
- `LateUpdate()` được gọi mỗi frame, ngay sau khi TOÀN BỘ các lệnh gọi `Update()` của mọi script trong frame đó đã hoàn tất. 

> **Tips:** Trường hợp dùng phổ biến nhất là camera bám theo nhân vật (follow camera) hoặc xử lý các logic cần kết quả của các logic trong update 

> **Why:** 
> Nếu nhân vật di chuyển/xoay trong `Update()`, thì camera nên tính toán vị trí/góc nhìn trong `LateUpdate()` để đảm bảo camera luôn "nhìn thấy" vị trí nhân vật đã được cập nhật mới nhất trong frame đó, tránh hiện tượng giật/lag một frame.

### OnDisable

- Document chính thức: [MonoBehaviour.OnDisable](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/MonoBehaviour.OnDisable.html). 
- `OnDisable()` được gọi khi component hoặc GameObject chứa nó chuyển sang trạng thái disabled/inactive — ví dụ khi gọi `gameObject.SetActive(false)`, set `enabled = false`, hoặc khi tắt scene chứa nó.

> **Tips:** Như đã nói trong phần của `OnEnable()`, nó thường được đi kèm để hủy sự kiện đã đăng ký trong `OnEnable()`

⚠️ **WARNING**: `OnDisable()` **cũng được gọi** ngay trước khi object bị **destroy** (`Object.Destroy` hoặc đóng scene), không chỉ khi bị disable đơn thuần. Vì vậy, nếu code trong `OnDisable()` giả định object vẫn còn tồn tại sau đó (ví dụ định bật lại enabled, hoặc truy cập reference sẽ bị huỷ), cần cẩn trọng — nên kiểm tra null hoặc tách rõ logic "tạm dừng" (chỉ disable) khỏi logic "dọn dẹp vĩnh viễn" (destroy) nếu cần phân biệt hai trường hợp này.

### OnDestroy

- Document chính thức: [MonoBehaviour.OnDestroy](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/MonoBehaviour.OnDestroy.html), 
- `OnDestroy()` được gọi sau khi frame update cuối cùng của object đã chạy xong, ngay trước khi object thực sự bị huỷ — do gọi `Object.Destroy` hoặc do scene chứa nó bị unload.
- `OnDestroy()` chỉ được gọi trên các GameObject **đã từng active** ít nhất một lần trong đời nó; nếu object luôn ở trạng thái inactive từ đầu thì sẽ không có `OnDestroy`. 

> **Tips:** Đây là nơi phù hợp để dọn dẹp tài nguyên do script tự tạo ra (ví dụ huỷ object con được spawn động, đóng kết nối, giải phóng resource...).

### Tóm tắt thứ tự đầy đủ

Gộp lại toàn bộ các event function phổ biến nhất theo đúng trình tự mà Unity gọi, từ lúc object được khởi tạo cho tới lúc bị huỷ:

```
Awake (1 lần)
   ↓
OnEnable (có thể gọi lại nhiều lần, mỗi khi object/component được enable)
   ↓
Start (1 lần — sau khi TOÀN BỘ Awake của scene đã chạy xong)
   ↓
┌─── Vòng lặp mỗi frame ───────────────────────────────--┐
│  FixedUpdate (0, 1 hoặc nhiều lần — theo nhịp cố định) │
│       ↓                                                │
│  Update (đúng 1 lần mỗi frame)                         │
│       ↓                                                │
│  LateUpdate (đúng 1 lần mỗi frame, sau khi Update      │
│              của MỌI script đã chạy xong)              │
└─────────────────────────────────────────────────────---┘
   ↓ (khi object bị disable hoặc sắp bị destroy)
OnDisable (có thể gọi lại nhiều lần — và CŨNG được gọi ngay trước khi destroy)
   ↓ (chỉ khi object thực sự bị huỷ)
OnDestroy (1 lần, chỉ gọi nếu object đã từng active)
```

Một số lưu ý quan trọng khi diễn giải sơ đồ trên:

- `Awake` (toàn bộ object trong scene) → `OnEnable` (toàn bộ object) → `Start` (toàn bộ object) là thứ tự được đảm bảo theo từng "lớp" — tức mọi `Awake` chạy xong rồi mới tới `OnEnable`, mọi `OnEnable` chạy xong rồi mới tới `Start`. Riêng `Unity.SceneManagement.SceneManager.sceneLoaded` event được raise sau `OnEnable` nhưng trước `Start` của toàn bộ object trong scene.
- Trong vòng lặp mỗi frame, thứ tự `FixedUpdate → Update → LateUpdate` được đảm bảo cho **cùng một object**, nhưng Unity **không đảm bảo thứ tự** gọi cùng một hàm (ví dụ `Update`) giữa các **instance khác nhau** của cùng một script hoặc giữa các object khác nhau — kể cả giữa object cha và object con. Muốn kiểm soát thứ tự này, dùng [Script Execution Order](https://docs.unity3d.com/6000.3/Documentation/Manual/script-execution-order.html) trong Project Settings.
- `OnDisable` không đồng nghĩa với `OnDestroy` — disable một GameObject (`SetActive(false)`) **không** kích hoạt `OnDestroy`, object vẫn còn tồn tại trong scene và có thể bật lại (`SetActive(true)` sẽ gọi lại `OnEnable`).

---

## 3. Biến và Inspector

Theo [SerializeField scripting reference](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/SerializeField.html), Unity mặc định chỉ serialize (và hiển thị lên Inspector) các field **public**. Nếu muốn một field **private** vẫn hiển thị và chỉnh được trên Inspector — cách được khuyến nghị hơn để giữ tính đóng gói (encapsulation) — gắn thêm attribute `[SerializeField]` vào field đó.

```csharp
public class PlayerMovement : MonoBehaviour
{
    public float moveSpeed = 5f;       // public: hiện trên Inspector, các class khác truy cập được

    [SerializeField]
    private float jumpForce = 8f;      // private nhưng vẫn hiện trên Inspector nhờ SerializeField
}
```

Lưu ý cho phiên bản 6000.3: theo [Upgrade Guide to Unity 6.3](https://docs.unity3d.com/6000.3/Documentation/Manual/UpgradeGuideUnity63.html), attribute `[SerializeField]` từ bản này chỉ được áp dụng hợp lệ lên **field**, áp lên property/method/type khác sẽ gây lỗi biên dịch (compile-time error) — nếu học viên từng thấy code mẫu cũ gắn `[SerializeField]` lên property, cần biết rằng cách đó sẽ không còn hợp lệ ở 6000.3.

---

## 4. Input — Đọc dữ liệu từ bàn phím với New Input System

Tham khảo: [Input System package — Manual](https://docs.unity3d.com/Packages/com.unity.inputsystem@1.11/manual/index.html), [Quick start guide](https://docs.unity3d.com/Packages/com.unity.inputsystem@1.11/manual/QuickStartGuide.html), [Time.deltaTime](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Time-deltaTime.html)

Khóa học dùng **Input System package** (New Input System) thay vì Input Manager cũ (`Input.GetAxis`), vì đây là hệ thống được chính Unity khuyến nghị cho mọi project tạo mới — hỗ trợ rebind input lúc runtime, nhận input từ nhiều loại thiết bị (keyboard, gamepad, touch...) thông qua cùng một đoạn code, và là nền tảng cần thiết nếu sau này học viên làm việc với các project Unity hiện đại hoặc multiplayer.

### 4.1. Input Actions Asset

New Input System tổ chức input thông qua một asset gọi là **Input Actions Asset** (file `.inputactions`), gồm 3 cấp:

- **Action Map**: nhóm các action theo ngữ cảnh sử dụng (ví dụ Action Map `Player` cho gameplay, `UI` cho menu).
- **Action**: hành động trừu tượng mà code sẽ đọc (ví dụ `Move`, `Jump`) — không gắn cứng với một phím cụ thể nào.
- **Binding**: ánh xạ cụ thể từ một thiết bị/phím vào Action đó (ví dụ phím WASD và Left Stick của gamepad có thể cùng bind vào một action `Move` duy nhất).

Cách tạo: chuột phải trong Project Tab > Create > Input Actions, đặt tên (ví dụ `PlayerControls`), double-click để mở **Input Actions Editor**, tạo Action Map `Player`, thêm Action `Move` với Action Type **Value** và Control Type **Vector2**, rồi bind bằng template có sẵn **"2D Vector Composite"** (gộp 4 phím WASD/mũi tên rời thành một giá trị Vector2 duy nhất — tương đương cách `Input.GetAxis("Horizontal"/"Vertical")` hoạt động ở hệ cũ, nhưng khai báo trực quan qua Editor thay vì cấu hình ẩn trong Input Manager).

![](Images/Pasted%20image%20input-actions-editor.png)

Sau khi bind xong, tick chọn **Generate C# Class** trong Inspector của asset, đặt tên class (ví dụ `PlayerControls`) rồi bấm **Apply** — Unity sẽ tự sinh ra một class C# wrapper để gọi action từ code mà không cần tra chuỗi tên action thủ công, có IntelliSense hỗ trợ và tránh lỗi gõ sai chuỗi.

### 4.2. Đọc input trong code

Có 2 cách phổ biến để đọc giá trị từ Action trong script. Khóa học dùng cách **polling** (đọc giá trị mỗi frame trong `Update`) vì gần với tư duy của `Input.GetAxis` mà học viên vừa làm quen, dễ chuyển tiếp hơn so với cách event-driven (đăng ký callback `performed`/`canceled`) sẽ học ở buổi sau:

```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class PlayerMovement : MonoBehaviour
{z
    public float moveSpeed = 5f;
	
	// Class này là class chúng ta đã generate ra từ InputActionMaps
    private PlayerControls controls;

    private void Awake()
    {
        controls = new PlayerControls();
    }

    private void OnEnable()
    {
        controls.Player.Enable();
    }

    private void OnDisable()
    {
        controls.Player.Disable();
    }

    private void Update()
    {
	    
        Vector2 input = controls.Player.Move.ReadValue<Vector2>();

        Vector3 movement = new Vector3(input.x, 0, input.y);
        transform.Translate(movement * moveSpeed * Time.deltaTime);
    }
}
```

Một vài điểm cần chú ý:
- `controls.Player.Enable()` / `Disable()` trong `OnEnable`/`OnDisable` là bước **bắt buộc** — Action Map mặc định ở trạng thái disabled, nếu quên enable thì `ReadValue` luôn trả về giá trị mặc định (`Vector2.zero`) dù phím có được nhấn. Đặt enable/disable ở đây cũng tận dụng đúng kiến thức về vòng đời script vừa học ở mục 2.
- `ReadValue<Vector2>()` đọc giá trị hiện tại của action — tương tự cách `Input.GetAxis` trả về giá trị liên tục mỗi frame, không cần đăng ký callback.
- Class `PlayerControls` được generate tự động nên **không tự sửa tay** file này — mọi thay đổi binding nên làm lại từ Input Actions Editor rồi bấm Apply để generate lại.

**Cách thay thế (không generate C# Class):** nếu chưa muốn dùng generated class, có thể kéo asset `.inputactions` vào một field kiểu `InputActionAsset` trên Inspector rồi lấy action bằng `asset.FindActionMap("Player").FindAction("Move")` — cách này linh hoạt hơn cho người mới làm quen với Inspector, nhưng dễ gõ sai tên action (chỉ phát hiện lỗi lúc runtime, không có IntelliSense), nên khóa học ưu tiên dùng generated class.

### 4.3. Time.deltaTime — vì sao phải nhân vào tốc độ di chuyển

`Time.deltaTime` đại diện cho khoảng thời gian đã trôi qua kể từ frame trước. Nhân biến tốc độ di chuyển với `Time.deltaTime` đảm bảo object di chuyển với tốc độ ổn định mỗi giây, bất kể framerate máy đang chạy cao hay thấp — đây là lỗi rất phổ biến ở người mới khi quên nhân deltaTime, khiến object di chuyển nhanh/chậm khác nhau tùy theo máy. Lưu ý này áp dụng như nhau dù dùng Input Manager cũ hay New Input System, vì deltaTime không liên quan đến cách đọc input.

Đoạn code ở mục 4.2 dùng `transform.Translate` để di chuyển object theo trục X và Z (phù hợp game top-down/3D); với game 2D, học viên thay bằng trục X và Y tùy theo setup camera.

## Tài liệu tham khảo (Unity Docs chính thức — bản 6000.3 LTS)

|Chủ đề|Link|
|---|---|
|Creating and Using Scripts|https://docs.unity3d.com/540/Documentation/Manual/CreatingAndUsingScripts.html|
|MonoBehaviour (Manual)|https://docs.unity3d.com/6000.3/Documentation/Manual/class-MonoBehaviour.html|
|MonoBehaviour (Scripting API)|https://docs.unity3d.com/6000.3/Documentation/ScriptReference/MonoBehaviour.html|
|Order of execution for event functions|https://docs.unity3d.com/6000.3/Documentation/Manual/execution-order.html|
|MonoBehaviour.Awake|https://docs.unity3d.com/6000.3/Documentation/ScriptReference/MonoBehaviour.Awake.html|
|MonoBehaviour.OnEnable|https://docs.unity3d.com/6000.3/Documentation/ScriptReference/MonoBehaviour.OnEnable.html|
|MonoBehaviour.Start|https://docs.unity3d.com/6000.3/Documentation/ScriptReference/MonoBehaviour.Start.html|
|MonoBehaviour.FixedUpdate|https://docs.unity3d.com/6000.3/Documentation/ScriptReference/MonoBehaviour.FixedUpdate.html|
|MonoBehaviour.Update|https://docs.unity3d.com/6000.3/Documentation/ScriptReference/MonoBehaviour.Update.html|
|MonoBehaviour.LateUpdate|https://docs.unity3d.com/6000.3/Documentation/ScriptReference/MonoBehaviour.LateUpdate.html|
|MonoBehaviour.OnDisable|https://docs.unity3d.com/6000.3/Documentation/ScriptReference/MonoBehaviour.OnDisable.html|
|MonoBehaviour.OnDestroy|https://docs.unity3d.com/6000.3/Documentation/ScriptReference/MonoBehaviour.OnDestroy.html|
|Script Execution Order reference|https://docs.unity3d.com/6000.3/Documentation/Manual/class-MonoManager.html|
|SerializeField|https://docs.unity3d.com/6000.3/Documentation/ScriptReference/SerializeField.html|
|Serialization rules|https://docs.unity3d.com/6000.3/Documentation/Manual/script-serialization-rules.html|
|Upgrade Guide to Unity 6.3 (SerializeField change)|https://docs.unity3d.com/6000.3/Documentation/Manual/UpgradeGuideUnity63.html|
|Input System package — Manual|https://docs.unity3d.com/Packages/com.unity.inputsystem@1.11/manual/index.html|
|Input System — Quick start guide|https://docs.unity3d.com/Packages/com.unity.inputsystem@1.11/manual/QuickStartGuide.html|
|Input System — Touch support|https://docs.unity3d.com/Packages/com.unity.inputsystem@1.11/manual/Touch.html|
|Time.deltaTime|https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Time-deltaTime.html|
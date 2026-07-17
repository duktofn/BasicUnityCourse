**Lưu ý:** Buổi này giả định học viên đã quen thao tác cơ bản trên Editor (Session 1) và đã viết được MonoBehaviour Script, hiểu vòng đời script cùng cách dùng `AddComponent<T>()` / `GetComponent<T>()` / `Object.Destroy()` ở mức tên gọi (Session 2). Buổi hôm nay là lúc `Object.Destroy()` được dùng thật trong code, và bổ sung thêm người anh em của nó: `Object.Instantiate()`.

---

## 1. Prefab là gì và vì sao cần dùng

Tham khảo: [Introduction to prefabs](https://docs.unity3d.com/6000.3/Documentation/Manual/prefabs-introduction.html)

**Vấn đề:** giả sử game có 20 viên gạch giống hệt nhau hoặc 10 con Enemy cùng loại rải khắp scene. Nếu dựng thủ công từng cái (kéo model, gắn Collider, gắn script, chỉnh giá trị...) thì khi cần sửa một thuộc tính chung — ví dụ tăng tốc độ di chuyển của Enemy — sẽ phải sửa tay lại cả 10 object. Với Prefab, sửa đúng một chỗ duy nhất (prefab asset) → toàn bộ instance của nó trong **mọi scene** của project tự động cập nhật theo.

**Prefab** là một loại asset cho phép lưu lại một GameObject hoàn chỉnh — bao gồm toàn bộ component, giá trị thuộc tính, và cả các GameObject con của nó — dưới dạng một cái gì đó tái sử dụng được. Từ prefab asset này, ta tạo ra được nhiều **prefab instance** trong scene, mỗi instance là một bản sao độc lập nhưng vẫn giữ liên kết ngược về prefab gốc.


> **Tips:** Cứ nghĩ Prefab như "class", còn Prefab Instance như "object" được tạo ra từ class đó — sửa định nghĩa class thì mọi object đều đổi theo, trừ khi object đó tự override lại giá trị riêng.

---

## 2. Tạo Prefab Asset

Tham khảo: [Create prefabs](https://docs.unity3d.com/6000.3/Documentation/Manual/CreatingPrefabs.html)

Các bước tạo:

1. Tạo một GameObject trong scene, cấu hình đầy đủ component, giá trị Transform, material... như bình thường.
2. Kéo GameObject đó từ **Hierarchy** thả vào một folder trong **Project window**.
3. Unity tự tạo file `.prefab` tại đó, và GameObject gốc trong Hierarchy **tự động trở thành một instance** của prefab vừa tạo — không cần thao tác gì thêm.

**Nhận diện**: một GameObject trong Hierarchy đã là prefab instance sẽ có icon và tên hiển thị màu xanh dương, khác với GameObject thường.

> **Tips:** Có thể chọn nhiều GameObject cùng lúc rồi kéo thả — Unity sẽ tạo prefab riêng cho từng cái, không gộp chung.

⚠️ **WARNING:** Nếu kéo một GameObject _vốn đã là prefab instance_ vào Project window đè lên một prefab khác, Unity sẽ hỏi xác nhận — chọn nhầm có thể ghi đè prefab asset đang dùng ở chỗ khác trong project. Đọc kỹ và xác nhận trước khi bấm.

---

## 3. Prefab Instance, Override và Apply/Revert

Tham khảo: [Prefab instance Inspector reference](https://docs.unity3d.com/6000.3/Documentation/Manual/prefab-instance-inspector-reference.html)

Một prefab instance trong scene không bắt buộc phải giống 100% prefab gốc. Có thể chỉnh riêng một vài giá trị trên **một** instance cụ thể — ví dụ đổi màu, đổi vị trí, tắt một component — mà không ảnh hưởng tới các instance khác hay tới prefab asset gốc. Hành động chỉnh riêng này gọi là **override**.

_Demo trực tiếp:_ tạo 3 instance của prefab "Enemy" trong scene. Chọn 1 instance, đổi Scale trong Inspector → tên field Scale đổi sang **in đậm/màu xanh** báo hiệu đây đang là override. Mục **Overrides** trên đầu Inspector của prefab instance liệt kê toàn bộ những gì đã bị override, với hai lựa chọn:

- **Apply** — đẩy thay đổi từ instance này ngược lên prefab asset gốc → tất cả instance khác (kể cả những instance chưa từng bị đổi) cũng nhận thay đổi theo ngay lập tức.
- **Revert** — huỷ override trên instance này, trả giá trị về đúng như prefab asset gốc.

> **Why:** Cơ chế Apply/Revert giúp tách biệt rõ hai loại thay đổi: "sửa cho toàn bộ hệ thống" (Apply lên prefab gốc) và "chỉnh riêng cho một trường hợp cá biệt" (giữ override trên instance, không Apply). Không có cơ chế này, mọi thay đổi sẽ lẫn lộn, không biết cái nào là chủ ý riêng và cái nào cần đồng bộ.

---

## 4. Prefab Variant

Tham khảo: [Create variations of prefabs](https://docs.unity3d.com/6000.3/Documentation/Manual/PrefabVariants.html)

Nếu số lượng override trên một instance nhiều và mang tính lặp lại (ví dụ cần hẳn một _loại_ Enemy khác hẳn về tốc độ, máu, màu sắc — không chỉ một instance lẻ), cách làm đúng là tạo **Prefab Variant** thay vì override tay từng instance.

Prefab Variant là một prefab **kế thừa** từ một prefab base (hoặc từ một variant khác) — nhận toàn bộ thay đổi từ base khi base được cập nhật, ngoại trừ những thuộc tính mà variant đã tự override riêng.

**Use case phổ biến nhất:** nhiều loại Enemy dùng chung khung sườn — cùng script, cùng Collider, cùng animation — nhưng khác tốc độ di chuyển, máu tối đa, hoặc model hiển thị. Base Prefab `Enemy` giữ toàn bộ logic chung; các Variant như `Enemy_Fast`, `Enemy_Tank` chỉ override đúng vài giá trị khác biệt.

**Cách tạo:** chuột phải vào prefab trong Project window → `Create` → `Prefab Variant`.

Icon nhận diện: prefab variant có icon khối màu xanh với một góc bị gạch chéo, khác với icon prefab gốc — dễ phân biệt trong Project window khi danh sách asset nhiều.

---

## 5. Instantiate — tạo GameObject từ Prefab lúc runtime

Tham khảo: [Introduction to instantiating prefabs](https://docs.unity3d.com/6000.3/Documentation/Manual/instantiating-prefabs-intro.html), [Object.Instantiate scripting reference](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Object.Instantiate.html)

Đây chính là cách tạo GameObject số 3 đã nhắc tên ở Session 1 ("qua script bằng class `GameObject`") — giờ mình dùng thật.

So với tự dựng GameObject từ code bằng `new GameObject()` rồi gọi `AddComponent<T>()` từng cái một, Instantiate từ một prefab có sẵn chỉ tốn **một dòng code** — vì toàn bộ component, giá trị, và GameObject con đã được định nghĩa sẵn trong prefab. Một lợi ích khác: đổi loại object được spawn mà **không cần sửa code** — chỉ cần kéo prefab khác vào field Inspector.

**Cú pháp cơ bản:**

**Instantiate**

```csharp
Instantiate(enemyPrefab);
```

Nếu chỉ Instantiate một tham số là một prefab thì nó sẽ spawn tại vị trí của đang được set trong prefabs (theo World Space).
Nếu Instantiate một Game Object đang tồn tại trong scene thì nó sẽ được copy và sinh ra đúng tại vị trí của Game Object hiện thời.

**Instantiate kèm vị trí và rotation:**

```csharp
Instantiate(enemyPrefab, transform.position, Quaternion.identity);
```

`Quaternion.identity` nghĩa là không xoay (rotation mặc định). Nếu không truyền position/rotation, object mới sẽ dùng đúng position/rotation đang lưu sẵn trong prefab asset.

**Instantiate kèm parent:**

```csharp
Instantiate(enemyPrefab, transform);
```

Tham số cuối chỉ định object mới sinh ra sẽ là **con** của transform truyền vào, và transform của object con lúc này sẽ theo Local Space với gốc tọa độ là vị trí của Object cha của nó.
Nếu không truyền theo vị trí xuất hiện thì nó sẽ mặc định spawn theo vị trí prefabs trong Local Space của Object cha.
Nếu truyền cùng vị trí và góc quay thì nó sẽ được spawn với vị trí và góc quay trong Local Space của Object cha.


---

### Ví dụ thực hành: Spawner theo chu kỳ

Ghép `Instantiate` với vòng đời script đã học ở Session 2 — dùng `Update()` cùng bộ đếm thời gian để spawn Enemy đều đặn:

```csharp
using UnityEngine;

public class EnemySpawner : MonoBehaviour
{
    [SerializeField] private GameObject enemyPrefab;
    [SerializeField] private float spawnInterval = 2f;

    private float _timer;

    private void Update()
    {
        _timer += Time.deltaTime;

        if (_timer >= spawnInterval)
        {
            _timer = 0f;
            Instantiate(enemyPrefab, transform.position, Quaternion.identity);
        }
    }
}
```

Vì sao dùng `Time.deltaTime` cộng dồn vào `_timer` thay vì đếm số frame: đảm bảo chu kỳ spawn ổn định theo giây thực, không phụ thuộc framerate máy đang chạy — đúng lý do đã giải thích ở Session 2.

---

## 6. Destroy — huỷ GameObject lúc runtime

Tham khảo: [Object.Destroy scripting reference](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Object.Destroy.html)

`Destroy()` đã được nhắc tên ở Session 1, giờ dùng thật:

```csharp
Destroy(gameObject);              // huỷ chính GameObject đang gắn script này
Destroy(GetComponent<Collider>()); // chỉ huỷ component Collider, GameObject vẫn tồn tại
Destroy(gameObject, 5f);          // huỷ sau 5 giây kể từ lúc dòng này chạy
```

Vài điểm quan trọng cần phân biệt rõ:

- Nếu object truyền vào là **component** → chỉ component đó bị gỡ, GameObject và các component khác vẫn còn nguyên.
- Nếu object truyền vào là **GameObject** → toàn bộ GameObject, mọi component, và mọi GameObject con của nó bị huỷ cùng lúc.
- Việc huỷ thật sự luôn được **hoãn tới cuối Update loop hiện tại**, nhưng chắc chắn xảy ra trước khi render frame đó — nên không thấy object "biến mất giữa chừng" một cách bất thường.
- Với tham số delay `t`, đồng hồ đếm bắt đầu tính **ngay tại thời điểm gọi** `Destroy(obj, t)`, không phải từ lúc script bị disable hay huỷ sau đó.
- `Destroy` an toàn khi gọi trên object đã bị huỷ hoặc đang null — không lỗi.
- Nếu dùng delay, thời gian đó bị ảnh hưởng bởi `Time.timeScale` — game đang pause (`Time.timeScale = 0`) thì việc huỷ cũng bị hoãn theo tới khi thời gian chạy lại.

⚠️ **WARNING — lỗi rất hay gặp:** viết `Destroy(this)` bên trong một MonoBehaviour tưởng sẽ huỷ cả GameObject, nhưng thực chất `this` đang trỏ tới **script instance**, không phải GameObject — kết quả chỉ script đó bị gỡ khỏi GameObject, GameObject vẫn còn sống với các component khác. Muốn huỷ cả GameObject, phải gọi rõ `Destroy(gameObject)`.

**DestroyImmediate — chỉ dùng trong Edit mode, không dùng lúc chạy game:**

`DestroyImmediate()` huỷ object ngay lập tức thay vì hoãn tới cuối frame, nhưng tài liệu Unity khuyến cáo hàm này chỉ dành cho **script chạy trong Editor** (editor tooling), không phải runtime code. Gọi `DestroyImmediate` trong lúc physics callback, animation event, hoặc rendering callback đang chạy sẽ gây lỗi. Trong code gameplay bình thường, luôn dùng `Destroy()`.

---

## 7. Kết hợp Instantiate và Destroy: Enemy tự huỷ sau thời gian sống

Một pattern thực tế hay gặp: object được spawn ra, sống một khoảng thời gian cố định rồi tự huỷ (ví dụ hiệu ứng nổ, hoặc đạn bay quá xa chưa va chạm gì vẫn cần biến mất để không tồn tại mãi):

```csharp
using UnityEngine;

public class SelfDestruct : MonoBehaviour
{
    [SerializeField] private float lifeTime = 3f;

    private void Start()
    {
        Destroy(gameObject, lifeTime);
    }
}
```

Gắn script này vào prefab Enemy (hoặc prefab đạn) — mỗi instance sinh ra từ `Instantiate` sẽ tự chạy `Start()` riêng của chính nó, tự đếm `lifeTime` riêng, và tự huỷ mà `EnemySpawner` không cần biết gì thêm. Đây là ví dụ rõ cho thấy lợi ích của việc để logic nằm đúng trên object liên quan, thay vì dồn hết vào một script quản lý trung tâm.

---

## 8. Vì sao không nên Instantiate/Destroy liên tục 

`Instantiate` và `Destroy` đều tốn chi phí hiệu năng: `Instantiate` phải cấp phát bộ nhớ mới và khởi tạo lại toàn bộ component; `Destroy` để lại rác cần bộ nhớ dọn (garbage collection). Giả sử, khi làm một game bắn súng bắn hàng chục viên đạn mỗi giây, hoặc spawner tạo/huỷ liên tục, việc gọi `Instantiate`/`Destroy` với tần suất cao có thể gây drop fps (tụt khung hình). 

Giải pháp phổ biến nhất cho vấn đề này là **Object Pooling** — tái sử dụng lại một nhóm object có sẵn (bật/tắt qua `SetActive`) thay vì tạo mới rồi huỷ liên tục. Session 2 đã nhắc tên pattern này khi nói về `OnEnable`/`OnDisable`. Đây là kỹ thuật thuộc phần nâng cao, khoá học cơ bản này chưa đi sâu — nhưng nên biết tên để tự tìm hiểu thêm khi project thật sự cần tối ưu. 

Nếu có ai thật sự cần biết thì có thể tham khảo series dưới đây (hoàn toàn bằng Tiếng Việt):
1. [Object Pooling 1 - Chuẩn bị](https://youtu.be/mesVsMhRyI4?si=jHHKkaXIO11-Wfdd)
2. [Object Pooling 2 - Lý thuyết](https://youtu.be/guD1p4U4ca0?si=Iy8hKzWha80p__yH)
3. [Object Pooling 3 - Xây dựng](https://youtu.be/ks4MtS1cQrs?si=JEREkFJ-OvC4bVww)
4. [Object Pooling 4 - Áp dụng](https://youtu.be/F7pXumz9pzY?si=RWzeh-NJO56Fi8EO)


---

## Tài liệu tham khảo (Unity Docs chính thức — bản 6000.3 LTS)

| Chủ đề                                        | Link                                                                                          |
| --------------------------------------------- | --------------------------------------------------------------------------------------------- |
| Introduction to prefabs                       | https://docs.unity3d.com/6000.3/Documentation/Manual/prefabs-introduction.html                |
| Create prefabs                                | https://docs.unity3d.com/6000.3/Documentation/Manual/CreatingPrefabs.html                     |
| Prefab instance Inspector reference           | https://docs.unity3d.com/6000.3/Documentation/Manual/prefab-instance-inspector-reference.html |
| Create variations of prefabs (Prefab Variant) | https://docs.unity3d.com/6000.3/Documentation/Manual/PrefabVariants.html                      |
| Introduction to instantiating prefabs         | https://docs.unity3d.com/6000.3/Documentation/Manual/instantiating-prefabs-intro.html         |
| Instantiating prefabs at runtime              | https://docs.unity3d.com/6000.3/Documentation/Manual/instantiating-prefabs.html               |
| Object.Instantiate (Scripting API)            | https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Object.Instantiate.html         |
| Object.Destroy (Scripting API)                | https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Object.Destroy.html             |
| Object.DestroyImmediate (Scripting API)       | https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Object.DestroyImmediate.html    |
| MonoBehaviour.OnDestroy (Scripting API)       | https://docs.unity3d.com/6000.3/Documentation/ScriptReference/MonoBehaviour.OnDestroy.html    |
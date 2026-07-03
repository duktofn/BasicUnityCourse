 
**Lưu ý:** Tất cả mọi thứ liên quan đến việc scripting sẽ được đề cập chi tiết hơn trong Session 2. Các Methods nêu ra trong các nội dung bên dưới có thể tham khảo trước về tính năng hoặc cách hoạt động. Nhưng chưa quá cần thiết trong Session này.

---

## 1. Tour Unity Editor

Tham khảo: [Unity Editor interface](https://docs.unity3d.com/6000.3/Documentation/Manual/unity-editor.html) — trang tổng quan chính thức về các công cụ và quy trình làm việc cốt lõi trong Editor.

**Lưu ý:** Tất cả mọi thứ liên quan đến việc scripting sẽ được đề cập chi tiết hơn trong Session 2. Các Methods nêu ra trong các nội dung bên dưới có thể tham khảo trước về tính năng hoặc cách hoạt động. Nhưng chưa quá cần thiết trong Session này.

Layout mặc định của Unity gồm các vùng chính sau:

![](Images/Pasted%20image%2020260630154038.png)

### Scene view & Game view

Đây là khu vực trung tâm của layout mặc định. **Scene view** là nơi bạn dựng và quan sát thế giới game từ nhiều góc độ; **Game view** nằm cùng khu vực, dùng để xem trước sản phẩm sẽ hiển thị cho người chơi cuối cùng khi nhấn nút Play (theo hướng dẫn từ Unity Learn — [Explore the Unity Editor](https://learn.unity.com/tutorial/explore-the-unity-editor-1)).

Lưu ý quan trọng cho học viên: khi đang ở Play mode, mọi thay đổi thực hiện trong Scene sẽ **không được lưu** sau khi thoát Play mode — cần tắt Play mode trước khi chỉnh sửa thật.

### Hierarchy window

Liệt kê toàn bộ GameObject có trong scene **đang mở**. Theo tài liệu chính thức về [Hierarchy window reference](https://docs.unity3d.com/6000.3/Documentation/Manual/hierarchy-reference.html), cửa sổ này hiển thị mọi GameObject trong scene, bao gồm cả model, camera, và prefab. Khi thêm hoặc xóa object trong Scene view, danh sách trong Hierarchy cũng tự động cập nhật.

Một chi tiết nhỏ nhưng hữu ích: nếu scene có thay đổi chưa lưu, sẽ xuất hiện dấu `*` ngay cạnh tên scene ở đầu Hierarchy window.

### Project window

Khác với Hierarchy (giới hạn theo scene), **Project window** chứa toàn bộ asset của cả project — hoạt động giống một trình quản lý file, được tổ chức theo folder ([Project window reference](https://docs.unity3d.com/6000.4/Documentation/Manual/ProjectView.html)). Bạn có thể kéo asset trực tiếp từ đây vào Scene view để thêm vào scene.

### Inspector window

Theo [Inspector window reference](https://docs.unity3d.com/6000.3/Documentation/Manual/UsingTheInspector.html), khi chọn một GameObject (ở Hierarchy hoặc Scene view), Inspector sẽ hiển thị thuộc tính của toàn bộ component và material gắn trên object đó, đồng thời cho phép chỉnh sửa giá trị và sắp xếp lại thứ tự component. Tài liệu [Manage the Inspector window](https://docs.unity3d.com/6000.3/Documentation/Manual/InspectorOptions.html) cũng có nói về Debug mode — chế độ hiển thị các property private của component, hữu ích khi cần debug sâu hơn (chưa cần dùng ở buổi này).

### Toolbar điều hướng & Transform tool

Thanh công cụ scene navigation cho phép Move, Rotate, Scale GameObject đang chọn. Theo Unity Learn, các phím tắt của các tool này tương ứng với hàng phím Q/W/E/R/T/Y ở góc trái bàn phím — giúp chuyển nhanh giữa các tool mà không cần rời chuột khỏi Scene view.

**Notes:**
>Nếu chưa hài lòng với layout làm việc hiện tại, có thể tự custom cho thuận tiện với bản thân.
>Xem thêm: [Customize your workspace layout](https://docs.unity3d.com/Manual/CustomizingYourWorkspace.html).

---

## 2. GameObjects

Tham khảo chính: [Introduction to GameObjects](https://docs.unity3d.com/6000.3/Documentation/Manual/GameObjects.html)

GameObject được tài liệu Unity mô tả là khái niệm quan trọng nhất trong Editor — mọi thứ tồn tại trong game đều là một GameObject, từ nhân vật, vật phẩm thu thập, cho đến đèn, camera, và hiệu ứng đặc biệt. Tuy nhiên, bản thân một GameObject không tự làm được gì cả — nó chỉ là một **container rỗng**; phải gán component vào thì nó mới có hình dạng và hành vi cụ thể.

Trang [GameObjects overview](https://docs.unity3d.com/6000.3/Documentation/Manual/working-with-gameobjects.html) cũng nhấn mạnh GameObject là khối xây dựng cơ bản (essential building block) của bất kỳ project Unity nào — chúng là các container đơn giản mà bạn gắn thêm component vào để biến chúng thành các thành phần cụ thể của ứng dụng.

### Chi tiết quan trọng: Transform luôn tồn tại

Theo tài liệu, mọi GameObject đều luôn có sẵn một **Transform component** (đại diện cho vị trí và hướng xoay, và scale của GameObject), và component này **không thể bị gỡ bỏ**. 

### Cách tạo GameObject

- Qua menu: Chuột phải trong tab Hierarchy: `3D Object` để dùng các 3D object dựng sẵn (Cube, Sphere, Capsule, Plane...) hoặc chọn `Create Empty` để tạo GameObject trống. Xem thêm [Primitive and placeholder objects](https://docs.unity3d.com/Manual/PrimitiveObjects.html) — trang mô tả các loại 3D Object sẵn có và cách thêm chúng vào scene. 
- Nếu làm game 2D, chúng ta cũng làm tương tự thông qua: chuột phải trong Hierarchy và chọn `2D Object` tham khảo thêm [Types of 2D primitive GameObjects](https://docs.unity3d.com/6000.3/Documentation/Manual/2DPrimitiveObjects.html).
- Qua script bằng class `GameObject` trong code — phần này sẽ học sâu ở buổi 2, nhưng học viên có thể tham khảo trước [GameObject scripting reference](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/GameObject.html) nếu tò mò.

---

## 3. Components

Tham khảo: [The GameObject class](https://docs.unity3d.com/6000.3/Documentation/Manual/class-GameObject.html)

Một ví dụ cụ thể được tài liệu Unity đưa ra: một khối lập phương đặc (solid cube) cần có **Mesh Filter** và **Mesh Renderer** để vẽ được bề mặt của nó, và cần thêm **Box Collider** để biểu diễn khối lượng vật lý — ba component này cùng gắn trên một GameObject nhưng đảm nhiệm ba vai trò hoàn toàn khác nhau (hiển thị hình học, render, và vật lý).

Cách thêm/xóa component:
- Qua menu `Component` trong Inspector (thủ công).
- Qua script bằng `AddComponent<T>()` để thêm tại runtime, và `Object.Destroy()` trên component đó để gỡ bỏ. Cách lấy reference đến component khác gắn trên cùng GameObject dùng `GetComponent<T>()`.

Học viên chưa cần viết code ở buổi này, nhưng nên biết trước những API name này vì sẽ gặp lại ngay ở buổi 2.

---

## 4. Transform — component đặc biệt

Tham khảo: [Transform scripting reference](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Transform.html)

Transform lưu trữ và cho phép thao tác Position, Rotation, Scale của object. Một điểm quan trọng: Transform có thể có **parent**, cho phép áp dụng Position/Rotation/Scale theo dạng phân cấp (hierarchy) — tức là di chuyển object cha sẽ kéo theo toàn bộ object con di chuyển cùng. 

Và các Properties Position, Rotation, Scale của GameObject con được hiển thị trên Inspector là Local được lấy mốc là vị trí của GameObject cha. Nếu GameObject không có cha thì các properties được hiển thị sẽ là của World.

Hai cách chỉnh giá trị Transform:
- Nhập số trực tiếp vào field trong Inspector (chính xác cao).
- Kéo-thả trong Scene view (trực quan, nhanh).

Để di chuyển/xoay/scale theo bước nhảy cố định (snap theo lưới), tham khảo [Move, rotate, and scale in increments](https://docs.unity3d.com/6000.3/Documentation/Manual/SnapIncrements.html) — giữ phím Control (macOS: Command) trong lúc kéo Gizmo để snap theo giá trị tăng cố định, hữu ích khi cần sắp xếp object thẳng hàng chính xác.

---

## 5. Scene là gì

Tham khảo: [Introduction to scenes](https://docs.unity3d.com/6000.3/Documentation/Manual/CreatingScenes.html)

Scene là asset chứa toàn bộ hoặc một phần nội dung của game/ứng dụng. Một game đơn giản có thể chỉ cần một scene duy nhất, trong khi game phức tạp hơn thường tách mỗi màn chơi thành một scene riêng. Một project Unity bắt buộc phải có ít nhất một scene.

---

## 6. Git/GitHub setup tối thiểu (15–20 phút cuối buổi or đẩy sang buổi scripting)

Không đi sâu lý thuyết Git, chỉ làm theo các bước thao tác:

1. Tạo repository mới trên GitHub.
2. Chạy `git init` trong thư mục gốc của project Unity.
3. Thêm file `.gitignore` chuẩn cho Unity — loại trừ các thư mục Unity tự sinh lại được (không cần và không nên đưa lên Git):
    - `Library/`
    - `Temp/`
    - `Obj/`
    - `Build/`
    - `Logs/`
    - `UserSettings/`
    Có thể lấy template chuẩn tại [GitHub's Unity .gitignore template](https://github.com/github/gitignore/blob/main/Unity.gitignore) để đảm bảo đầy đủ và đúng chuẩn cộng đồng.
4. `git add . && git commit -m "init project"`.
5. Push lên remote: `git remote add origin <repo-url>` rồi `git push -u origin main`.

**Giải thích ngắn gọn cho học viên:** thư mục `Library/` chứa cache do Unity tự tạo lại mỗi lần mở project — rất nặng (có thể vài GB) và gây xung đột (conflict) vô nghĩa nếu đưa lên Git, nên luôn phải loại trừ.

---

## Tài liệu tham khảo (Unity Docs chính thức — bản 6000.3 LTS)

|Chủ đề|Link|
|---|---|
|Unity Editor interface|https://docs.unity3d.com/6000.3/Documentation/Manual/unity-editor.html|
|Introduction to GameObjects|https://docs.unity3d.com/6000.3/Documentation/Manual/GameObjects.html|
|Working with GameObjects|https://docs.unity3d.com/6000.3/Documentation/Manual/working-with-gameobjects.html|
|The GameObject class (Component, AddComponent, GetComponent)|https://docs.unity3d.com/6000.3/Documentation/Manual/class-GameObject.html|
|GameObject scripting reference|https://docs.unity3d.com/6000.3/Documentation/ScriptReference/GameObject.html|
|Transform scripting reference|https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Transform.html|
|Move, rotate, and scale in increments|https://docs.unity3d.com/6000.3/Documentation/Manual/SnapIncrements.html|
|Hierarchy window reference|https://docs.unity3d.com/6000.3/Documentation/Manual/hierarchy-reference.html|
|Project window reference|https://docs.unity3d.com/6000.4/Documentation/Manual/ProjectView.html|
|Inspector window reference|https://docs.unity3d.com/6000.3/Documentation/Manual/UsingTheInspector.html|
|Manage the Inspector window|https://docs.unity3d.com/6000.3/Documentation/Manual/InspectorOptions.html|
|Introduction to scenes|https://docs.unity3d.com/6000.3/Documentation/Manual/CreatingScenes.html|
|Primitive and placeholder objects|https://docs.unity3d.com/Manual/PrimitiveObjects.html|
|Types of 2D primitive GameObjects|https://docs.unity3d.com/6000.3/Documentation/Manual/2DPrimitiveObjects.html|
|Customize your workspace layout|https://docs.unity3d.com/Manual/CustomizingYourWorkspace.html|

_Lưu ý: trang Project window reference hiện chỉ có bản 6000.4 được index đầy đủ tại thời điểm soạn tài liệu này; nội dung không thay đổi đáng kể so với 6000.3 nên vẫn dùng được làm tham khảo chính xác._
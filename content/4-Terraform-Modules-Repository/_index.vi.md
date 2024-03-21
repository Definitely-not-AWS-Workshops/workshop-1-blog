---
title : "Tạo Admin Group và Admin User"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 3. </b> "
---


## Tạo Admin Group

1. **Đăng nhập vào Bảng điều khiển** ở trang [AWS Web Service page](https://aws.amazon.com/)

2. Nhấn vào tên tài khoản ở góc trên bên phải và chọn **My Security Credentials**

   ![AWS IAM](/images/01/0001.png?featherlight=false&width=90pc)

   > **Lưu ý:** Trong trường hợp không thấy menu **My Security Credentials**, bạn có thể click vào biểu tượng tìm kiếm và điền **IAM**. Sau đó click vào dịch vụ IAM để truy cập vào giao diện quản lý dịch vụ IAM.

   ![AWS IAM](/images/01/0002.png?featherlight=false&width=90pc)

3. Ở thanh bên trái, chọn **User Groups** sau đó chọn **Create Group**

   ![AWS IAM](/images/01/0003.png?featherlight=false&width=90pc)

4. Dưới mục **Name the group**, nhập tên Group (Ví dụ: *AdminGroup*) và cuộn chuột xuống dưới

   ![AWS IAM](/images/01/0004.png?featherlight=false&width=90pc)

5. Ở phần **Attach permissions policies**, gõ **AdministratorAccesss** vào thanh tìm kiếm và nhấn chọn nó. Cuối cùng, chọn **Create Group**.

   ![AWS IAM](/images/01/0005.png?featherlight=false&width=90pc)

6. Hoàn thành tạo admin group.

   ![AWS IAM](/images/01/0006.png?featherlight=false&width=90pc)

## Tạo Admin User

1. Ở thanh bên trái, chọn **Users** sau đó chọn **Add User**

   ![AWS IAM](/images/02/0001.png?featherlight=false&width=90pc)

2. Nhập tên User (Ví dụ: *AdminUser*).
    + Click **AWS Management Console access**.
    + Click **Programmatic Access**.
    + Click **Custom password** rồi gõ một password tùy ý của bạn (lưu ý: bạn phải ghi nhớ mật khẩu này cho những lần đăng nhập trong tương lai).
    + Bỏ chọn mục **User must create a new password...**.
    + Click **Next:Permissions**.

   ![AWS IAM](/images/02/0002.png?featherlight=false&width=90pc)

   > **Lưu ý:** Bằng cách chọn **AWS Management Console access**, bạn vừa cho phép IAM User được truy cập vào AWS thông qua bảng điều khiển AWS trên web. Việc bỏ mục **User must create a new password...** cho phép người dùng khi lần đầu đăng nhập vào IAM User đó không cần phải tạo mật khẩu mới.

3. Click tab **Add user to group** và click **AdminGroup** mà chúng ta tạo trước đó.

   ![AWS IAM](/images/02/0003.png?featherlight=false&width=90pc)

4. Click **Next:Tags**
    - Tags (thẻ) là một tùy chọn không bắt buộc để tổ chức, theo dõi, hoặc điều khiển truy cập của user, thế nên bạn có thể thêm tags hoặc không.

5. Click **Next:Review**.

   ![AWS IAM](/images/02/0004.png?featherlight=false&width=90pc)

6. Kiểm tra thông tin và chọn **Create user**

   ![AWS IAM](/images/02/0005.png?featherlight=false&width=90pc)

7. Hoàn thành tạo user. Có thể download.csv để lưu trữ Access key.

   ![AWS IAM](/images/02/0006.png?featherlight=false&width=90pc)

8. Tạo admin user thành công.

   ![AWS IAM](/images/02/0007.png?featherlight=false&width=90pc)

9. Kiểm tra thông tin chi tiết user.

   ![AWS IAM](/images/02/0008.png?featherlight=false&width=90pc)

   > **Lưu ý:** Sau khi tạo user, bạn sẽ thấy hiện lên hộp thoại download thông tin access key và secret key. Đây là thông tin dùng để thực hiện **Programmatic access** tới các tài nguyên của AWS thông qua **AWS CLI** và **AWS SDK**. Tạm thời chúng ta sẽ chưa sử dụng tới.

# Đăng nhập vào AdminUser

1. Trở về dịch vụ IAM, và chọn **Users** ở thanh bên trái.
2. Nhấn vào tên của IAM User mà bạn vừa chọn.
3. Trong mục **Summary**, chọn tab **Security credentials**. Nhìn vào dòng **Summary: Console sign-in link** và copy đường link bên cạnh nó. Đây là đường link bạn dùng để đăng nhập vào IAM User.

   ![AWS IAM](/images/03/0001.png?featherlight=false&width=90pc)

4. Mở một tab ẩn danh của trình duyệt bạn đang sử dụng và paste đường link ấy vào thanh tìm kiếm.

   ![AWS IAM](/images/03/0002.png?featherlight=false&width=90pc)

   > **Lưu ý:** Việc đăng nhập bằng tab ẩn danh cho phép bạn đăng nhập vào AWS bằng IAM User mà không cần phải log out khỏi root user trong tab chính.

5. Nhập đúng tên IAM User và password mà bạn đã nhập ở phần **tạo IAM User** ở trên. Nhấn **sign in**.

   ![AWS IAM](/images/03/0003.png?featherlight=false&width=90pc)

6. Chúc mừng, bạn đã truy cập thành công vào tài khoản của bạn dưới danh nghĩa của một IAM User **AdminUser**.

   ![AWS IAM](/images/03/0004.png?featherlight=false&width=90pc)

7. Bước tiếp theo, chúng ta sẽ chuyển sang sử dụng IAM Role để nâng cao tính bảo mật hơn cho account của bạn nhé.


#### Tham khảo

# IAM User và Đăng Nhập Trong AWS

Một IAM user là một định danh được tạo trong một tài khoản AWS, có quyền được tương tác với các tài nguyên AWS. Người dùng IAM đăng nhập bằng cách sử dụng ID tài khoản hoặc tên bí danh, tên người dùng của họ và mật khẩu. Tên người dùng IAM được cấu hình bởi quản trị viên của bạn. Tên người dùng IAM có thể là tên thân thiện như "Zhang", hoặc là địa chỉ email như "zhang@example.com". Tên người dùng IAM không thể chứa khoảng trắng, nhưng có thể chứa chữ cái in hoa và thường, số, và các ký tự + = , . @ _ -.

**Mẹo**
Nếu người dùng IAM của bạn đã bật xác thực đa yếu tố (MFA), bạn phải có quyền truy cập vào thiết bị xác thực. Để biết thêm chi tiết, xem [Sử dụng thiết bị MFA với trang đăng nhập IAM của bạn](link_to_MFA_documentation).

**Để đăng nhập với tư cách người dùng IAM**
1. Mở Bảng điều khiển Quản lý AWS tại https://console.aws.amazon.com/.

2. Trang đăng nhập chính hiển thị. Chọn "IAM user", nhập ID tài khoản (12 chữ số) hoặc bí danh và chọn "Next".

**Ghi chú**
Bạn có thể không cần phải nhập ID tài khoản hoặc bí danh nếu bạn đã đăng nhập trước đó với tài khoản IAM bằng trình duyệt hiện tại hoặc nếu bạn đang sử dụng URL đăng nhập tài khoản của bạn.

3. Nhập tên người dùng IAM và mật khẩu của bạn, sau đó chọn "Sign in".

4. Nếu MFA được bật cho người dùng IAM của bạn, sau đó bạn sẽ được xác thực sử dụng nó.

Sau khi xác thực, Bảng điều khiển Quản lý AWS sẽ mở trang chủ Bảng điều khiển. 


### Tạo khóa truy cập (access key) cho tài khoản root user trên AWS

#### Quyền tối thiểu cần thiết

Để thực hiện các bước sau, bạn cần có ít nhất các quyền IAM (Identity and Access Management) sau đây:

- Bạn phải đăng nhập với tư cách là tài khoản root user của AWS, điều này không yêu cầu bất kỳ quyền IAM bổ sung nào khác. Bạn không thể thực hiện các bước này với tư cách là người dùng IAM hoặc vai trò (role).

- Sử dụng địa chỉ email và mật khẩu của tài khoản AWS để đăng nhập vào Bảng điều khiển quản lý AWS (AWS Management Console) với tư cách là tài khoản root user.

- Ở góc trên bên phải của bảng điều khiển, chọn tên hoặc số tài khoản của bạn, sau đó chọn Security Credentials (Thông tin xác thực bảo mật).

- Ở phần Access keys (Khóa truy cập), chọn Create access key (Tạo khóa truy cập). Nếu tùy chọn này không khả dụng, điều đó có nghĩa bạn đã có số lượng tối đa các khóa truy cập. Bạn phải xóa một trong các khóa truy cập hiện có trước khi tạo khóa mới. Để biết thêm thông tin, xem IAM Object Quotas trong Hướng dẫn Người dùng IAM.

- Trên trang Alternatives to root user access keys (Các phương án thay thế cho khóa truy cập tài khoản root user), xem xét các khuyến nghị về bảo mật. Để tiếp tục, chọn vào ô kiểm và sau đó chọn Create access key (Tạo khóa truy cập).

- Trên trang Retrieve access key (Trang Lấy lại khóa truy cập), Access key ID của bạn sẽ được hiển thị.

- Dưới mục Secret access key (Khóa truy cập bí mật), chọn Show (Hiển thị) và sau đó sao chép Access key ID và Secret key từ cửa sổ trình duyệt của bạn, sau đó dán chúng vào một nơi an toàn. Tùy chọn khác, bạn có thể chọn Download .csv file (Tải xuống tệp .csv) để tải xuống một tệp có tên là rootkey.csv chứa Access key ID và Secret key. Lưu tệp ở một nơi an toàn.

- Chọn Done (Hoàn thành). Khi bạn không còn cần khóa truy cập nữa, chúng tôi khuyến nghị bạn xóa nó hoặc ít nhất là xem xét vô hiệu hóa nó để không ai có thể lạm dụng.

> Lưu ý: Các bước này chỉ áp dụng cho tài khoản root user trên AWS. Đối với các người dùng IAM hoặc vai trò (role), cách tạo và quản lý khóa truy cập có thể khác nhau.

### Xóa một khóa truy cập cho người dùng gốc trên AWS

#### Quyền tối thiểu
Để thực hiện các bước sau, bạn phải có ít nhất các quyền IAM (Quản lý Quyền và Danh sách quyền) sau đây:

- Bạn phải đăng nhập dưới tư cách người dùng gốc của tài khoản AWS, điều này không yêu cầu bất kỳ quyền AWS Identity and Access Management (IAM) bổ sung nào. Bạn không thể thực hiện các bước này dưới tư cách người dùng IAM hoặc vai trò.
- Sử dụng địa chỉ email và mật khẩu của tài khoản AWS để đăng nhập vào Bảng điều khiển Quản lý AWS dưới tư cách người dùng gốc của bạn.
- Ở góc trên bên phải của bảng điều khiển, chọn tên hoặc số tài khoản của bạn, sau đó chọn **Security Credentials** (Chứng chỉ Bảo mật).
- Trong phần **Access keys** (Khóa truy cập), chọn khóa truy cập mà bạn muốn xóa, sau đó, ở mục **Actions** (Thao tác), chọn **Delete** (Xóa).

> **Lưu ý**
> Theo cách khác, bạn có thể **Deactivate** (Ngưng hoạt động) một khóa truy cập thay vì xóa nó vĩnh viễn. Điều này giúp bạn có thể tiếp tục sử dụng nó trong tương lai mà không cần thay đổi cả ID khóa lẫn khóa bí mật. Trong khi khóa bị vô hiệu hóa, mọi yêu cầu sử dụng nó trong các yêu cầu tới AWS API sẽ thất bại với lỗi "access denied" (truy cập bị từ chối).
>
> Trên hộp thoại **Delete <access key ID>** (Xóa <ID khóa truy cập>), chọn **Deactivate** (Ngưng hoạt động), nhập ID khóa truy cập để xác nhận bạn muốn xóa nó, sau đó chọn **Delete** (Xóa).

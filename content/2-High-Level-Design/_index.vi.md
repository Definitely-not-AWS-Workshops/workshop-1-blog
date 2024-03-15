---
title : "MFA cho Tài khoản AWS"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 2. </b> "
---

#### Hướng dẫn Thiết lập Multi-Factor Authentication (MFA)

Trong quy trình bảo mật, việc sử dụng Multi-Factor Authentication (MFA) rất quan trọng. Trong bước này, bạn sẽ sử dụng ba loại thiết bị MFA khác nhau để tăng cường tính bảo mật.

#### Thiết bị MFA ảo trên smartphone

Một cách phổ biến để thực hiện MFA là sử dụng các ứng dụng MFA trên điện thoại thông minh. Có ba ứng dụng phổ biến mà bạn có thể sử dụng:

1. Microsoft Authenticator
2. Google Authenticator
3. Okta Verify

Để cài đặt MFA với các ứng dụng này, bạn cần thực hiện các bước sau:

1. Tải và cài đặt ứng dụng từ cửa hàng ứng dụng chính thức của hãng phát triển.
2. Theo hướng dẫn trong ứng dụng, thêm tài khoản bảo mật bằng cách quét mã QR hoặc nhập mã cung cấp.

#### Khóa bảo mật U2F cứng

Khóa bảo mật U2F (Universal 2nd Factor) cung cấp một lớp bảo mật bổ sung thông qua cổng USB. Để cài đặt khóa bảo mật U2F, bạn cần thực hiện các bước sau:

1. Mua khóa bảo mật U2F tương thích với hệ thống của bạn.
2. Kết nối khóa vào cổng USB của máy tính.
3. Theo hướng dẫn của hãng sản xuất, thực hiện quá trình đăng ký và cài đặt.

#### Thiết bị MFA phần cứng khác

Ngoài các tùy chọn trên, còn có các thiết bị MFA phần cứng khác như khóa bảo mật Gemalto. Để sử dụng các thiết bị này, bạn cần tuân thủ hướng dẫn cụ thể từ nhà sản xuất.

#### Liên kết nhanh đến các phần hướng dẫn chi tiết

1. [Thiết lập với thiết bị MFA ảo](#1-virtual-mfa-device)
2. [Thiết lập với Khóa Bảo mật U2F](#2-u2f-security-key)
3. [Thiết lập với thiết bị MFA phần cứng khác](#3-other-hardware-mfa-device)

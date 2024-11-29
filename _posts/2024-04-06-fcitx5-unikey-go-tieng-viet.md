---
layout: post
title: Mình đã chuyển sang Fcitx5 thay vì IBus Bamboo và IBus Unikey
date: 2024-04-06 16:16:00+0700
description: Bộ gõ Tiếng Việt thay thế cho IBus Unikey và IBus Bamboo
tags: fcitx5 unikey vietnamese
categories: utilities
giscus_comments: true
related_posts: false
toc:
  beginning: true
---

### Giới thiệu

Trước đây lúc mới bước chân vào xài Linux cách đây 6 năm, mình đã bắt đầu với Ubuntu (GNOME) và IBus Unikey, sau một thời gian cảm thấy bộ gõ này không đáp ứng được nhu cầu của bản thân, mình đã tìm thấy một bộ gõ tương tự nhưng hỗ trợ nhiều tính năng hơn là IBus Bamboo và mình đã xài đến tận đầu năm 2024.

Khoảng thời gian này mình thường xài 2 distro chính là Ubuntu rồi sau đó là Fedora với Desktop Environment là GNOME, mọi thứ vẫn hoạt động khá tốt đẹp, cho đến một ngày đầu năm 2024, mình quyết định chuyển sang xài KDE Plasma (Wayland) trên openSUSE Tumbleweed và đây là lúc mọi vấn đề bắt đầu.

Sau khi cài đặt IBus Bamboo, chế độ gõ mặc định gạch chân được chọn và mình không có cách nào để chuyển chế độ gõ, chọn menu IBus trên menu bar lại không hoạt động bình thường, lúc hiển thị lúc không và cũng hiển thị không đúng. Thậm chí một khi menu hiển thị và hover vào chọn 'Chế độ gõ mặc định' thì ngay lập tức menu bị nháy một cái và hidden luôn, nếu vẫn cố gắng làm lại thì sẽ crash luôn menu bar.

Một vấn đề nữa mình gặp phải là lúc đang để chế độ 'Tiếng Việt' do không chuyển được chế độ gõ mặc định nên gõ password bị lộ ra hết.

Lúc này mình đã tìm đến một bộ gõ khác là `fcitx5` và mọi vấn đề được giải quyết.

### Cài đặt

- fcitx5
- fcitx5-qt
- fcitx5-gtk
- fcitx5-unikey
- kcm-fcitx5

Hiện mình đang xài openSUSE Tumbleweed và có thể cài từ các resources sau:

Truy cập [fcitx5](https://software.opensuse.org//download.html?project=openSUSE%3AFactory&package=fcitx5), [fcitx5-qt](), [fcitx5-gtk](https://software.opensuse.org/package/fcitx5-gtk), [fcitx5-unikey](https://software.opensuse.org/download/package?package=fcitx5-unikey&project=M17N), [kcm5-fcitx](https://software.opensuse.org/download/package?package=kcm5-fcitx&project=openSUSE%3AFactory) tải xuống các files .ymp (click download) và click to install (đây cũng là một điểm mình khá thích openSUSE so với các distro khác từng xài trước đây) hoặc cũng có thể cài đặt qua script:

```sh
sudo zypper addrepo https://download.opensuse.org/repositories/openSUSE:Factory/standard/openSUSE:Factory.repo
sudo zypper addrepo https://download.opensuse.org/repositories/M17N/openSUSE_Tumbleweed/M17N.repo
sudo zypper refresh
sudo zypper install fcitx5
sudo zypper install fcitx-qt5
sudo zypper install fcitx5-gtk
sudo zypper install kcm5-fcitx
sudo zypper install fcitx5-unikey
```

Để chọn bộ gõ, vào `System Settings` -> `Virtual Keyboard` chọn fcitx5.

**_Lưu ý:_** Nếu xài Wayland, khi khởi động các app các bạn nhớ thêm các options `--enable-features=UseOzonePlatform --ozone-platform=wayland --enable-wayland-ime`.

Ví dụ khi mình xài Typora:

```sh
Typora --enable-features=UseOzonePlatform --ozone-platform=wayland --enable-wayland-ime
```

Tuy nhiên mình thường tạo/chỉnh sửa file _.desktop_ rồi thêm options này vô luôn để mở app cho tiện.

Mình viết bài này nhằm mục đích chia sẻ trải nghiệm gõ Tiếng Việt trên linux của cá nhân mình, đồng thời hy vọng có thể giúp các bạn cũng đã và đang gặp phải vấn đề tương tự như mình.

Theo mình, IBus Bamboo vẫn là bộ gõ hoạt động rất tốt với GNOME vì một khoảng thời gian gần 6 năm mình vẫn hài lòng với nó, và fcitx5 là một lựa chọn tốt đối với KDE (ít nhất theo cảm nhận của mình hiện tại).

Một số tài liệu tham khảo thêm:

- https://fcitx-im.org/wiki/Using_Fcitx_5_on_Wayland

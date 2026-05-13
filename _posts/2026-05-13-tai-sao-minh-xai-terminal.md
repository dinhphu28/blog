---
layout: post
title: "Tại sao mình xài terminal trong hầu hết công việc làm software"
date: 2026-05-13 14:19:00+0700
description: Anh em làm software nói riêng và IT nói chung đều có những công cụ ruột, ông thì xài IntelliJ, ông thì VS Code, và hàng tá app khác như Postman, Git Kraken. Mỗi app một cách xài khác nhau, cập nhật GUI cái là không biết cái mình đang xài đi đâu, riêng mình thì chọn làm việc với terminal.
mermaid:
  enabled: false
  zoomable: false
tags: terminal vim productivity linux mac
categories: utilities
citation: false
giscus_comments: true
toc:
  beginning: true
---

## Vấn đề

Anh em có bao giờ trải qua cảm giác app mình đang xài thay đổi bộ UI mới chưa, cái button hồi giờ anh em xài không còn ở đó nữa mà trôi đi đâu mất.

Hoặc anh em cất công cả buổi trời để mò ra chỗ setup cái IntelliJ Idea sao cho nó nhận env, cái nút **run** nó chạy đúng ý anh em rồi phải capture cái màn hình, note lại để sau này đỡ mất công mò lại từ đầu. Và tới lúc đó nó cập nhật bộ UI mới. Y chang cái cách mà anh em đang xài Windows 7 lên 10, 11 vậy.

Chưa kể mỗi framework, ngôn ngữ hay công cụ mà anh em đang xài nó lại cấu hình một kiểu khác nhau.
Tại sao phải đau đầu và mất thời gian cho mấy thứ đó, dành thời gian làm những thứ khác hiệu quả hơn đi.
Nên từ ngày đó mình đã chuyển sang làm việc với TUI nhiều hơn.

## Viết note cho những lần sau

Với CLI, mọi thứ anh em sử dụng là lệnh, do đó đương nhiên là có thể lưu lại dưới dạng text dễ dàng để lần tới tham khảo thay vì phải capture màn hình rồi thêm một đống mô tả cho cái hình đó.

Thậm chí nếu nhiều bước anh em cứ viết hẳn thành một file script, tới đó chạy script đó là được, khỏi phải click click dài dòng. Như mình viết sẵn luôn một bộ script cho Linux, khi nào cài máy mới thì chạy một cái một là xong.

## Coding

Mình làm Java và mỗi lần mở cái IntelliJ lên là nó cắn hẳn một khúc RAM của mình, chưa kể lúc mới khởi động lên bao lag. Đang code Java quay qua làm Typescript thì sao, mở VS Code. À thì xài VS Code cho mọi thứ cũng được =)))

Có điều mình thấy việc đang gõ phím mà lâu lâu phải bỏ tay ra cầm vô con chuột để di chuyển tới chỗ này chỗ kia, click click các thứ thì khá là mất thời gian nên mình xài Vim. Đương nhiên là anh em xài VS Code, IntelliJ cũng có Vim mode.

Mình thì xài tmux + NeoVim nên gần như không phải đụng đến chuột nếu không cần phải ra khỏi terminal.

## Setup công việc của mình

Với tmux, mình có thể chuyển qua lại giữa các windows, panes. Mỗi window, pane tương đương một app hoặc nhóm công việc.

Thay vì xài Postman hay Insomnia, mình xài **curl**, để format JSON cho đẹp thì có thể sử dụng **jq**, nó cũng cho phép truy vấn JSON nên khá tiện.

```sh
curl -X 'https://example.com/articles' \
	-H 'x-api-key: apikey123' | jq
```

Anh em xài Linux hay Mac cũng không lạ gì mấy lệnh cơ bản như truy cập file system.

Di chuyển tới thư mục nhanh thì dùng **fzf**, grep file thì có **ripgrep**.

Thực ra mình cũng ko cần nhớ hết mấy cái lệnh phức tạp hay xài đâu, có thể dùng tổ hợp phím **^R** với **fzf** để tìm kiếm nhanh lệnh đã xài trong history thay vì gõ lại từ đầu.

Chưa kể có thể kết hợp với **oh-my-zsh auto-complete** gõ vài chữ đầu là nó hiện đầy đủ rồi.

Mình có sẵn server để ở nhà nên việc sử dụng chủ yếu bằng terminal này khá tiện, cần gì cứ SSH vô là được. Bản chất mọi thứ mình làm là trên server nên đang làm dở thì cứ đóng máy lại, lần tới SSH vô lại mở tmux session lên là có thể tiếp tục đúng chỗ đó.

Nghĩa là chỉ cần anh em có một thiết bị có thể SSH là đủ, thậm chí là điện thoại iPad cũng có thể code được.

Đôi lúc đi xa mà muốn mang đồ gọn trong lúc vẫn có thể xử lý được công việc thì với mình iPad là đủ. Dĩ nhiên là điện thoại cũng được nhưng nó quá nhỏ và mình vẫn chưa muốn thay mắt mới =)))

## Kết

Mình chọn terminal không hẳn là vì bài xích các app sử dụng GUI, đơn giản là nó phù hợp và thuận tiện với cách làm việc của cá nhân mình. Mấy cái app dùng truy vấn SQL databases thì mình vẫn dùng DataGrip, mấy cái mysql client hay psql CLI chữa cháy thôi.

Đối với một số anh em thì xài terminal có vẻ cool, cũng là một lý do để anh em lựa chọn.

Mình viết bài này mục đích chia sẻ trải nghiệm cá nhân. Anh em nào hứng thú hoặc có setup làm việc nào thú vị có thể để lại comments dưới đây.

---
layout: post
title: Context trong Linux  
subtitle:  Định nghĩa về các loại context (ngữ cảnh) trong Linux Kernel.
gh-repo: 
gh-badge: [star, fork, follow]
tags: [kernel, embedded, context]
---

Process context, interrupt context, user(space) context, system call context, atomic context, nonatomic context,... là những khái niệm về context hay gặp khi làm việc với Linux kernel. Chắc hẳn nhiều bạn cũng giống như mình, ban đầu sẽ rất bối rối khi gặp những khái niệm này. Tại sao lại có nhiều loại context như này? Có cái nào giống cái nào không? Hôm nay mình sẽ đi tổng hợp những kiến thức liên quan đến các loại ```context - ngữ cảnh``` trong linux kernel:
### 1. Process context: 
#### 1.1. Process context trên user space:
Một trong những nhiệm vụ chính của process là thực thi lệnh chương trình. Những lệnh này được đọc từ một file thực thi (executable file) là thực thi cùng với không gian địa chỉ của chương trình. Nếu chương trình chỉ gọi các ```library call``` thì chương trình đó sẽ chạy trên process context ở ```user space```. 

#### 1.2. Process context trên kernel space:
Khi một chương trình thực hiện gọi tới system call, nó nhảy vào kernel-space. Ở thời điểm này, kernel thay mặt cho process thực thi chương trình, và chương trình bây giờ sẽ chạy trên process context ở ```kernel space```. 

#### 1.3. Lưu ý:
Ở ```process context```, kernel có thể ngủ (lấy ví dụ khi một system call blocks khi gọi ```schedule()```), và cũng hoàn toàn preemtible. Nhắc lại thì preemtible là có thể xảy ra chen hàng, chen hàng là hiện tượng một process A đang chạy thì bị ngắt, sau khi thực hiện ngắt xong thì process B lại được thực hiện thay vì process A. 

### 2. Interrupt context:

Khi thực thi một trình phục vụ ngắt (interrupt handler), đơn giản là kernel đang ở trong ```interrupt context```. Trái ngược với ```process context```, ```interrupt context``` không liên kết gì với một process, không chạy trên ```user space``` với mục đích giúp trình phục vụ ngắt phản hồi thật nhanh một ngắt rồi exit. Loại ngữ cảnh đặc biệt này thỉnh thoảng còn được gọi là ```atomic context```, vì code thực thi ở ngữ cảnh này không được phép block (hay ngủ). (```Nonatomic context``` thì ngược lại, như vậy có vẻ nonatomic ám chỉ ```process context```). ```Interrupt context``` không thể ngủ, do đó không thể sử dụng một số hàm ở đây ( ví dụ như sleep function).  ```Interrupt context``` vô cùng khắt khe về mặt thời gian (time-critical) vì trình phục vụ ngắt đã ngắt một chương trình khác, vậy nên trình phục vụ phải nhanh và đơn giản. Ví dụ nếu gọi hàm ```sleep()``` trong interrupt thì rất dễ gây xảy ra hiện tượng deadlock (Mình sẽ giải thích về deadlock ở 1 bài khác).
![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/1yim01qsk0_making-linux-do-hard-realtime-74-638.jpg)


Nếu bạn muốn thay ngôn ngữ trong boot.wim bằng cách sử dụng file ngôn ngữ lấy từ một boot.wim khác (ví dụ: từ file boot.wim của phiên bản zh-CN), điều này hoàn toàn khả thi. Dưới đây là cách thực hiện:

1. Chuẩn bị
	•	Một file boot.wim gốc (hiện tại của bạn, cần đổi ngôn ngữ).
	•	Một file boot.wim có ngôn ngữ zh-CN (để lấy các file cần thiết).
	•	Công cụ: DISM, 7-Zip (hoặc công cụ giải nén file WIM khác).

2. Các bước thực hiện

Bước 1: Mount cả hai file boot.wim
	1.	Tạo 2 thư mục để mount:

mkdir C:\Mount\Original
mkdir C:\Mount\ZhCN


	2.	Mount file boot.wim gốc (của bạn):

dism /mount-wim /wimfile:"D:\Win11Setup\sources\boot.wim" /index:2 /mountdir:C:\Mount\Original


	3.	Mount file boot.wim zh-CN (có ngôn ngữ bạn cần):

dism /mount-wim /wimfile:"D:\ZhCN\boot.wim" /index:2 /mountdir:C:\Mount\ZhCN

Bước 2: Sao chép file ngôn ngữ từ zh-CN sang file gốc
	1.	Xác định các thư mục ngôn ngữ cần sao chép:
	•	File ngôn ngữ thường nằm trong các thư mục:
	•	C:\Mount\ZhCN\Windows\System32\lang
	•	C:\Mount\ZhCN\Windows\System32\en-US (hoặc zh-CN)
	•	C:\Mount\ZhCN\Windows\SysWOW64\lang
	•	C:\Mount\ZhCN\Windows\SysWOW64\zh-CN
	•	C:\Mount\ZhCN\Windows\Boot\DVD\zh-CN
	2.	Sao chép các file và thư mục cần thiết:
	•	Dùng lệnh xcopy hoặc công cụ quản lý file:

xcopy C:\Mount\ZhCN\Windows\System32\zh-CN C:\Mount\Original\Windows\System32\zh-CN /E /I
xcopy C:\Mount\ZhCN\Windows\SysWOW64\zh-CN C:\Mount\Original\Windows\SysWOW64\zh-CN /E /I
xcopy C:\Mount\ZhCN\Windows\Boot\DVD\zh-CN C:\Mount\Original\Windows\Boot\DVD\zh-CN /E /I

Bước 3: Cập nhật cấu hình ngôn ngữ
	1.	Đặt ngôn ngữ mặc định cho boot.wim:

dism /image:C:\Mount\Original /set-allintl:zh-CN
dism /image:C:\Mount\Original /set-inputlocale:zh-CN
dism /image:C:\Mount\Original /set-syslocale:zh-CN
dism /image:C:\Mount\Original /set-userlocale:zh-CN


	2.	Kiểm tra cấu hình sau khi chỉnh sửa:

dism /image:C:\Mount\Original /get-intl

Bước 4: Unmount và lưu thay đổi
	1.	Lưu thay đổi và unmount file gốc:

dism /unmount-wim /mountdir:C:\Mount\Original /commit


	2.	Unmount file zh-CN (không cần commit):

dism /unmount-wim /mountdir:C:\Mount\ZhCN /discard


	3.	Xóa các thư mục mount (tùy chọn):

rmdir /s /q C:\Mount

3. Đóng gói lại file ISO
	•	Chép file boot.wim đã chỉnh sửa trở lại thư mục sources của file ISO.
	•	Dùng công cụ như Rufus hoặc PowerISO để tạo lại file ISO.

Lưu ý:
	•	Phương pháp này chỉ thay đổi ngôn ngữ trong Windows Setup. Để cài đặt ngôn ngữ hệ thống, bạn cần chỉnh sửa install.wim.
	•	Đảm bảo rằng bạn chỉ sao chép các file ngôn ngữ cần thiết để tránh làm tăng kích thước file boot.wim quá nhiều.
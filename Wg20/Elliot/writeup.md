Việc bắt đầu bằng xử lý 2 file ở thư mục Little help
file init.sh chỉ là để tạo ra 1 video hoàn chỉnh có âm thanh thôi. 
Phần video là gợi ý những bước cần thực hiện:
- Mở file lưu trữ ảnh
- lấy thuật toán sinh RSA đc giấu trong bức ảnh nào đó
- Dùng thuật toán đó thì đc private key để giải mã file ở trong "Ecorp's encrypted data"
- Flag nằm ở đấy
Bí mật ở file ".wav" ta dùng wavsteg để lấy đc gợi ý tiếp theo gồm:
- mã của file "track0.iso" (dùng hxdEditor thì ta sẽ biết là cần đổi sang đuôi ".zip")
- Đầu vào của thuật toán sinh private key
stegolsb wavsteg -r -i audio.wav -o output.txt -n 2 -b 100

Thu được

Từ đây là có wrong password là "IM9G_95791.png" và file.name là "" (dùng cho phần sau)

Thì ý tưởng của mình là dùng mật khẩu kia đảo thứ tự các chữ cái để ra được mật khẩu thật (bruteforce). Mình có gợi ý bằng các để chữ "IMG_" và ".png" để mọi người hiểu ra là mật khẩu có dạng gần giống với định dạng tên ảnh nhưng mọi thứ k theo đúng ý của mình :( :( :( nên đã có 1 hint thêm vào cho phần này

Sau khi bruteforce với không gian là 6! (= 720 vì chỉ bruteforce phần số thôi) thì ta sẽ đc mật khảu là "IMG_599197.png"

Giải nén file thì ta có 6 bức ảnh. Lúc đầu thì mình định làm giống hệt video từng lệnh 1 nhưng thế thì lại dễ quá nên mình sang 1 cách khác

Đó là giấu 2 nửa của thuật toán sinh RSA và bit r của file "IMG_599192.png" và bit g của file "IMG_599193.png"

Tìm được và ghép 2 nửa lại thì mình sẽ có 1 thuật toán như sau :

```cpp
f.seek(1024)
salt = f.read(32)
master = PBKDF2(password, salt, count=10000)
def notrand(n):
	notrand.i +=1
	return PBKDF2(master, str(notrand.i), dkLen=n, count =1)
notrand.i=0
key = RSA.generate(2048, randfunc=notrand)
print key.exportKey()
```

ta thêm phần import

```cpp
from Crypto.Protocol.KDF import PBKDF2
from Crypto.PublicKey import RSA
```

thứ bị thiếu bây giờ là pass và file
Nhưng mà lúc giải mã ở phần âm thanh thì ta lại có file.name = "IMG_599197.png" chính là nó
Còn password thì là password tìm được. Chạy xong thì ta sẽ có PK và giải mã thôiiiiiiiiiii

flag:  WhiteHat{Th4nk_G0d_Y0u_Ar3_H3r3_Elliot}

PS: Thực ra mình làm pwn nhưng cái ý tưởng ra đề này có từ lâu rồi nên tiện đợt tháng 9 mr.robot ra season cuối thì làm. Nhưng k ngờ btc delay đến tận đầu tháng 1 mà lúc ý mr.robot hết rồi :( buồn vl
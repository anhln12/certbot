# certbot
# Cài đặt SSL Let’s Encrypt với Certbot trên Nginx

SSL là gì?

SSL là một loại chứng chỉ giúp mã hóa các thông tin trên những thiết bị hoặc các ứng dụng có hỗ trợ mã hóa này bằng chứng chỉ SSL. Chứng chỉ SSL sẽ có hai phần gồm những phần Private Key và Public Key, trong đó Public Key nó sẽ được cài ở các ứng dụng đầu cuối mà trình duyệt hay các ứng dụng khác cũng có thể truy cập đọc được, còn Private Key nó sẽ được cài đặt ở các ứng dụng xử lý tiếp nhận dữ liệu.

Mục đích hoạt động của nó giống như chìa khóa để giúp giải mã những dữ liệu gửi đi từ thiết bị đầu cuối cũng đã được mã hóa thông qua Public Key.

Let’s Encrypt là gì?

Let’s Encrypt là một tổ chức xác thực SSL giống như Comodo, GeoTrust, Symantec nhưng cái điểm khác đó là họ là tổ chức phi lợi nhuận được thành lập với sự bảo trợ của các tổ chức lớn trên thế giới Cisco, Akamai, Mozilla, Facebook…Do đó, những chứng chỉ SSL Let’s Encrypt sẽ không khác gì với những loại chứng chỉ SSL khác mà chỉ khác nhau ở chỗ bạn phải gia hạn mỗi 90 ngày một lần nếu muốn tiếp tục sử dụng.

Các bước thực hiện
Bước 1: Cài đặt Cerbot Let’s Encrypt Client
Với Let’s Encrypt các bạn có thể cài đặt nhanh nó thông qua Certbot client  chỉ với vài dòng lệnh đơn giản, việc còn lại của bạn chỉ là ngồi chờ hệ thống chạy tự động.

Đầu tiên bạn cần cài đặt EPEL repository:
```
yum -y install epel-release
```

Bây giờ thì cài đặt certbot-nginx bằng câu lệnh sau
```
yum -y install certbot-nginx
```

Bước 2: Cài đặt SSL Let’s Encrypt
```
certbot --nginx -d azdigi.cf -d www.azdigi.cf
```

```
[root@template conf.d]# certbot --nginx -d azdigi.cf -d www.azdigi.cf
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator nginx, Installer nginx
Enter email address (used for urgent renewal and security notices)
 (Enter 'c' to cancel): kiendt@azdigi.cf  (Nhập email của bạn)
Starting new HTTPS connection (1): acme-v02.api.letsencrypt.org

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf. You must
agree in order to register with the ACME server. Do you agree?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: Y   (Nhấn Y để đồng ý điều khoản)

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Would you be willing, once your first certificate is successfully issued, to
share your email address with the Electronic Frontier Foundation, a founding
partner of the Let's Encrypt project and the non-profit organization that
develops Certbot? We'd like to send you email about our work encrypting the web,
EFF news, campaigns, and ways to support digital freedom.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: N   (Nhấn N để từ chối các thông tin, tin tức từ letsencrypt và Certbot )
Account registered.
Requesting a certificate for azdigi.cf and www.azdigi.cf
Performing the following challenges:
http-01 challenge for azdigi.cf
http-01 challenge for www.azdigi.cf
Waiting for verification...
Cleaning up challenges
Deploying Certificate to VirtualHost /etc/nginx/conf.d/azdigi.cf.conf
Deploying Certificate to VirtualHost /etc/nginx/nginx.conf
Redirecting all traffic on port 80 to ssl in /etc/nginx/conf.d/azdigi.cf.conf
Redirecting all traffic on port 80 to ssl in /etc/nginx/nginx.conf

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Congratulations! You have successfully enabled https://azdigi.cf and
https://www.azdigi.cf
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/azdigi.cf/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/azdigi.cf/privkey.pem
   Your cert will expire on 2021-04-14. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot again
   with the "certonly" option. To non-interactively renew *all* of
   your certificates, run "certbot renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

Như vậy đã cài đặt thành công SSL thông qua Certbot, đường đẫn lưu file chứng chỉ của website sẽ nằm tại đường dẫn tương ứng.
```
- Certificate: /etc/letsencrypt/live/azdigi.cf/fullchain.pem
- Private Key: /etc/letsencrypt/live/azdigi.cf/privkey.pem 
```

Chứng chỉ Let’s Encrypt chỉ có hiệu lực trong 90 ngày, do đó bạn có thể thiết lập cronjob  để chứng chỉ tự động gia hạn nếu như hết hạn.
```
00 6 * * * /usr/bin/certbot renew --quiet
```
Chú thích: Cronjob này có nghĩa là cứ đúng 6:00 AM nó sẽ check chứng chỉ, nếu chứng chỉ hết hạn sẽ tự động gia hạn. Ngược lại, nếu còn hạn sẽ không thực hiện gia hạn.

Bước 3: Kiểm tra chứng chỉ sau cài đặt
Các bạn có thể truy cập trang SSL Checker theo đường dẫn: https://www.sslshopper.com/ssl-checker.html

<img src=https://imgur.com/fFKeu20>

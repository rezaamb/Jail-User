1) ساخت یوزر و تعیین شِل

sudo useradd -m -d /home/<یوزر> -s /bin/bash <یوزر>
sudo passwd <یوزر>

2) آماده‌سازی chroot و bind-mount به carbonapps

sudo mkdir -p <جیل_روت>/{bin,lib,lib64,usr/bin,dev,data}
sudo chown root:root <جیل_روت>
sudo chmod 755 <جیل_روت>

# bind-mount دایرکتوری واقعی carbonapps به داخل jail
sudo mount --bind <مسیر_کربن> <جیل_روت>/data

# دائمی‌کردن در fstab
sudo vim /etc/fstab
# این خط را اضافه کن:
# <مسیر_کربن>  <جیل_روت>/data  none  bind  0  0

sudo mount -a

3) کپی باینری‌های لازم داخل jail

حداقل‌ها: bash, ls, cp, mv, mkdir, rm, touch. (اگه می‌خوای vim هم باشه، اضافه کن؛ سنگین‌تره.)

for b in /bin/bash /bin/ls /bin/cp /bin/mv /bin/mkdir /bin/rm /bin/touch; do
  sudo cp -v $b <جیل_روت>/bin/
  ldd $b | awk '{print $3}' | grep -E '^/' | sudo xargs -I{} cp -v --parents {} <جیل_روت>/
done

4) ساخت devهای ضروری داخل jail (حداقلی)

sudo mknod -m 666 <جیل_روت>/dev/null c 1 3
sudo mknod -m 666 <جیل_روت>/dev/zero c 1 5
sudo mknod -m 666 <جیل_روت>/dev/tty c 5 0
sudo mknod -m 666 <جیل_روت>/dev/urandom c 1 9
sudo mknod -m 666 <جیل_روت>/dev/random c 1 8

5) هوم داخل jail را بکن /data

(تا یوزر بعد از لاگین مستقیماً توی همون پوشه بیاد)

sudo usermod -d /data <یوزر>
# مالکیت نوشتن برای یوزر روی carbonapps (بدون تغییر owner سرویس MI)
sudo apt-get update && sudo apt-get install -y acl
sudo setfacl -m u:<یوزر>:rwx <مسیر_کربن>
sudo setfacl -d -m u:<یوزر>:rwx <مسیر_کربن>

6) پیکربندی SSH (chroot + شل)

sudo vim /etc/ssh/sshd_config

مطمئن شو این هست:

Subsystem sftp internal-sftp

و این بلوک رو آخر فایل اضافه کن:

Match User <یوزر>
    ChrootDirectory <جیل_روت>
    ForceCommand /bin/bash --noprofile --norc
    X11Forwarding no
    AllowTCPForwarding no

سپس:

sudo systemctl reload sshd

7) تست

از سیستم خودت:

ssh <یوزر>@<IP_سرور>
# باید وارد شل بشی و مسیر کاری‌ت /data باشه (همون carbonapps واقعی به‌صورت bind).
pwd
ls -la

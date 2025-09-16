sudo mkdir -p /new/etc/wso2mi-4.4.0/repository/deployment/server/carbonapps/


sudo chown carbon:carbon /new /new/etc /new/etc/wso2mi-4.4.0 /new/etc/wso2mi-4.4.0/repository /new/etc/wso2mi-4.4.0/repository/deployment /new/etc/wso2mi-4.4.0/repository/deployment/server /new/etc/wso2mi-4.4.0/repository/deployment/server/


sudo chmod 755 /new /new/etc /new/etc/wso2mi-4.4.0 /new/etc/wso2mi-4.4.0/repository /new/etc/wso2mi-4.4.0/repository/deployment /new/etc/wso2mi-4.4.0/repository/deployment/server /new/etc/wso2mi-4.4.0/repository/deployment/server/



sudo mount --bind /etc/wso2mi-4.4.0/repository/deployment/server/carbonapps/ /new/etc/wso2mi-4.4.0/repository/deployment/server/carbonapps/



sudo useradd -s /bin/bash -g carbon -d /new/etc/wso2mi-4.4.0/repository/deployment/server/carbonapps/ carbon

usermod -s /usr/sbin/nologin  -d /sftp/carbon/data/carbonapps  -g carbon carbon

sudo usermod -s /bin/bash -d  /new/etc/wso2mi-4.4.0/repository/deployment/server/carbonapps/ -g carbon carbon

echo 'carbon:carbon' | sudo chpasswd

getent passwd carbon



sudo vim /etc/fstab

/etc/wso2mi-4.4.0/repository/deployment/server/carbonapps  /new/etc/wso2mi-4.4.0/repository/deployment/server/carbonapps  none  bind  0  0


sudo mount -a

# مثال

user: carbon

pass: carbon

home direc : 

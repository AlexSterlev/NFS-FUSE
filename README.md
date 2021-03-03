## Выполнено ДЗ №7

## NFS FUSE
 ### Серверная часть
  - Устанавливаем nfs-utils и запускаем сервисы.

  ````
 systemctl start firewalld
 yum install nfs-utils -y
 systemctl enable rpcbind nfs-server
 systemctl start rpcbind nfs-server
  ````

  - Создадим дериктории и дадим права доступа.

  ````
   mkdir -p /var/nfs
   chmod -R a+r /var/nfs
   mkdir -p /var/nfs/upload
   chmod -R a+w /var/nfs/upload
  ````

  - Правим файл конфигурации NFS-сервера.

  ````
  echo "/var/nfs 192.168.1.0/24(rw,sync,all_squash)" > /etc/exports
  exportfs -r
  ````

  - Настраиваем брандмауэр.

  ````
   firewall-cmd --permanent --zone=public --add-service=nfs
   firewall-cmd --permanent --zone=public --add-service=nfs3
   firewall-cmd --permanent --zone=public --add-service=mountd
   firewall-cmd --permanent --zone=public --add-service=rpc-bind
   firewall-cmd --reload
  ````

### Клиентская часть часть

- Устанавливаем nfs-utils и запускаем сервисы.

  ````
   yum install nfs-utils -y
   systemctl start rpcbind
   systemctl enable rpcbind
  ````

- Создадим точку монтирования.

  ````
 mkdir /mnt/nfs-share
  ````

  - Отредактируем fstab для автоматического монтирования.  

  ````
echo "##### NFS MOUNT #####" >> /etc/fstab
echo "192.168.1.1:/var/nfs/    /mnt/nfs-share/ nfs auto,nofail,vers=3,udp  0 0" >> /etc/fstab
  ````

  - Монтируем

  ````
  mount -a
  ````

### Тестируем

  ````
  mount | grep nfs
  ````
  - Пытаемся записать в коревую директорию 

  ````
touch /mnt/nfs-share/test
touch: cannot touch ‘/mnt/nfs-share/test’: Permission denied
  ````

 - Пытаемся записать в директорию /mnt/nfs-share/upload

  ````
 [vagrant@client ~]$ touch /mnt/nfs-share/upload/test
  ````

 - Успешно

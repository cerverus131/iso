b# iso

# Raid 1

## linux
```
### creacion de particiones 
   1. sudo fdisk -l
   2. sudo fdisk /dev/sdX
   3. sudo mkfs.ext4 /dev/sdX1
### Crear el RAID 1
   4. sudo mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sdb /dev/sdc
   
   +  Verificar el estado del RAID
   5. cat /proc/mdstat
      - sudo mdadm --detail /dev/md0 (funcionan los dos)

      
   +  Formatear y montar el RAID
   6. sudo mkfs.ext4 /dev/md0
      - crear una carperta  en media para tenet localizado el montaje
        
   + Monta el Raid
   7. sudo mount /dev/md0 /mnt/raid1
     
   + para que se monte automaticamente la reiniciar
   8. nano /etc/fstab
     
   + Guardar la configuración del RAID
   9. /dev/md0 /mnt/raid1 ext4 defaults 0 0
```

### volumen lógico
>[!note]
>necesitas tenetinstalado el lvm2 para hacer discos logicos\
>Para el disco fisico se usan las praticiones es decir (/dev/sdX1)
```
  1. sudo pvcreate /dev/sdX /dev/sdX

   +Verifica los volúmenes físicos creados
   2. sudo pvdisplay

   +Crea un grupo del volumen
   3. sudo vgcreate vg_storage /dev/sdb /dev/sdc

   +Verifica el grupo de volúmenes
   3.1 sudo vgdisplay

   +Crear volúmenes lógicos
   4. sudo lvcreate -L 50G -n lv_datos vg_storage
   Tambien funciona:
   4. sudo lvcreate -l 100%FREE -n lv_full vg_storage

   +Formatear y montar el volumen lógico
   5. sudo mkfs.ext4 /dev/vg_storage/lv_datos
   6. sudo mkdir /media/montage
   7. nano /etc/fstab
   8. /dev/'el grupo'/'nombre del volumen' /media/'punto de montage' ext4 defaults 0 0

   +Ampliar un volumen lógico (opcional)
   0. sudo lvextend -L +10G /dev/vg_storage/lv_datos
   0. sudo resize2fs /dev/vg_storage/lv_datos
```


   
## windous

# _Simulacion de Fallos_
Esto tambien se usa para sacar el disco del raid
```
## linux
+ Marcar un disco como fallido
   1. sudo mdadm --fail /dev/md0 /dev/sdb
+ Eliminar el disco fallido del RAID  
  1. sudo mdadm --remove /dev/md0 /dev/sdb
+ Agregar un nuevo disco para reemplazo
  1. sudo mdadm --add /dev/md0 /dev/sdb
```
# Cuotas 
>[!note]
>Primo hay que instalar una plicacion llamada quota
>sudo apt install quota quotatool -

+ habilita las quotas en el sistemade archivos
1. /dev/md1 /media/raid1 ext4 defaults,usrquota,grpquota


+Remontar la partición y crear los archivos de cuota 
1. sudo mount -o remount /
2. sudo quotacheck -cum /
3. sudo quotacheck -cgm /
4. sudo quotaon -v /  

+ Asignar cuotas a un usuario
6. sudo edquota -u usuario

+ Asignar cuotas a un grupo

----------
# WindousServer

\\el nombre del sevidor \ carpeta o archivo \ % username %


----------
# Primer Paso
tener las dos maquinas en adaptador puente y nat condos targetas
# Segundo Paso
ls -l /etc/netpplan
 
nano /etc/netplan/(copia el nombre del archivo del ls -l)

# dentro del archivo
 
network:
  athernets:
	  enp0s3:
			dhcp4: true
		enp0s8: 
			addresses: [la ip que tu quieras/24]

>[!importan]
no se usa el tabulador porque no te lo van a coger los espacios 
en la ip tienes que ponerlas en el mismo rango 

# Tercer paso
netplan applay





#linux informaacionde maquinas

windous 11.2000 ip 192.168.40.11
windous2 ip 192.168.40.10
windous server ip 192.68.40.36s




# samba
1.adduser **//añadir usuarios  
2. smbpasswd -a ^** //añade a un grupo de samba  
3.pdbedit -L //tedice los usuarios que tienes en samda  
## nos metemos en la parpeta /etc/samba/smb.conf  
Dentro del archivo abajo del todo tienes que esecribir  
[*es nombre de la carpera de uso compartido*]  
  path = /home/**  
  browseable = yes  
  guest ok = no  
  valid users = samba1  
  read only = yes  
  write list  = samba1  
  writable = yes  

  _netplan apply_(esto se usa para revisar los warning)

4. smbclient -L *la ip para acceder al servidor samba/tiene que eser la ip de la maquina donde esta alojado el servidor samba*  
para hacer lo cliente hay que instalar smbclient cifs-common
5.smbclient //*la ip del server*/*el archivo* -U *el usuario*
6. *montage de samba* mount -t cifs -o user=*el usuario* //*la ip del server samba*/*el archivo* /*la carpeta que se a preparado previamente para el montaje*
7. systemctl restart systemd-networkd //restaura los demonios _*todos los archivos de sistema u eterminen en %d son demonios*_
8. apt install samba krb5-config winding smbclient krb5-user //tenemos que instalar eseto para que la comunicacion sea total con windous y hacer dicha configuracion
9. [•nano /etc/hosts] 
   192.168.50.3 user.samba.com

11. rm /etc/resolv.com
12. [nano /etc/resolv.com]
  nameserver 192.168.50.3 //la ip de la maquina
  nameserver 8.8.8.8 //conexion con googel
  search samba //nombre del domunio

13.mv /etc/samba/smd-conf /etc/samba //renombramos el archivo smd.conf
14 // nos tendria que aparederel archivo kr5.conf
15. samba-tool group add fermin  //crear grupos 

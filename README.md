# iso

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


# iso
# creacion de particiones 
## linux
1. sudo fdisk -l
2. sudo fdisk /dev/sdX
3. sudo mkfs.ext4 /dev/sdX1


# Raid 1

## linux
+ Crear el RAID 1
4. sudo mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sdb /dev/sdc
+  Verificar el estado del RAID
5. cat /proc/mdstat
+  Formatear y montar el RAID
6. sudo mkfs.ext4 /dev/md0
   - crear una carperta  en media para tenet localizado el montaje
7. sudo mount /dev/md0 /mnt/raid1
8. nano /etc/fstab
+ Guardar la configuración del RAID
9. /dev/md0 /mnt/raid1 ext4 defaults 0 0


   
## windous

# _Simulacion de Fallos_
## linux
+ Marcar un disco como fallido
   1. sudo mdadm --fail /dev/md0 /dev/sdb
+ Eliminar el disco fallido del RAID  
  1. sudo mdadm --remove /dev/md0 /dev/sdb
+ Agregar un nuevo disco para reemplazo
  1. sudo mdadm --add /dev/md0 /dev/sdb



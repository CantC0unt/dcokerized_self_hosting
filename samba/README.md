## Example

```conf
#======================= Share Definitions =======================

[commons]
   comment = Shared Storage
   path = /mnt/share/localcloud/commons
   browseable = yes
   writable = yes
   valid users = @home
   force group = home
   create mask = 0664
   directory mask = 0775

[personal]
   comment = Personal Storage
   path = /mnt/share/localcloud/cantc0unt
   browseable = yes
   writable = yes
   valid users = cantc0unt
   create mask = 0664
   directory mask = 0775

```
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



## "The Guide"


### Samba Config 


```config
#======================= Share Definitions =======================

[<common share name>]
   comment = Shared Storage
   path = <path to share>
   browseable = yes
   writable = yes
   valid users = @<usergroup>
   force group = <usergroup>
   create mask = 0664
   directory mask = 0775

[<personal share name>]
   comment = Personal Storage
   path = <path to per user share>
   browseable = yes
   writable = yes
   valid users = <username>
   create mask = 0664
   directory mask = 0775
```

|value|replace with|conditions|
|-|-|-|
|`<common share name>`|Any name|It _**MUST**_ be unique.
|`<path to share>`|Any path|
|`<usergroup>`|Any group| Group _**MUST**_ have write access to share directories
|`<personal share name>`|Any name|It _**MUST**_ be unique.
|`<path to per user share>`|Any path|
|`<username>`|Any username|User _**MUST**_ have write access to share directories

\* you can add as many common share and personal share configs as you want.


### Run

```bash
sudo systemctl restart smbd
```
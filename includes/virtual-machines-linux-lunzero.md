När du lägger till diskar till en Linux-VM kan fel inträffa om en disk inte finns på LUN 0. Om du lägger till en disk manuellt med hjälp av den `azure vm disk attach-new` kommando och anger ett LUN (`--lun`) i stället för att tillåta att Azure-plattformen att avgöra lämpliga LUN, ta hand som en disk redan finns / kommer att finnas på LUN 0. 

Fundera på följande exempel visar en fragment på utdata från `lsscsi`:

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

Två diskar finns på LUN 0 och 1 för LUN (den första kolumnen i den `lsscsi` utdata information `[host:channel:target:lun]`). Båda diskarna måste vara accessbile från den virtuella datorn. Om du hade angett den första disken som ska läggas till på LUN 1 och den andra disken på LUN 2 manuellt, syns inte diskarna rätt från inom den virtuella datorn.

> [!NOTE]
> Azure `host` värdet är 5 i dessa exempel, men detta kan variera beroende på vilken typ av lagring som du väljer.
> 
> 

Detta disk är inte ett problem med Azure, men hur där Linux-kärnan följer SCSI-specifikationer. När Linux-kärnan igenom SCSI-bussen för anslutna enheter, måste en enhet finns på LUN 0 för systemet att fortsätta söka efter ytterligare enheter. Som exempel:

* Granska resultatet av `lsscsi` när du lägger till en datadisk för att kontrollera att du har en disk på LUN 0.
* Om disken inte visas korrekt i den virtuella datorn, kontrollerar du att det finns en disk på LUN 0.


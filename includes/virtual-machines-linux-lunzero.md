När du lägger till datadiskar till en Linux-VM, kan det uppstå fel om en disk inte finns på LUN 0. Om du lägger till en disk manuellt med hjälp av den `azure vm disk attach-new` kommandot och du anger en LUN (`--lun`) i stället för att tillåta Azure-plattformen för att avgöra lämpliga LUN noga med att en disk redan finns / kommer att finnas på LUN 0. 

Överväg följande exempel visar ett kodfragment på utdata från `lsscsi`:

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

De två diskarna finns på LUN 0 och 1 för LUN (den första kolumnen i den `lsscsi` utdata information `[host:channel:target:lun]`). Båda diskarna bör vara accessbile från den virtuella datorn. Om du har angett den första disken som ska läggas till på LUN-1 och den andra disken på LUN 2 manuellt, syns inte diskarna rätt från din virtuella dator.

> [!NOTE]
> Azure `host` värdet är 5 i de här exemplen, men detta kan variera beroende på vilken typ av lagring som du väljer.
> 
> 

Det här beteendet för disken är inte ett Azure-problem, men det sättet där Linux-kärnan följer SCSI-specifikationer. När Linux-kärnan genomsöker SCSI-bussen efter anslutna enheter, måste en enhet finns på LUN 0 för systemet att fortsätta söka efter ytterligare enheter. Därför:

* Granska utdata för `lsscsi` när du lägger till en datadisk för att kontrollera att du har en disk på LUN 0.
* Om disken inte visas korrekt i den virtuella datorn, kontrollerar du att det finns en disk på LUN 0.


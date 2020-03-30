---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09fa612e7e5c681da16bf19e94c626ae14a3b8a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590720"
---
När du lägger till datadiskar till en virtuell Linux-dator kan det uppstå fel om det inte finns en disk på LUN 0. Om du lägger till en `az vm disk attach -new` disk manuellt med kommandot`--lun`och du anger en LUN ( ) i stället för att låta Azure-plattformen bestämma lämplig LUN, se till att en disk redan finns / kommer att finnas på LUN 0. 

Tänk på följande exempel som visar ett `lsscsi`utdrag av utdata från:

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

De två datadiskarna finns på LUN 0 och LUN `lsscsi` 1 (den första kolumnen i utdatainformationen). `[host:channel:target:lun]` Båda diskarna bör vara tillgängliga inifrån den virtuella datorn. Om du manuellt hade angett den första disken som ska läggas till vid LUN 1 och den andra disken på LUN 2 kanske du inte ser diskarna korrekt inifrån den virtuella datorn.

> [!NOTE]
> Azure-värdet `host` är 5 i dessa exempel, men det kan variera beroende på vilken typ av lagring du väljer.
> 
> 

Den här diskens beteende är inte ett Azure-problem, men det sätt på vilket Linux-kärnan följer SCSI-specifikationerna. När Linux-kärnan söker igenom SCSI-bussen efter anslutna enheter måste en enhet hittas på LUN 0 för att systemet ska kunna fortsätta söka efter ytterligare enheter. Som sådan:

* Granska utdata `lsscsi` från när du har lagt till en datadisk för att kontrollera att du har en disk på LUN 0.
* Om disken inte visas korrekt i den virtuella datorn kontrollerar du att det finns en disk på LUN 0.


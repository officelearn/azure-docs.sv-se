---
title: Problem med en fullständig OS-disk på en virtuell Linux-dator
description: Så här löser du problem med en fullständig OS-disk på en virtuell Linux-dator
author: v-miegge
ms.author: tibasham
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 11/20/2020
ms.openlocfilehash: 2e350fe297b2aaf89ac302baaefb29092cfe2532
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523647"
---
# <a name="issues-with-a-full-os-disk-on-a-linux-virtual-machine"></a>Problem med en fullständig OS-disk på en virtuell Linux-dator

När operativ system disken för en virtuell Linux-dator (VM) blir full kan detta orsaka problem med den virtuella datorns korrekta drift.

## <a name="symptom"></a>Symptom

När du försöker skapa en ny fil får du följande meddelande:

```
username@AZUbuntu1404:~$ touch new 
touch: cannot touch “new”: No space left on device 
username@AZUbuntu1404:~$
```

Flera daemonar visar sedan att de inte kan skapa temporära filer under startsessionen.

```
ERROR:IOError: [Errno 28] No space left on device: '/var/lib/waagent/events/1474306860983232.tmp' 
    
OSError: [Errno 28] No space left on device: '/var/lib/cloud/data/tmpDZCq0g'
```
    
## <a name="cause"></a>Orsak

Det finns flera orsaker till att det här fel meddelandet kan inträffa:

1. Disken kan vara full.
1. Fil systemet kan ha slut på noder i procent.
1. En datadisk kan monteras över en befintlig katalog som gör att filerna är dolda.
1. Filer som öppnas av en process och sedan tas bort.

## <a name="solution"></a>Lösning

### <a name="process-overview"></a>Process översikt

1. Skapa och få åtkomst till en reparations-VM.
1. Ledigt utrymme på disken.
1. Återskapa den virtuella datorn.

> [!NOTE]
> Gäst operativ systemet fungerar inte när det här felet påträffas. Felsök det här problemet i offline-läge för att lösa det här problemet.

### <a name="create-and-access-a-repair-vm"></a>Skapa och få åtkomst till en virtuell reparations dator

1. Använd steg 1-3 i [reparations kommandona för virtuella datorer](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md) för att förbereda en reparations-VM.
1. Med SSH ansluter du till den virtuella reparations datorn.

### <a name="free-up-space-on-the-disk"></a>Frigör utrymme på disken

Så här löser du problemet:

- Ändra storlek på disken upp till 1 TB om den inte redan har en maximal storlek på 1 TB.
- Frigör disk utrymme.

1. Kontrol lera att disken är full. Om disk storleken är under 1 TB expanderar du upp till maximalt 1 TB [med Azure CLI](../linux/expand-disks.md).
1. Om disken redan är 1 TB måste du frigöra disk utrymme.
1. När du har ändrat storlek och rensat klart kan du fortsätta med att återskapa den virtuella datorn.

### <a name="rebuild-the-vm"></a>Återskapa den virtuella datorn

Använd [steg 5 i reparations kommandona för virtuella datorer](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) för att återskapa den virtuella datorn.

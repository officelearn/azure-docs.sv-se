---
title: Behåll filer för Bash i Azure Cloud Shell | Microsoft Docs
description: Genomgång av hur Bash i Azure Cloud Shell kvarstår filer.
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: juluk
ms.openlocfilehash: 606b36be4a2bbeff8dd226f41341d60e23f0d988
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163285"
---
[!INCLUDE [PersistingStorage-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-storage-works"></a>Så här fungerar Cloud Shell-lagring 
Cloudshell kvarstår filerna med båda av följande metoder: 
* Skapa en avbildning av din `$Home` directory för att bevara allt innehåll i katalogen. Disk image sparas i angivna filresursen som `acc_<User>.img` på `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`, och synkroniserar automatiskt ändringar. 
* Montera angivna filresursen som `clouddrive` i din `$Home` katalogen för direkta filresurs interaktion. `/Home/<User>/clouddrive` mappas till `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Alla filer i din `$Home` katalog, t.ex SSH-nycklar finns kvar i din användaravbildning för disk som är lagrat i monterade filresursen. Gäller bästa praxis när du bevara informationen i din `$Home` directory och monterade filresurs.

## <a name="bash-specific-commands"></a>Bash-fil

### <a name="use-the-clouddrive-command"></a>Använd den `clouddrive` kommando
Med Bash i Cloud Shell, kan du köra ett kommando som kallas `clouddrive`, vilket gör att du manuellt uppdatera filresursen som är kopplad till Cloud Shell.
![Kör kommandot ”clouddrive”](media/persisting-shell-storage/clouddrive-h.png)

### <a name="mount-a-new-clouddrive"></a>Montera en ny clouddrive

#### <a name="prerequisites-for-manual-mounting"></a>Krav för manuell montering
Du kan uppdatera filresursen som är associerat med Cloud Shell med hjälp av den `clouddrive mount` kommando.

Om du monterar en befintlig resurs måste storage-konton finnas i din väljer Cloud Shell-region. Hämta platsen genom att köra `env` från Bash och kontrollerar de `ACC_LOCATION`.

#### <a name="the-clouddrive-mount-command"></a>Den `clouddrive mount` kommando

> [!NOTE]
> Om du montera en ny filresurs, skapas en ny användaravbildning för din `$Home` directory. Den föregående `$Home` avbildningen sparas i föregående filresursen.

Kör den `clouddrive mount` kommandot med följande parametrar:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Om du vill visa mer information om kör `clouddrive mount -h`enligt nedan:

![Kör den ' clouddrive mount'command](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Demontera clouddrive
Du kan demontera en filresurs som är kopplad till Cloud Shell när som helst. Eftersom Cloud Shell kräver en monterad filresurs som ska användas, uppmanas du att skapa och montera en annan filresurs i nästa session.

1. Kör `clouddrive unmount`.
2. Bekräfta och bekräfta att anvisningarna.

Filresursen fortsätter att existera såvida inte du ta bort den manuellt. Cloudshell efter inte längre den här filresursen på framtida sessioner. Om du vill visa mer information om kör `clouddrive unmount -h`enligt nedan:

![Kör den ' clouddrive unmount'command](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Även om inte tas bort några resurser genom att köra följande kommando, ta bort en resursgrupp, en storage-konto eller en filresurs som är mappad till Cloud Shell manuellt raderar din `$Home` directory diskavbildning och alla filer i filresursen. Det går inte att ångra den här åtgärden.

### <a name="list-clouddrive"></a>Lista `clouddrive`
Identifiera vilka filresursen monteras som `clouddrive`, kör den `df` kommando. 

Sökvägen till clouddrive visar ditt lagringskontonamn och filresurs i URL: en. Till exempel, `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```
## <a name="powershell-specific-commands"></a>PowerShell-fil

### <a name="list-clouddrive-azure-file-shares"></a>Lista `clouddrive` Azure-filresurser
Den `Get-CloudDrive` cmdlet: en hämtar den Azure information om filresurs monterad genom den `clouddrive` i Cloud Shell. <br>
![Kör Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Demontera `clouddrive`
Du kan demontera en Azure-filresurs som är kopplad till Cloud Shell när som helst. Om Azure-filresursen har tagits bort, uppmanas du att skapa och montera en ny Azure-filresurs vid nästa session.

Den `Dismount-CloudDrive` cmdlet demonteras en Azure-filresurs från det aktuella storage-kontot. Demontera den `clouddrive` avslutas den aktuella sessionen. Användaren uppmanas att skapa och montera en ny Azure-filresurs under nästa session.
![Kör Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Nästa steg
[Bash i Cloud Shell-Snabbstart](quickstart.md) <br>
[PowerShell i Cloud Shell-Snabbstart](quickstart-powershell.md) <br>
[Lär dig mer om Microsoft Azure File storage](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Lär dig mer om storage taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>

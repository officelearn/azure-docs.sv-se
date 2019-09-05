---
title: Spara filer i Azure Cloud Shell | Microsoft Docs
description: Genom gång av hur Azure Cloud Shell sparar filer.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: damaerte
ms.openlocfilehash: b2823c935d11ae99ab1d87ae708945721820ad8c
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306738"
---
[!INCLUDE [PersistingStorage-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-storage-works"></a>Så här fungerar Cloud Shell Storage 
Cloud Shell sparar filer genom båda följande metoder: 
* Skapa en disk avbildning av `$Home` katalogen för att spara allt innehåll i katalogen. Disk avbildningen sparas i den angivna fil resursen som `acc_<User>.img` på `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`och synkroniserar automatiskt ändringar. 
* Montera din angivna fil resurs som `clouddrive` i din `$Home` katalog för direkt interaktion av fil resurser. `/Home/<User>/clouddrive`är mappad `fileshare.storage.windows.net/fileshare`till.
 
> [!NOTE]
> Alla filer i `$Home` katalogen, till exempel SSH-nycklar, finns kvar i din användar disk avbildning, som lagras i den monterade fil resursen. Använd bästa praxis när du bevarar information `$Home` i din katalog och monterade fil resursen.

## <a name="clouddrive-commands"></a>clouddrive-kommandon

### <a name="use-the-clouddrive-command"></a>`clouddrive` Använda kommandot
I Cloud Shell kan du köra ett kommando som kallas `clouddrive`, vilket gör att du kan uppdatera fil resursen som är monterad till Cloud Shell manuellt.
![Köra kommandot "clouddrive"](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Lista`clouddrive`
För att identifiera vilken fil resurs som monteras som `clouddrive`kör du `df` kommandot. 

Fil Sök vägen till clouddrive visar ditt lagrings konto namn och fil resurs i URL: en. Till exempel, `//storageaccountname.file.core.windows.net/filesharename`

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

### <a name="mount-a-new-clouddrive"></a>Montera en ny clouddrive

#### <a name="prerequisites-for-manual-mounting"></a>Krav för manuell montering
Du kan uppdatera fil resursen som är kopplad till Cloud Shell med hjälp `clouddrive mount` av kommandot.

Om du monterar en befintlig fil resurs måste lagrings kontona finnas i din SELECT Cloud Shell-region. Hämta platsen genom att köra `env` och `ACC_LOCATION`kontrol lera.

#### <a name="the-clouddrive-mount-command"></a>`clouddrive mount` Kommandot

> [!NOTE]
> Om du monterar en ny fil resurs skapas en ny användar avbildning för din `$Home` katalog. Din föregående `$Home` bild behålls i din tidigare fil resurs.

`clouddrive mount` Kör kommandot med följande parametrar:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Om du vill visa mer information `clouddrive mount -h`, kör, som du ser här:

![Kör clouddrive-mount'command](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Demontera clouddrive
Du kan demontera en fil resurs som är monterad till Cloud Shell när som helst. Eftersom Cloud Shell kräver att en monterad fil resurs används uppmanas du att skapa och montera en annan fil resurs i nästa session.

1. Kör `clouddrive unmount`.
2. Bekräfta och bekräfta prompter.

Fil resursen kommer att finnas kvar om du inte tar bort den manuellt. Cloud Shell kommer inte längre att söka efter den här fil resursen på efterföljande sessioner. Om du vill visa mer information `clouddrive unmount -h`, kör, som du ser här:

![Kör clouddrive-unmount'command](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Även om du kör det här kommandot tar du `$Home` inte bort några resurser, manuellt tar bort en resurs grupp, ett lagrings konto eller en fil resurs som är mappad till Cloud Shell raderar katalog disk avbildningen och eventuella filer i fil resursen. Det går inte att utföra den här åtgärden.
## <a name="powershell-specific-commands"></a>PowerShell-/regionsspecifika kommandon

### <a name="list-clouddrive-azure-file-shares"></a>Visa `clouddrive` en lista över Azure-filresurser
Cmdleten hämtar information om Azure-filresurser som för närvarande monteras `clouddrive` av i Cloud Shell. `Get-CloudDrive` <br>
![Kör get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Demontera`clouddrive`
Du kan demontera en Azure-filresurs som är monterad till Cloud Shell när som helst. Om Azure-filresursen har tagits bort uppmanas du att skapa och montera en ny Azure-fil resurs vid nästa session.

`Dismount-CloudDrive` Cmdleten demonterar en Azure-filresurs från det aktuella lagrings kontot. Demonteringen av `clouddrive` avslutar den aktuella sessionen. Användaren uppmanas att skapa och montera en ny Azure-fil resurs under nästa session.
![Köra dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Obs! Om du behöver definiera en funktion i en fil och anropa den från PowerShell-cmdletarna måste punkt operatorn inkluderas. Till exempel:. .\MyFunctions.ps1

## <a name="next-steps"></a>Nästa steg
[Cloud Shell snabb start](quickstart.md) <br>
[Läs mer om lagring av Microsoft Azure filer](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[Läs mer om lagrings märken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>

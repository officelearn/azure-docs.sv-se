---
title: "Spara filer för Bash i Azure Cloud Shell | Microsoft Docs"
description: "Genomgång av hur Bash i Azure Cloud Shell kvarstår filer."
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: juluk
ms.openlocfilehash: d8188634846a7ce75b5294cb3012069d9eafafc1
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-bash-in-cloud-shell-storage-works"></a>Så här fungerar Bash i molnet Shell lagring 
Bash i molnet Shell kvarstår filerna med båda av följande metoder: 
* Skapa en avbildning av din `$Home` directory att bevara allt innehåll i katalogen. Disk image sparas i angivna filresursen som `acc_<User>.img` på `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`, och den automatiskt synkroniserar ändringar. 
* Montera filresursen angivna som `clouddrive` i din `$Home` katalogen för filresursen direkt interaktion. `/Home/<User>/clouddrive`mappas till `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Alla filer i din `$Home` katalog, t.ex SSH-nycklar finns kvar i din användare diskavbildning som lagras i monterade filresursen. Tillämpa metodtips när du bevara informationen i dina `$Home` directory och monterade filresurs.

## <a name="use-the-clouddrive-command"></a>Använd den `clouddrive` kommando
Med Bash i molnet Shell kan du köra ett kommando som kallas `clouddrive`, vilket gör att du manuellt uppdatera filresursen som är kopplad till molnet Shell.
![Kör kommandot ”clouddrive”](media/persisting-shell-storage/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>Montera en ny clouddrive

### <a name="prerequisites-for-manual-mounting"></a>Krav för manuell montering
Du kan uppdatera filresursen som är kopplad till molnet Shell med hjälp av den `clouddrive mount` kommando.

Om du monterar en befintlig resurs måste storage-konton vara:
* Lokalt redundant lagring eller geo-redundant lagring som stöd för filresurser.
* Finns i din tilldelade region. När du är onboarding den region som du har tilldelats visas i resursgruppens namn `cloud-shell-storage-<region>`.

### <a name="the-clouddrive-mount-command"></a>Kommandot clouddrive monteringspunkt

> [!NOTE]
> Om du montera en ny filresurs, skapas en ny användaravbildning för din `$Home` directory. Den föregående `$Home` avbildningen sparas i din tidigare filresurs.

Kör den `clouddrive mount` kommandot med följande parametrar:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Om du vill visa mer information, kör `clouddrive mount -h`, som visas här:

![Kör den ' clouddrive mount'command](media/persisting-shell-storage/mount-h.png)

## <a name="unmount-clouddrive"></a>Demontera clouddrive
Du kan montera en filresurs som är kopplad till molnet Shell när som helst. Eftersom molnet Shell kräver en monterad filresurs som ska användas, uppmanas du att skapa och montera en annan filresurs på Nästa.

1. Kör `clouddrive unmount`.
2. Bekräfta och bekräfta anvisningarna.

Filresursen fortsätter att finns om du tar bort den manuellt. Molnet Shell efter inte längre filresursen på efterföljande sessioner. Om du vill visa mer information, kör `clouddrive unmount -h`, som visas här:

![Kör den ' clouddrive unmount'command](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Även om kör detta kommando inte raderar några resurser, ta bort en resursgrupp, storage-konto eller filresurs som har mappats till molnet Shell manuellt raderar din `$Home` directory diskavbildning och alla filer i filresursen. Det går inte att ångra åtgärden.

## <a name="list-clouddrive"></a>Lista`clouddrive`
Identifiera vilka filresursen är monterad som `clouddrive`kör den `df` kommando. 

Sökvägen till clouddrive visar dina lagringskontonamnet och filresurs på URL: en. Till exempel, `//storageaccountname.file.core.windows.net/filesharename`

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

[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Nästa steg
[Bash i molnet Shell Snabbstart](quickstart.md) <br>
[Lär dig mer om Microsoft Azure File storage](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Lär dig mer om lagring taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>

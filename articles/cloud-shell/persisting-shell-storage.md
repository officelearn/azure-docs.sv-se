---
title: Beständiga filer i Azure Cloud Shell | Microsoft-dokument
description: Genomgång av hur Azure Cloud Shell beständiga filer.
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
ms.date: 02/24/2020
ms.author: damaerte
ms.openlocfilehash: d4d59642f638e7b1221c35a4bb281923571d5066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297590"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Beständiga filer i Azure Cloud Shell
Cloud Shell använder Azure File Storage för att spara filer över sessioner. Vid den första starten uppmanar Cloud Shell dig att associera en ny eller befintlig filresurs för att spara filer över sessioner.

> [!NOTE]
> Bash och PowerShell delar samma filresurs. Endast en filresurs kan associeras med automatisk montering i Cloud Shell.

> [!NOTE]
> Azure-lagringsbrandvägg stöds inte för molnskallagringskonton.

## <a name="create-new-storage"></a>Skapa ny lagring

När du använder grundläggande inställningar och bara väljer en prenumeration skapar Cloud Shell tre resurser för din räkning i den region som stöds som är närmast dig:
* Resursgrupp: `cloud-shell-storage-<region>`
* Lagringskonto:`cs<uniqueGuid>`
* Filresurs:`cs-<user>-<domain>-com-<uniqueGuid>`

![Inställningen Prenumeration](media/persisting-shell-storage/basic-storage.png)

Filresursen monteras `clouddrive` som `$Home` i katalogen. Detta är en engångsåtgärd och filresursen monteras automatiskt i efterföljande sessioner. 

Filresursen innehåller också en 5 GB-avbildning som skapas för `$Home` dig som automatiskt sparar data i katalogen. Detta gäller både Bash och PowerShell.

## <a name="use-existing-resources"></a>Använda befintliga resurser

Med det avancerade alternativet kan du associera befintliga resurser. När du väljer en Cloud Shell-region måste du välja ett stödlagringskonto som finns i samma region. Om din tilldelade region till exempel är västra USA måste du associera en filresurs som finns även i västra USA.

När frågan om lagringsinställningar visas väljer du **Visa avancerade inställningar** för att visa ytterligare alternativ. Filtret ifyllt lagringsalternativ för lokalt redundant lagring (LRS), geo-redundant lagring (GRS) och zonundant lagringskonton (ZRS). 

> [!NOTE]
> Användning av GRS- eller ZRS-lagringskonton rekommenderas för ytterligare återhämtning för din säkerhetskopieringsfilresurs. Vilken typ av redundans beror på dina mål och prisinställningar. [Läs mer om replikeringsalternativ för Azure Storage-konton](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

![Inställningen resursgrupp](media/persisting-shell-storage/advanced-storage.png)

## <a name="securing-storage-access"></a>Skydda åtkomst till lagring
Av säkerhetsskäl bör varje användare etablera sitt eget lagringskonto.  För rollbaserad åtkomstkontroll (RBAC) måste användare ha deltagaråtkomst eller högre på lagringskontonivå.

Cloud Shell använder en Azure File Share i ett lagringskonto, inuti en angiven prenumeration. På grund av ärvda behörigheter kan användare med tillräcklig åtkomst till prenumerationen komma åt alla lagringskonton och filresurser som finns i prenumerationen.

Användare bör låsa åtkomsten till sina filer genom att ange behörigheterna på lagringskontot eller prenumerationsnivån.

## <a name="supported-storage-regions"></a>Lagringsområden som stöds
För att hitta din `env` nuvarande region kan `ACC_LOCATION`du köra i `$env:ACC_LOCATION`Bash och leta reda på variabeln eller från PowerShell-körningen . Filresurser får en 5 GB-avbildning `$Home` som skapats för att du ska kunna spara din katalog.

Molnskalsdatorer finns i följande regioner:

|Område|Region|
|---|---|
|Nord- och Sydamerika|Östra USA, Södra centrala USA, västra USA|
|Europa|Europa, norra, Europa, västra|
|Asien och stillahavsområdet|Centrala Indien, Sydostasien|

Kunder bör välja en primär region, såvida de inte har ett krav på att deras data i vila ska lagras i en viss region. Om de har ett sådant krav bör en sekundär lagringsregion användas.

### <a name="secondary-storage-regions"></a>Sekundära lagringsområden
Om en sekundär lagringsregion används finns det associerade Azure-lagringskontot i en annan region som Cloud Shell-datorn som du monterar dem på. Jane kan till exempel ange att hennes lagringskonto ska placeras i Östra Kanada, en sekundär region, men den maskin hon är monterad på finns fortfarande i en primär region. Hennes data i vila ligger i Kanada, men det behandlas i USA.

> [!NOTE]
> Om en sekundär region används kan filåtkomst och starttid för Cloud Shell vara långsammare.

En användare `(Get-CloudDrive | Get-AzStorageAccount).Location` kan köras i PowerShell för att se var fildelningen finns.

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Begränsa skapandet av resurser med en Azure-resursprincip
Lagringskonton som du skapar i `ms-resource-usage:azure-cloud-shell`Cloud Shell taggas med . Om du inte vill tillåta användare från att skapa lagringskonton i Cloud Shell skapar du en [Azure-resursprincip för taggar](../azure-policy/json-samples.md) som utlöses av den här specifika taggen.

## <a name="how-cloud-shell-storage-works"></a>Så här fungerar Cloud Shell-lagring 
Cloud Shell beständiga filer via båda följande metoder: 
* Skapa en diskavbildning av `$Home` katalogen för att spara allt innehåll i katalogen. Diskavbildningen sparas i den `acc_<User>.img` angivna `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`filresursen som på och synkroniseras automatiskt ändringar. 
* Montera den angivna filresursen som `clouddrive` i `$Home` katalogen för direkt fildelningsinteraktion. `/Home/<User>/clouddrive`mappas `fileshare.storage.windows.net/fileshare`till .
 
> [!NOTE]
> Alla filer `$Home` i katalogen, till exempel SSH-nycklar, sparas i användardiskavbildningen, som lagras i den monterade filresursen. Använd metodtips när du `$Home` beständiga information i katalogen och den monterade filresursen.

## <a name="clouddrive-commands"></a>clouddrive-kommandon

### <a name="use-the-clouddrive-command"></a>Använda `clouddrive` kommandot
I Cloud Shell kan du `clouddrive`köra ett kommando som heter , som gör att du kan uppdatera filresursen manuellt som är monterad på Cloud Shell.

![Köra kommandot "clouddrive"](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Lista`clouddrive`
Om du vill ta reda `clouddrive`på `df` vilken filresurs som är monterad som kör du kommandot. 

Filsökvägen till clouddrive visar ditt lagringskontonamn och filresurs i URL:en. Till exempel, `//storageaccountname.file.core.windows.net/filesharename`

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

#### <a name="prerequisites-for-manual-mounting"></a>Förutsättningar för manuell montering
Du kan uppdatera filresursen som är associerad `clouddrive mount` med Cloud Shell med kommandot.

Om du monterar en befintlig filresurs måste lagringskontona finnas i din valda Cloud Shell-region. Hämta platsen genom `env` att `ACC_LOCATION`köra och kontrollera .

#### <a name="the-clouddrive-mount-command"></a>Kommandot `clouddrive mount`

> [!NOTE]
> Om du monterar en ny filresurs skapas en `$Home` ny användaravbildning för katalogen. Den `$Home` tidigare bilden sparas i den tidigare filresursen.

Kör `clouddrive mount` kommandot med följande parametrar:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Om du vill `clouddrive mount -h`visa mer information kör du , som du ser här:

![Köra kommandot clouddrive mount](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Avmontera clouddrive
Du kan när som helst avmontera en filresurs som är monterad på Cloud Shell. Eftersom Cloud Shell kräver att en monterad filresurs används uppmanas du att skapa och montera en annan filresurs på nästa session.

1. Kör `clouddrive unmount`.
2. Bekräfta och bekräfta uppmaningar.

Filresursen fortsätter att finnas om du inte tar bort den manuellt. Cloud Shell söker inte längre efter den här filresursen på efterföljande sessioner. Om du vill `clouddrive unmount -h`visa mer information kör du , som du ser här:

![Köra kommandot clouddrive avmontera](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Även om du kör det här kommandot kommer inte att ta bort några resurser, raderar manuellt `$Home` en resursgrupp, ett lagringskonto eller en filresurs som är mappad till Cloud Shell, din katalogdiskavbildning och alla filer i filresursresursen. Det går inte att ångra den här åtgärden.
## <a name="powershell-specific-commands"></a>PowerShell-specifika kommandon

### <a name="list-clouddrive-azure-file-shares"></a>Lista `clouddrive` Azure-filresurser
Cmdlet `Get-CloudDrive` hämtar Azure-filresursinformationen `clouddrive` som för närvarande är monterad av i Cloud Shell. <br>
![Köra Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Avmontera`clouddrive`
Du kan när som helst avmontera en Azure-filresurs som är monterad på Cloud Shell. Om Azure-filresursen har tagits bort uppmanas du att skapa och montera en ny Azure-filresurs vid nästa session.

Cmdleten `Dismount-CloudDrive` avmonterar en Azure-filresurs från det aktuella lagringskontot. Demontering av `clouddrive` avslutningar den aktuella sessionen. Användaren uppmanas att skapa och montera en ny Azure-filresurs under nästa session.
![Köra Demount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Om du behöver definiera en funktion i en fil och anropa den från PowerShell-cmdlets måste punktoperatorn inkluderas. Till exempel: . .\Mina funktioner.ps1

## <a name="next-steps"></a>Nästa steg
[Snabbstart i Molnskal](quickstart.md) <br>
[Lär dig mer om lagring av Microsoft Azure-filer](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[Läs mer om lagringstaggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>

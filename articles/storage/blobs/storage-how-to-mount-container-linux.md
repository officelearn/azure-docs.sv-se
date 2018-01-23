---
title: Hur man monterar Azure Blob storage som ett filsystem i Linux | Microsoft Docs
description: "Montera en Azure Blob storage-behållare med SÄKRAD på Linux"
services: storage
documentationcenter: linux
author: seguler
manager: jahogg
ms.service: storage
ms.devlang: bash
ms.topic: article
ms.date: 01/19/2018
ms.author: seguler
ms.openlocfilehash: 299b96c783fb3606347bb448d00d44f0071da429
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse-preview"></a>Hur man monterar Blob storage som ett filsystem med blobfuse (förhandsgranskning)

## <a name="overview"></a>Översikt
[Blobfuse](https://github.com/Azure/azure-storage-fuse) är en virtuell-drivrutin för Azure Blob Storage, där du kan komma åt dina befintliga block-blob-data i ditt lagringskonto via filsystemet Linux. Azure Blob Storage är en tjänst för lagring av objekt och därför inte ett hierarkiskt namnområde. Blobfuse innehåller det här namnområdet med virtuell katalog med hjälp av snedstreck (/) som avgränsare.  

Den här guiden visar hur du använder blobfuse och montera en behållare för Blob storage på Linux- och komma åt data. Mer information om blobfuse läsa informationen i [blobfuse databasen](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse garanterar inte 100% POSIX kompatibilitet som det innebär helt enkelt begäranden till [Blob REST API: er](https://docs.microsoft.com/en-us/rest/api/storageservices/blob-service-rest-api). Byt namn på åtgärder är t.ex, atomisk i POSIX, men inte i blobfuse.
> En fullständig lista över skillnaderna mellan ett filsystem och blobfuse finns [blobfuse kod källdatabasen](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Installera blobfuse på Linux
Blobfuse binärfiler är tillgängliga på [databaser för Microsoft-programvara för Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software). Konfigurera en av dessa databaser för att kunna installera blobfuse.

### <a name="configure-the-microsoft-package-repository"></a>Konfigurera Microsoft paketdatabasen
Konfigurera den [Linux Paketdatabasen för Microsoft-produkter](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Ett exempel på en Enterprise Linux 6-distributionsplats:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

På liknande sätt kan ändra webbadressen till `.../rhel/7/...` att peka till en Enterprise Linux 7-distribution.

Ett annat exempel på en Ubuntu 14.04:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

På liknande sätt kan ändra webbadressen till `.../ubuntu/16.04/...` att peka till en distributionsplats Ubuntu 16.04.

### <a name="install-blobfuse"></a>Installera blobfuse

På en Ubuntu/Debian-distributionsplats:
```bash
sudo apt-get install blobfuse
```

På en Enterprise Linux-distributionsplats:
```bash
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Förbereda för montering
Blobfuse kräver en tillfällig sökväg i filsystemet för att buffra och cachelagra alla öppna filer, vilket hjälper ger intern-liknande prestanda. Välj de flesta performant disken för den här tillfälliga sökvägen eller Använd en ramdisk för bästa prestanda. 

> [!NOTE]
> Blobfuse lagrar alla öppna innehållet i den temporära sökvägen. Se till att ha tillräckligt med utrymme för att kunna hantera alla öppna filer. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Valfritt) Använd en ramdisk för den temporära sökvägen
I följande exempel skapas en ramdisk 16 GB samt att skapa en katalog för blobfuse. Välj storleken på baserat på dina behov. Den här ramdisk tillåter blobfuse att öppna filer upp till 16 GB i storlek. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-for-temporary-path"></a>Använd en SSD för temporär sökväg
I Azure, kan du använda de tillfälliga diskarna (SSD) som är tillgängliga på din virtuella dator för att tillhandahålla en buffert med låg latens för blobfuse. I Ubuntu distributioner tillfälliga disken är monterad på ' / mnt' medan den är monterad på ' / mnt/resurs /' i RedHat och CentOS-distributioner.

Se till att dina användare har åtkomst till den temporära sökvägen:
```bash
sudo mkdir /mnt/resource/blobfusetmp
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Konfigurera autentiseringsuppgifterna för ditt Lagringskonto
Blobfuse kräver dina autentiseringsuppgifter som ska lagras i en textfil i följande format: 

```
accountName myaccount
accountKey myaccesskey==
containerName mycontainer
```

När du har skapat den här filen, se till att begränsa åtkomst så att andra användare kan läsa den.
```bash
chmod 700 fuse_connection.cfg
```

### <a name="create-an-empty-directory-for-mounting"></a>Skapa en tom katalog för montering
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Montera

> [!NOTE]
> En fullständig lista över monteringsalternativ, kontrollera [blobfuse databasen](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Kör följande kommando för att montera blobfuse med dina användare. Det här kommandot monterar den behållare som angavs i ' / path/to/fuse_connection.cfg' på platsen ' / minbehållare '.

```bash
blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Du bör nu ha åtkomst till din blockblobbar via vanlig filsystemet API: er. Observera att den monterade katalogen bara kan användas av användaren montera, vilket skyddar åtkomsten. Om du vill tillåta åtkomst till alla användare kan du montera via alternativet ```-o allow_other```. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Nästa steg

* [Blobfuse startsida](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Rapporten blobfuse problem](https://github.com/Azure/azure-storage-fuse/issues) 


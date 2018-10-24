---
title: Hur du monterar en Azure Blob storage som ett filsystem i Linux | Microsoft Docs
description: Montera en Azure Blob storage-behållare med FUSE på Linux
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 10/11/2018
ms.author: seguler
ms.openlocfilehash: 50378fd7739567b0cc56066168ddd33c3ea14141
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957062"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Hur du montera Blob storage som ett filsystem med blobfuse

## <a name="overview"></a>Översikt
[Blobfuse](https://github.com/Azure/azure-storage-fuse) är en virtuell filsystemsdrivrutin för Azure Blob Storage, vilket ger dig tillgång till dina befintliga block blob-data i ditt Storage-konto via filsystemet Linux. Azure Blob Storage är en tjänst för lagring av objekt och därför inte ett hierarkiskt namnområde. Blobfuse innehåller det här namnområdet med virtuell katalog med hjälp av snedstreck (/) som avgränsare.  

Den här guiden visar hur du använder blobfuse och montera Blob storage-behållare på Linux och komma åt data. Om du vill veta mer om blobfuse kan du läsa informationen i [blobfuse databasen](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse garanterar inte 100% POSIX-efterlevnad som den helt enkelt översätter begäranden till [Blob REST API: er](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api). Exempel: Byt namn på är atomiska i POSIX, men inte i blobfuse.
> En fullständig lista över skillnaderna mellan ett filsystem och blobfuse finns [lagringsplatsen för källkod blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Installera blobfuse på Linux
Blobfuse binärfiler finns på [lagringsplatser för Microsoft-programvara för Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) för distributioner som Ubuntu och RHEL. Konfigurera en databaser i listan för att kunna installera blobfuse på dessa distributioner. Du kan också skapa binärfiler från källkoden följa installationsstegen [här](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) om det finns inga binärfiler för din distribution.

### <a name="configure-the-microsoft-package-repository"></a>Konfigurera Microsoft-paketdatabasen
Konfigurera den [Linux Paketdatabasen för Microsoft-produkter](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Exempel på en Enterprise Linux 6-distribution:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

På samma sätt kan ändra webbadressen till `.../rhel/7/...` så att den pekar till en Enterprise Linux 7-distribution.

Ett annat exempel på en Ubuntu 14.04:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

På samma sätt kan ändra webbadressen till `.../ubuntu/16.04/...` att den pekar på en distribution i Ubuntu 16.04.

### <a name="install-blobfuse"></a>Installera blobfuse

På en Ubuntu/Debian-distribution:
```bash
sudo apt-get install blobfuse
```

På en Enterprise Linux-distribution:
```bash
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Förbereda för montering
Blobfuse kräver en tillfällig sökväg i filsystemet att buffra och cachelagra alla öppna filer, som hjälper dig att ger inbyggd-liknande prestanda. Välj de flesta högpresterande disken för den här tillfälliga sökvägen eller Använd en ramdisk för bästa prestanda. 

> [!NOTE]
> Blobfuse lagrar alla öppna filens innehåll i den tillfälliga sökvägen. Se till att du har tillräckligt med utrymme för att kunna hantera alla öppna filer. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Valfritt) Använda en ramdisk för den tillfälliga sökvägen
I följande exempel skapas en ramdisk av 16 GB samt skapa en katalog för blobfuse. Välj storleken utifrån dina behov. Den här ramdisk tillåter blobfuse att öppna filer upp till 16 GB i storlek. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-for-temporary-path"></a>Använda SSD-disk för tillfällig sökväg
I Azure, kan du använda de differentierande diskarna (SSD) som är tillgängliga på dina virtuella datorer för att tillhandahålla en buffert med låg latens för blobfuse. I Ubuntu-distributioner kan den här tillfälliga disken är monterad på ' / mnt' medan den är monterad på ' / mnt/resource / ”i Red Hat och CentOS-distributioner.

Kontrollera att användaren har åtkomst till den tillfälliga sökvägen:
```bash
sudo mkdir /mnt/resource/blobfusetmp
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Konfigurera autentiseringsuppgifterna för ditt Lagringskonto
Blobfuse kräver dina autentiseringsuppgifter som ska lagras i en textfil i följande format: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```

När du har skapat den här filen kan du se till att begränsa åtkomst så att ingen annan användare kan läsa den.
```bash
chmod 700 fuse_connection.cfg
```

> [!NOTE]
> Om du har skapat konfigurationsfilen på Windows, se till att köra `dos2unix` att rensa och konvertera till Unix-format. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Skapa en tom katalog för montering
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Montera

> [!NOTE]
> En fullständig lista över alternativ för montering, kontrollera [blobfuse databasen](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Kör följande kommando för att montera blobfuse med dina användare. Det här kommandot monterar den behållare som angavs i ' / path/to/fuse_connection.cfg ”till platsen” / mycontainer ”.

```bash
blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Du bör nu ha åtkomst till dina blockblobar via vanlig filsystemet API: er. Observera att den monterade katalogen bara kan användas av användaren montera den, vilket skyddar åtkomsten. Om du vill tillåta åtkomst till alla användare, kan du montera via alternativet ```-o allow_other```. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Nästa steg

* [Startsida för Blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Rapportera blobfuse problem](https://github.com/Azure/azure-storage-fuse/issues) 


---
title: Så här monterar du Azure Blob-lagring som ett filsystem på Linux | Microsoft-dokument
description: Montera en Azure Blob-lagringsbehållare med FUSE på Linux
author: rishabpoh
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 2/1/2019
ms.author: ripohane
ms.reviewer: dineshm
ms.openlocfilehash: a0a03df59bc6ecffcb4f0a701616297f2da78fdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061433"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Så här monterar du Blob-lagring som filsystem med blobfuse

## <a name="overview"></a>Översikt
[Blobfuse](https://github.com/Azure/azure-storage-fuse) är en virtuell filsystemsdrivrutin för Azure-blobblagring. Blobfuse kan du komma åt dina befintliga block blob data i ditt lagringskonto via Linux-filsystemet. Blobfuse använder det virtuella katalogschemat med snedstrecket framåt som avgränsare.  

Den här guiden visar hur du använder blobfuse och monterar en Blob-lagringsbehållare på Linux och åtkomstdata. Om du vill veta mer om blobfuse, läs detaljerna i [blobfuse arkivet](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse garanterar inte 100% POSIX-efterlevnad eftersom det helt enkelt översätter begäranden till [Blob REST API:er](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api). Byt namn på åtgärder är till exempel atomära i POSIX, men inte i blobfuse.
> En fullständig lista över skillnader mellan ett inbyggt filsystem och blus finns [i blobfuse-källkodsarkivet](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Installera blobfuse på Linux
Blobfuse binärfiler finns på [Microsofts programvarudatabaser för Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) för Ubuntu och RHEL distributioner. Om du vill installera blobfuse på dessa distributioner konfigurerar du ett av databaserna från listan. Du kan också skapa binärfiler från källkoden enligt [installationsstegen för Azure Storage](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) om det inte finns några binärfiler tillgängliga för distributionen.

Blobfuse stöder installation på Ubuntu 14.04, 16.04 och 18.04. Kör det här kommandot för att se till att du har en av dessa versioner distribuerad:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>Konfigurera Microsoft-paketdatabasen
Konfigurera [Linux-paketarkivet för Microsoft-produkter](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Som ett exempel på en Enterprise Linux 6-distribution:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

På samma sätt ändra `.../rhel/7/...` webbadressen så att den pekar på en Enterprise Linux 7-distribution.

Ett annat exempel på en Ubuntu 14,04 distribution:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

På samma sätt ändrar du URL:en till `.../ubuntu/16.04/...` eller `.../ubuntu/18.04/...` refererar till en annan Ubuntu-version.

### <a name="install-blobfuse"></a>Installera blus

På en Ubuntu/Debiandistribution:
```bash
sudo apt-get install blobfuse
```

På en Enterprise Linux-distribution:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Förbered för montering
Blobfuse ger inbyggt-liknande prestanda genom att kräva en tillfällig sökväg i filsystemet för att buffra och cachelagra alla öppna filer. För den här tillfälliga sökvägen väljer du den mest högpresterande disken eller använder en ramdisk för bästa prestanda. 

> [!NOTE]
> Blobfuse lagrar allt öppet filinnehåll i den tillfälliga sökvägen. Se till att ha tillräckligt med utrymme för att rymma alla öppna filer. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Valfritt) Använda en ramdisk för den tillfälliga sökvägen
I följande exempel skapas en ramdisk på 16 GB och en katalog för blus. Välj storlek baserat på dina behov. Denna ramdisk tillåter blobfuse att öppna filer upp till 16 GB i storlek. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>Använda en SSD som en tillfällig sökväg
I Azure kan du använda de Efemära diskar (SSD) som är tillgängliga på dina virtuella datorer för att tillhandahålla en buffert med låg latens för blobfuse. I Ubuntu-distributioner monteras denna efemära disk på "/mnt". I Red Hat- och CentOS-distributioner monteras disken på "/mnt/resource/".

Kontrollera att användaren har åtkomst till den tillfälliga sökvägen:
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Konfigurera autentiseringsuppgifterna för ditt lagringskonto
Blobfuse kräver att dina autentiseringsuppgifter lagras i en textfil i följande format: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
Det `accountName` är prefixet för ditt lagringskonto - inte den fullständiga webbadressen.

Skapa den här filen med:

```
touch ~/fuse_connection.cfg
```

När du har skapat och redigerat den här filen måste du begränsa åtkomsten så att inga andra användare kan läsa den.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Om du har skapat konfigurationsfilen i `dos2unix` Windows måste du köra för att sanera och konvertera filen till Unix-format. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Skapa en tom katalog för montering
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Montera

> [!NOTE]
> En fullständig lista över monteringsalternativ [finns i blobfuse-arkivet](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Om du vill montera blobfuse kör du följande kommando med användaren. Detta kommando monterar behållaren som anges i "/path/to/fuse_connection.cfg" på platsen "/mycontainer".

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Du bör nu ha åtkomst till dina blockblobar via vanliga api:er för filsystem. Den användare som monterar katalogen är den enda person som kan komma åt den, som standard, vilket skyddar åtkomsten. Om du vill ge åtkomst till alla ```-o allow_other```användare kan du montera via alternativet . 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Nästa steg

* [Blobfuse hemsida](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Rapportera blobfuse frågor](https://github.com/Azure/azure-storage-fuse/issues) 


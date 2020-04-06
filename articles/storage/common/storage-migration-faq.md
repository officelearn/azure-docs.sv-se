---
title: Vanliga frågor och svar om Azure Storage-migrering | Microsoft-dokument
description: Svar på vanliga frågor om att migrera Azure Storage
services: storage
author: genlin
manager: dcscontentpm
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: 39f1137638f9cd4926b712bdd18e681d90adcdc4
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668555"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Vanliga frågor om Azure Storage-migrering

Den här artikeln besvarar vanliga frågor om Azure Storage-migrering.

## <a name="copy-upload-or-download"></a>Kopiera, ladda upp eller ladda ner

**Hur skapar jag ett skript för att kopiera filer från en behållare till en annan?**

Om du vill kopiera filer mellan behållare kan du använda AzCopy. Se följande exempel:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy använder [kopiera blob API](https://docs.microsoft.com/rest/api/storageservices/copy-blob) för att kopiera varje fil i behållaren.  

Du kan använda vilken virtuell dator eller lokal dator som helst som har tillgång till Internet för att köra AzCopy. Du kan också använda ett Azure Batch-schema för att göra detta automatiskt, men det är mer komplicerat.  

Automatiseringsskriptet är utformat för Azure Resource Manager-distribution i stället för datamanipulering av lagringsinnehåll. Mer information finns i [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

**Finns det någon avgift för att kopiera data mellan två filresurser på samma lagringskonto inom samma region?**

Nej. Det finns ingen avgift för denna process.

**Hur laddar jag ned 1–2 TB data från Azure-portalen?**

Använd AzCopy för att hämta data. Mer information finns i [Överföra data med AzCopy på Windows](storage-use-azcopy.md) och Överför data med [AzCopy på Linux](storage-use-azcopy-linux.md).

**Hur kan jag ladda ner en virtuell hårddisk till en lokal dator, annat än genom att använda nedladdningsalternativet i portalen?**

Du kan använda [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att hämta en virtuell hårddisk.

**Hur laddar jag ned data till en Linux-baserad dator från ett Azure-lagringskonto eller laddar upp data från en Linux-dator?**

Du kan använda Azure CLI.

- Ladda ner en enda blob:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Ladda upp en enda blob:

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Hur migrerar jag Blobbar från ett lagringskonto till ett annat?**

 Du kan göra detta med hjälp av vårt [Blob-migreringsskript](../scripts/storage-common-transfer-between-storage-accounts.md).
 
## <a name="migration-or-backup"></a>Migrering eller säkerhetskopiering

**Hur flyttar jag data från en lagringsbehållare till en annan?**

Följ de här stegen:

1.  Skapa behållaren (mappen) i målblobben.

2.  Använd [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) för att kopiera innehållet från den ursprungliga blob-behållaren till en annan blob-behållare.

**Hur skapar jag ett PowerShell-skript för att flytta data från en Azure-filresurs till en annan i Azure Storage?**

Använd AzCopy för att flytta data från en Azure-filresurs till en annan i Azure Storage. Mer information finns i [Överföra data med AzCopy på Windows](storage-use-azcopy.md) och Överför data med [AzCopy på Linux](storage-use-azcopy-linux.md).

**Hur laddar jag upp stora CSV-filer till Azure Storage?**

Använd AzCopy för att överföra stora CSV-filer till Azure Storage. Mer information finns i [Överföra data med AzCopy på Windows](storage-use-azcopy.md) och Överför data med [AzCopy på Linux](storage-use-azcopy-linux.md).

**Jag måste flytta loggarna från enhet D till mitt Azure-lagringskonto varje dag. Hur automatiserar jag detta?**

Du kan använda AzCopy och skapa en aktivitet i Schemaläggaren. Ladda upp filer till ett Azure-lagringskonto med hjälp av ett AzCopy-kommandoskript. Mer information finns i [Så här konfigurerar och kör du startuppgifter för en molntjänst](../../cloud-services/cloud-services-startup-tasks.md).

**Hur flyttar jag mitt lagringskonto mellan prenumerationer?**

Använd AzCopy för att flytta ditt lagringskonto mellan prenumerationer. Mer information finns i [Överföra data med AzCopy på Windows](storage-use-azcopy.md) och Överför data med [AzCopy på Linux](storage-use-azcopy-linux.md).

**Hur kan jag flytta ca 10 TB data till lagring i en annan region?**

Använd AzCopy för att flytta data. Mer information finns i [Överföra data med AzCopy på Windows](storage-use-azcopy.md) och Överför data med [AzCopy på Linux](storage-use-azcopy-linux.md).

**Hur kopierar jag data från lokalt till Azure Storage?**

Använd AzCopy för att kopiera data. Mer information finns i [Överföra data med AzCopy på Windows](storage-use-azcopy.md) och Överför data med [AzCopy på Linux](storage-use-azcopy-linux.md).

**Hur flyttar jag data från lokala filer till Azure Files?**

Använd AzCopy för att flytta data. Mer information finns i [Överföra data med AzCopy på Windows](storage-use-azcopy.md) och Överför data med [AzCopy på Linux](storage-use-azcopy-linux.md).

**Hur flyttar jag hanterade diskar till ett annat lagringskonto?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Följ de här stegen:

1.  Stoppa den virtuella datorn som den hanterade disken är ansluten till.

2.  Kopiera den hanterade hårddiskens virtuella hårddisk från ett område till ett annat genom att köra följande Azure PowerShell-skript:

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Skapa en hanterad disk med hjälp av VHD-filen i en annan region som du kopierade den virtuella hårddisken till. Det gör du genom att köra följande Azure PowerShell-skript:  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ```

Mer information om hur du distribuerar en virtuell dator från en hanterad disk finns i [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Hur flyttar eller hämtar jag data från ett lagringskonto?**

Använd AzCopy för att hämta data. Mer information finns i [Överföra data med AzCopy på Windows](storage-use-azcopy.md) och Överför data med [AzCopy på Linux](storage-use-azcopy-linux.md).

**Hur flyttar jag från ett premiumlagringskonto till ett standardlagringskonto?**

Följ de här stegen:

1.  Skapa ett standardlagringskonto. (Eller använd ett befintligt standardlagringskonto i din prenumeration.)

2.  Ladda ner AzCopy. Kör något av följande AzCopy-kommandon.

    Så här kopierar du hela diskar i lagringskontot:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S

    Om du bara vill kopiera en disk anger du namnet på disken i **Mönster:**

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

Åtgärden kan ta flera timmar att slutföra.

Kontrollera att överföringen har slutförts genom att undersöka behållaren för mållagringskonto i Azure-portalen. När diskarna har kopierats till standardlagringskontot kan du koppla dem till den virtuella datorn som en befintlig disk. Mer information finns i [Så här ansluter du en hanterad datadisk till en virtuell Windows-dator i Azure-portalen](../../virtual-machines/windows/attach-managed-disk-portal.md).  

**Hur flyttar jag från ett klassiskt lagringskonto till ett Azure Resource Manager-lagringskonto?**

Du kan använda cmdleten **Move-AzureStorageAccount.** Den här cmdleten har flera steg (validera, förbereda, genomföra). Du kan validera flytten innan du gör det.

Om du har virtuella datorer måste du vidta ytterligare åtgärder innan du migrerar lagringskontodata. Mer information finns i [Migrera IaaS-resurser från klassiska till Azure Resource Manager med hjälp av Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Hur säkerhetskopierar jag hela mitt lagringskonto till ett annat lagringskonto?**

Det finns inget alternativ för att säkerhetskopiera ett helt lagringskonto direkt. Men du kan manuellt flytta behållaren i det lagringskontot till ett annat konto med hjälp av AzCopy eller Storage Explorer. Följande steg visar hur du använder AzCopy för att flytta behållaren:  

1.  Installera kommandoradsverktyget [AzCopy.](storage-use-azcopy.md) Det här verktyget hjälper dig att flytta VHD-filen mellan lagringskonton.

2.  När du har installerat AzCopy i Windows med installationsprogrammet öppnar du ett kommandotolksfönster och bläddrar sedan till installationsmappen för AzCopy på datorn. Som standard installeras AzCopy på **%ProgramFiles(x86)%\Microsoft SDK\Azure\AzCopy** eller **%ProgramFiles%\Microsoft SDK:er\Azure\AzCopy**.

3.  Kör följande kommando för att flytta behållaren. Du måste ersätta texten med de faktiska värdena.   

            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Ange URI för källlagringskontot (upp till behållaren).  
    - `/Dest`: Ange URI för mållagringskontot (upp till behållaren).  
    - `/SourceKey`: Ange den primära nyckeln för källlagringskontot. Du kan kopiera den här nyckeln från Azure-portalen genom att välja lagringskontot.  
    - `/DestKey`: Ange den primära nyckeln för mållagringskontot. Du kan kopiera den här nyckeln från portalen genom att välja lagringskontot.

När du har kört det här kommandot flyttas behållarfilerna till mållagringskontot.

> [!NOTE]
> AzCopy CLI fungerar inte tillsammans med **mönsterväxeln** när du kopierar från en Azure-blob till en annan.
>
> Du kan kopiera och redigera kommandot AzCopy direkt och dubbelkolla för att se till att **Mönster** matchar källan. Kontrollera också att **/S-jokertecken** är i kraft. Mer information finns i [AzCopy-parametrar](storage-use-azcopy.md).

**Hur säkerhetskopierar jag Azure-fillagring?**

Det finns ingen säkerhetskopieringslösning. Azure-filer stöder dock också asynkron kopia. Så kan du kopiera filer:

- Från en resurs till en annan resurs i ett lagringskonto eller till ett annat lagringskonto.

- Från en resurs till en blob-behållare i ett lagringskonto eller till ett annat lagringskonto.

Mer information finns i [Överföra data med AzCopy i Windows](storage-use-azcopy.md).
## <a name="configuration"></a>Konfiguration

**Hur ändrar jag den sekundära platsen till Europaregionen för ett lagringskonto?**

När du skapar ett lagringskonto väljer du den primära regionen för kontot. Valet av den sekundära regionen baseras på den primära regionen och kan inte ändras. Mer information finns i [Geo-redundant lagring (GRS): Korsregional replikering för Azure Storage](storage-redundancy.md).

**Var kan jag få mer information om Azure Storage Service Encryption (SSE)?**  

Se följande artiklar:

-  [Säkerhetsguide för Azure Storage](../blobs/security-recommendations.md)

-  [Kryptering av Azure Storage Service för data i vila](storage-service-encryption.md)

**Hur krypterar jag data i ett lagringskonto?**

När du har aktiverat kryptering i ett lagringskonto krypteras inte befintliga data. Om du vill kryptera befintliga data måste du ladda upp dem igen till lagringskontot.

Använd AzCopy för att kopiera data till ett annat lagringskonto och sedan flytta tillbaka data. Du kan också använda [kryptering i vila](storage-service-encryption.md).

**Finns det några förutsättningar för att ändra replikeringen av ett lagringskonto från geouppsagbar lagring till lokalt redundant lagring?**

Nej.

**Hur konverterar jag till Azure Premium Storage för en filresurs?**

Premium Storage är inte tillåtet på en Azure-filresurs.

**Hur uppgraderar jag från ett standardlagringskonto till ett premiumlagringskonto? Hur nedgraderar jag från ett premiumlagringskonto till ett standardlagringskonto?**

Du måste skapa mållagringskontot, kopiera data från källkontot till målkontot och sedan ta bort källkontot. Du kan använda ett verktyg som AzCopy för att kopiera data.

Om du har virtuella datorer måste du vidta ytterligare åtgärder innan du migrerar lagringskontodata. Mer information finns i [Migrera till Azure Premium Storage (ohanterat diskar)](storage-migration-to-premium-storage.md).

**Hur kan jag ge andra åtkomst till mina lagringsresurser?**

Så här ger du andra personer åtkomst till lagringsresurserna:

-   Använd en SAS-token (Shared Access Signature) för att ge åtkomst till en resurs.

-   Ge en användare den primära eller sekundära nyckeln för lagringskontot. Mer information finns i [Hantera åtkomstnycklar för lagringskonto](storage-account-keys-manage.md).

-   Ändra åtkomstprincipen så att anonym åtkomst tillåts. Mer information finns i [Bevilja anonyma användare behörigheter till behållare och blobbar](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Var är AzCopy installerat?**

-   Om du öppnar AzCopy från kommandoraden Microsoft Azure Storage skriver du **AzCopy**. Kommandoraden installeras tillsammans med AzCopy.

-   Om du har installerat 32-bitarsversionen finns den här: **%ProgramFiles(x86)%\\Microsoft SDK\\Azure\\AzCopy**.

-   Om du har installerat 64-bitarsversionen finns den här: **%ProgramFiles%\\Microsoft SDK\\Azure\\AzCopy**.

**Hur använder jag en anpassad HTTPS-domän med mitt lagringskonto? Hur får jag till exempel\/"https: /mystorageaccountname.blob.core.windows.net/images/image.gif" att visas\/som "https: /www.contoso.com/images/image.gif"?**

TLS/SSL stöds för närvarande inte på lagringskonton med anpassade domäner.
Men du kan använda icke-HTTPS-anpassade domäner. Mer information finns i [Konfigurera ett anpassat domännamn för slutpunkten för Blob-lagring](../blobs/storage-custom-domain-name.md).

## <a name="access-to-storage"></a>Tillgång till lagring

**Hur mappar jag en behållarmapp på en virtuell dator?**

Använd en Azure-filresurs.

**Hur kommer jag åt Azure Files redundant lagring?**

Geo redundant lagring med läsåtkomst krävs för att komma åt redundant lagring. Azure-filer stöder dock endast lokalt redundant lagring och geografisk standardupptundret lagring som inte tillåter skrivskyddad åtkomst.

**För ett replikerat lagringskonto (till exempel zonundant lagring, geo redundant lagring eller geo redundant lagring med läsåtkomst), hur kommer jag åt data som lagras i den sekundära regionen?**

-   Om du använder zonupptäckt lagring eller geoupptäckt lagring kan du inte komma åt data från den sekundära regionen om du inte initierar en redundans till den regionen. Mer information om redundansprocessen finns i [Redundan- och lagringskontos redundans (förhandsversion) i Azure Storage](storage-disaster-recovery-guidance.md).

-   Om du använder geoupptredande lagring med läsåtkomst kan du komma åt data från den sekundära regionen när som helst. Använd någon av följande metoder:  

    - **AzCopy**: Lägg till **-sekundärt** till lagringskontonamnet i URL:en för att komma åt den sekundära slutpunkten. Ett exempel:  

      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **SAS-token**: Använd en SAS-token för att komma åt data från slutpunkten. Mer information finns i [Använda signaturer för delad åtkomst](storage-sas-overview.md).

**Hur använder jag FTP för att komma åt data som finns i ett lagringskonto?**

Det finns inget sätt att komma åt ett lagringskonto direkt med ftp. Du kan dock konfigurera en virtuell Azure-dator och sedan installera en FTP-server på den virtuella datorn. Du kan låta FTP-servern lagra filer på en Azure Files-resurs eller på en datadisk som är tillgänglig för den virtuella datorn.

Om du bara vill hämta data utan att behöva använda Storage Explorer eller ett liknande program kanske du kan använda en SAS-token. Mer information finns i [Använda signaturer för delad åtkomst](storage-sas-overview.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.

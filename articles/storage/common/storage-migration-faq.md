---
title: Vanliga frågor och svar om Azure Storage migrering | Microsoft Docs
description: Svar på vanliga frågor om migrering av Azure Storage
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: 2ba09496ed8c110e3bf0e431da20f09c82d5ea0b
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985595"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Vanliga frågor om Azure Storage migrering

I den här artikeln besvaras vanliga frågor om Azure Storage migrering. 

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Hur gör jag för att skapa ett skript för att kopiera filer från en behållare till en annan?**

Du kan använda AzCopy för att kopiera filer mellan behållare. Se följande exempel:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy använder [copy BLOB API](https://docs.microsoft.com/rest/api/storageservices/copy-blob) för att kopiera varje fil i behållaren.  
  
Du kan använda valfri virtuell dator eller lokal dator som har Internet åtkomst för att köra AzCopy. Du kan också använda ett Azure Batch schema för att göra detta automatiskt, men det är mer komplicerat.  
  
Automation-skriptet är utformat för Azure Resource Manager distribution i stället för manipulering av lagrings innehåll. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Kostar det något att kopiera data mellan två fil resurser på samma lagrings konto inom samma region?**

Nej. Det kostar inget att ta betalt för den här processen.

**Hur gör jag för att säkerhetskopiera hela lagrings kontot till ett annat lagrings konto?**

Det finns inget alternativ för att säkerhetskopiera ett helt lagrings konto direkt. Men du kan manuellt flytta behållaren i det lagrings kontot till ett annat konto genom att använda AzCopy eller Storage Explorer. Följande steg visar hur du använder AzCopy för att flytta behållaren:  
 

1.  Installera kommando rads verktyget [AzCopy](storage-use-azcopy.md) . Med det här verktyget kan du flytta VHD-filen mellan lagrings konton.

2.  När du har installerat AzCopy i Windows med hjälp av installations programmet öppnar du ett kommando tolks fönster och bläddrar sedan till mappen AzCopy-installation på din dator. Som standard installeras AzCopy på **% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy** eller **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Kör följande kommando för att flytta behållaren. Du måste ersätta texten med de faktiska värdena.   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Ange URI för käll lagrings kontot (upp till behållaren).  
    - `/Dest`: Ange URI för mål lagrings kontot (upp till behållaren).  
    - `/SourceKey`: Ange primär nyckel för käll lagrings kontot. Du kan kopiera den här nyckeln från Azure Portal genom att välja lagrings kontot.  
    - `/DestKey`: Ange primär nyckel för mål lagrings kontot. Du kan kopiera den här nyckeln från portalen genom att välja lagrings kontot.

När du har kört det här kommandot flyttas behållar filerna till mål lagrings kontot.

> [!NOTE]
> AzCopy CLI fungerar inte tillsammans med **mönster** växeln när du kopierar från en Azure-blob till en annan.
>
> Du kan kopiera och redigera AzCopy-kommandot direkt och markera kryss rutan för att kontrol lera att **mönstret** matchar källan. Kontrol lera också att **/s** jokertecken är aktiva. Mer information finns i [AzCopy-parametrar](storage-use-azcopy.md).

**Hur gör jag för att flytta data från en lagrings behållare till en annan?**

Följ de här stegen:

1.  Skapa behållaren (mappen) i mål-bloben.

2.  Använd [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) för att kopiera innehållet från den ursprungliga BLOB-behållaren till en annan BLOB-behållare.

**Hur gör jag för att skapa ett PowerShell-skript för att flytta data från en Azure-filresurs till en annan i Azure Storage?**

Använd AzCopy för att flytta data från en Azure-filresurs till en annan i Azure Storage. Mer information finns i [överföra data med AZCopy i Windows](storage-use-azcopy.md) och [överföra data med AZCopy på Linux](storage-use-azcopy-linux.md).

**Hur gör jag för att du ladda upp stora CSV-filer till Azure Storage?**

Använd AzCopy för att ladda upp stora CSV-filer till Azure Storage. Mer information finns i [överföra data med AZCopy i Windows](storage-use-azcopy.md) och [överföra data med AZCopy på Linux](storage-use-azcopy-linux.md).

**Jag måste flytta loggarna från enhet D till mitt Azure Storage-konto varje dag. Hur gör jag för att automatisera detta?**

Du kan använda AzCopy och skapa en uppgift i Schemaläggaren. Ladda upp filer till ett Azure Storage-konto med hjälp av ett AzCopy-kommando skript. Mer information finns i [så här konfigurerar och kör du Start åtgärder för en moln tjänst](../../cloud-services/cloud-services-startup-tasks.md).

**Hur gör jag för att flytta mitt lagrings konto mellan prenumerationer?**

Använd AzCopy för att flytta ditt lagrings konto mellan prenumerationer. Mer information finns i [överföra data med AZCopy i Windows](storage-use-azcopy.md) och [överföra data med AZCopy på Linux](storage-use-azcopy-linux.md).

**Hur kan jag flytta cirka 10 TB data till lagring i en annan region?**

Använd AzCopy för att flytta data. Mer information finns i [överföra data med AZCopy i Windows](storage-use-azcopy.md) och [överföra data med AZCopy på Linux](storage-use-azcopy-linux.md).

**Hur kan jag kopiera data från en lokal plats till Azure Storage?**

Använd AzCopy för att kopiera data. Mer information finns i [överföra data med AZCopy i Windows](storage-use-azcopy.md) och [överföra data med AZCopy på Linux](storage-use-azcopy-linux.md).

**Hur kan jag flytta data från en lokal plats till Azure Files?**

Använd AzCopy för att flytta data. Mer information finns i [överföra data med AZCopy i Windows](storage-use-azcopy.md) och [överföra data med AZCopy på Linux](storage-use-azcopy-linux.md).

**Hur gör jag för att mappar en container-mapp på en virtuell dator?**

Använd en Azure-filresurs.

**Hur gör jag för att säkerhetskopiera Azure File Storage?**

Det finns ingen lösning för säkerhets kopiering. Azure Files stöder dock även asynkron kopiering. Så du kan kopiera filer:

- Från en resurs till en annan resurs inom ett lagrings konto eller till ett annat lagrings konto.

- Från en resurs till en BLOB-behållare i ett lagrings konto eller till ett annat lagrings konto.

Mer information finns i [överföra data med AZCopy i Windows](storage-use-azcopy.md).

**Hur gör jag för att flytta hanterade diskar till ett annat lagrings konto?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Följ de här stegen:

1.  Stoppa den virtuella dator som den hanterade disken är ansluten till.

2.  Kopiera den hanterade virtuella hård disken från ett zon till ett annat genom att köra följande Azure PowerShell skript:

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Skapa en hanterad disk med hjälp av VHD-filen i en annan region dit du kopierade den virtuella hård disken. Det gör du genom att köra följande Azure PowerShell skript:  

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

Mer information om hur du distribuerar en virtuell dator från en hanterad disk finns i [CreateVmFromManagedOsDisk. ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Hur kan jag hämta 1-2 TB data från Azure Portal?**

Använd AzCopy för att hämta data. Mer information finns i [överföra data med AZCopy i Windows](storage-use-azcopy.md) och [överföra data med AZCopy på Linux](storage-use-azcopy-linux.md).

**Hur gör jag för att ändra den sekundära platsen till regionen Europa för ett lagrings konto?**

När du skapar ett lagrings konto väljer du den primära regionen för kontot. Valet av den sekundära regionen baseras på den primära regionen och kan inte ändras. Mer information finns i [Geo-redundant lagring (GRS): Replikering mellan regioner för Azure Storage](storage-redundancy.md).

**Var kan jag få mer information om Azure Storage Service Encryption (SSE)?**  
  
Se följande artiklar:

-  [Säkerhetsguide för Azure Storage](storage-security-guide.md)

-  [Azure Storage tjänst kryptering för vilande data](storage-service-encryption.md)

**Hur gör jag för att flytta eller hämta data från ett lagrings konto?**

Använd AzCopy för att hämta data. Mer information finns i [överföra data med AZCopy i Windows](storage-use-azcopy.md) och [överföra data med AZCopy på Linux](storage-use-azcopy-linux.md).


**Hur gör jag för att kryptering av data i ett lagrings konto?**

När du har aktiverat kryptering i ett lagrings konto krypteras inte befintliga data. Om du vill kryptera befintliga data måste du ladda upp det igen till lagrings kontot.

Använd AzCopy för att kopiera data till ett annat lagrings konto och sedan flytta tillbaka data. Du kan också använda [kryptering i vila](storage-service-encryption.md).

**Hur kan jag hämta en virtuell hård disk till en lokal dator, förutom med alternativet Hämta i portalen?**

Du kan använda [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att ladda ned en virtuell hård disk.

**Finns det några krav för att ändra replikeringen av ett lagrings konto från Geo-redundant lagring till lokalt redundant lagring?**

Nej. 

**Hur gör jag för att åtkomst Azure Files redundant lagring?**

Geo-redundant lagring med Läs behörighet krävs för att få åtkomst till redundant lagring. Azure Files stöder dock endast lokalt redundant lagring och standard Geo-redundant lagring som inte tillåter skrivskyddad åtkomst. 

**Hur gör jag för att flytta från ett Premium Storage-konto till ett standard lagrings konto?**

Följ de här stegen:

1.  Skapa ett standard lagrings konto. (Eller Använd ett befintligt standard lagrings konto i din prenumeration.)

2.  Ladda ned AzCopy. Kör något av följande AzCopy-kommandon.
      
    Så här kopierar du hela diskar i lagrings kontot:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    Om du bara vill kopiera en disk anger du namnet på disken i **mönster**:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
Åtgärden kan ta flera timmar att slutföra.

Kontrol lera att överföringen har slutförts genom att granska mål lagrings kontots behållare i Azure Portal. När diskarna har kopierats till standard lagrings kontot kan du koppla dem till den virtuella datorn som en befintlig disk. Mer information finns i [så här ansluter du en hanterad datadisk till en virtuell Windows-dator i Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md).  
  
**Hur gör jag för att konvertera till Azure Premium Storage för en fil resurs?**

Premium Storage tillåts inte på en Azure-filresurs.

**Hur gör jag för att uppgradering från ett standard lagrings konto till ett Premium Storage-konto? Hur gör jag för att nedgradering från ett Premium Storage-konto till ett standard lagrings konto?**

Du måste skapa mål lagrings kontot, kopiera data från käll kontot till mål kontot och sedan ta bort käll kontot. Du kan använda ett verktyg som AzCopy för att kopiera data.

Om du har virtuella datorer måste du vidta ytterligare åtgärder innan du migrerar lagrings konto data. Mer information finns i [migrera till Azure Premium Storage (ohanterade diskar)](storage-migration-to-premium-storage.md).

**Hur gör jag för att flytta från ett klassiskt lagrings konto till ett Azure Resource Manager lagrings konto?**

Du kan använda cmdleten **Move-AzStorageAccount** . Denna cmdlet har flera steg (validate, preping, commit). Du kan verifiera flytten innan du gör den.

Om du har virtuella datorer måste du vidta ytterligare åtgärder innan du migrerar lagrings konto data. Mer information finns i [migrera IaaS-resurser från klassisk till Azure Resource Manager med hjälp av Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Hur gör jag för att ladda ned data till en Linux-baserad dator från ett Azure Storage-konto eller ladda upp data från en Linux-dator?**

Du kan använda Azure CLI.

- Hämta en enda BLOB:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Ladda upp en enda BLOB: 

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Hur kan jag ge andra personer åtkomst till mina lagrings resurser?**

Ge andra personer åtkomst till lagrings resurserna:

-   Använd en signatur för signatur för delad åtkomst (SAS) för att ge åtkomst till en resurs. 

-   Ange en användare med den primära eller sekundära nyckeln för lagrings kontot. Mer information finns i [Hantera ditt lagrings konto](storage-account-manage.md#access-keys).

-   Ändra åtkomst principen så att anonym åtkomst tillåts. Mer information finns i [bevilja anonyma användare behörigheter till behållare och blobbar](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Var är AzCopy installerat?**

-   Om du öppnar AzCopy från kommando raden Microsoft Azure Storage skriver du **AzCopy**. Kommando raden installeras tillsammans med AzCopy.

-   Om du har installerat 32-bitars versionen finns den här: **% ProgramFiles (x86)%\\Microsoft SDK\\Azure\\-AzCopy**.

-   Om du har installerat 64-bitars versionen finns den här: **% ProgramFiles%\\Microsoft SDK\\Azure\\AzCopy**.

**Hur kommer jag åt data som lagras i den sekundära regionen för ett replikerat lagrings konto (till exempel zon redundant lagring, Geo-redundant lagring eller Geo-redundant lagring med Läs åtkomst)?**

-   Om du använder zoner som är redundant lagring eller Geo-redundant lagring, kan du inte komma åt data från den sekundära regionen om du inte initierar en redundansväxling till den regionen. Mer information om redundansväxlingen finns [i haveri beredskap och lagrings kontots redundans (för hands version) i Azure Storage](storage-disaster-recovery-guidance.md).

-   Om du använder Geo-redundant lagring med Läs behörighet kan du när som helst komma åt data från den sekundära regionen. Använd någon av följande metoder:  
      
    - **AzCopy**: Lägg till **sekundär** till lagrings kontots namn i URL: en för att få åtkomst till den sekundära slut punkten. Exempel:  
     
      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **SAS**-token: Använd en SAS-token för att komma åt data från slut punkten. Mer information finns i [använda signaturer för delad åtkomst](storage-sas-overview.md).

**Hur gör jag för att använder du en anpassad HTTPS-domän med mitt lagrings konto? Hur gör jag för att t. ex. "\/https:/mystorageaccountname.blob.Core.Windows.net/images/image.gif" visas som "\/https:/www.contoso.com/images/image.gif"?**

SSL stöds för närvarande inte på lagrings konton med anpassade domäner.
Men du kan använda icke-HTTPS-anpassade domäner. Mer information finns i [Konfigurera ett eget domän namn för din Blob Storage-slutpunkt](../blobs/storage-custom-domain-name.md).

**Hur gör jag för att använda FTP för att komma åt data som finns i ett lagrings konto?**

Det finns inget sätt att få åtkomst till ett lagrings konto direkt med hjälp av FTP. Du kan dock konfigurera en virtuell Azure-dator och sedan installera en FTP-server på den virtuella datorn. Du kan ha FTP-servern lagra filer på en Azure Files resurs eller på en datadisk som är tillgänglig för den virtuella datorn.

Om du bara vill hämta data utan att behöva använda Storage Explorer eller ett liknande program, kan du använda en SAS-token. Mer information finns i [använda signaturer för delad åtkomst](storage-sas-overview.md).

**Hur gör jag för att migrera blobbar från ett lagrings konto till ett annat?**

 Du kan göra detta med hjälp av vårt [skript för BLOB-migrering](../scripts/storage-common-transfer-between-storage-accounts.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt.

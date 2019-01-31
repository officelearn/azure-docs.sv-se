---
title: Azure Storage-migrering vanliga frågor och svar | Microsoft Docs
description: Svar på vanliga frågor om hur du migrerar Azure Storage
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: 095c592e53f44cb674994c440258bcca1bfa760c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467178"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Vanliga frågor och svar om Azure Storage-migrering

Den här artikeln får du svar på vanliga frågor om Azure Storage-migrering. 

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Hur gör jag för att skapa ett skript för att kopiera filer från en behållare till en annan?**

Du kan använda AzCopy för att kopiera filer mellan behållare. Se följande exempel:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy använder den [kopiera Blob API](https://docs.microsoft.com/rest/api/storageservices/copy-blob) som kopierar filer i behållaren.  
  
Du kan använda valfri virtuell dator eller en lokal dator som är ansluten till internet att köra AzCopy. Du kan också använda ett Azure Batch-schema för att göra detta automatiskt, men det har mer komplicerat.  
  
Automation-skriptet är utformat för Azure Resource Manager-distribution i stället för innehåll manipulering av lagring. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Finns det någon avgift för att kopiera data mellan två filresurser på samma lagringskonto i samma region?**

Nej. Det finns ingen kostnad för den här processen.

**Hur säkerhetskopierar jag mitt hela storage-konto till ett annat lagringskonto?**

Det finns inget alternativ för att säkerhetskopiera en hel storage-konto direkt. Men du kan manuellt flytta behållaren i det lagringskontot till ett annat konto med hjälp av AzCopy eller Storage Explorer. Följande steg visar hur du använder AzCopy för att flytta behållaren:  
 

1.  Installera den [AzCopy](storage-use-azcopy.md) kommandoradsverktyget. Det här verktyget kan du flytta den virtuella hårddiskfilen mellan lagringskonton.

2.  När du har installerat AzCopy på Windows med hjälp av installationsprogrammet, öppna ett kommandotolksfönster och bläddrar till installationsmappen för AzCopy på datorn. Som standard installeras AzCopy på **% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy** eller **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Kör följande kommando för att flytta behållaren. Du måste ersätta texten med de faktiska värdena.   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Ange URI: N för källagringskontot (upp till behållaren).  
    - `/Dest`: Ange URI: N för mål-lagringskontot (upp till behållaren).  
    - `/SourceKey`: Ange den primära nyckeln för källagringskontot. Du kan kopiera den här nyckeln från Azure portal genom att välja storage-konto.  
    - `/DestKey`: Ange den primära nyckeln för mållagringskontot. Du kan kopiera den här nyckeln från portalen genom att välja storage-konto.

När du har kört det här kommandot flyttas behållarfiler till mållagringskontot.

> [!NOTE]
> AzCopy CLI inte fungerar tillsammans med den **mönstret** växla när du kopierar från en Azure blob till en annan.
>
> Du kan direkt kopiera och redigera AzCopy-kommandot och kontrollera om du vill se till att **mönstret** matchar källan. Också kontrollera att **/S** jokertecken tillämpas. Mer information finns i [AzCopy parametrar](storage-use-azcopy.md).

**Hur flyttar jag data från en storage-behållare till en annan?**

Följ de här stegen:

1.  Skapa behållaren (mapp) i mål-blob.

2.  Använd [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) att kopiera innehållet från den ursprungliga blob-behållaren till en annan blob-behållare.

**Hur gör jag för att skapa ett PowerShell.skript för att flytta data från en Azure-filresurs till en annan i Azure Storage?**

Använda AzCopy för att flytta data från en Azure-filresurs till en annan i Azure Storage. Mer information finns i [överföra data med AzCopy i Windows](storage-use-azcopy.md) och [överföra data med AzCopy i Linux](storage-use-azcopy-linux.md).

**Hur jag för att överföra stora CSV-filer till Azure Storage?**

Använda AzCopy för att överföra stora CSV-filer till Azure Storage. Mer information finns i [överföra data med AzCopy i Windows](storage-use-azcopy.md) och [överföra data med AzCopy i Linux](storage-use-azcopy-linux.md).

**Jag behöver flytta loggarna från enhet D till mitt Azure storage-konto varje dag. Hur automatiserar jag?**

Du kan använda AzCopy och skapa en uppgift i Schemaläggaren. Ladda upp filer till ett Azure storage-konto med hjälp av ett AzCopy-kommandoskript. Mer information finns i [hur du konfigurerar och köra startåtgärder för en molntjänst](../../cloud-services/cloud-services-startup-tasks.md).

**Hur flyttar jag mitt lagringskonto mellan prenumerationer?**

Använda AzCopy för att flytta ditt lagringskonto mellan prenumerationer. Mer information finns i [överföra data med AzCopy i Windows](storage-use-azcopy.md) och [överföra data med AzCopy i Linux](storage-use-azcopy-linux.md).

**Hur kan jag flytta 10 TB data till lagring på en annan region?**

Använda AzCopy för att flytta data. Mer information finns i [överföra data med AzCopy i Windows](storage-use-azcopy.md) och [överföra data med AzCopy i Linux](storage-use-azcopy-linux.md).

**Hur kopierar data från en lokal plats till Azure Storage?**

Använda AzCopy för att kopiera data. Mer information finns i [överföra data med AzCopy i Windows](storage-use-azcopy.md) och [överföra data med AzCopy i Linux](storage-use-azcopy-linux.md).

**Hur kan jag flytta data från en lokal plats till Azure Files?**

Använda AzCopy för att flytta data. Mer information finns i [överföra data med AzCopy i Windows](storage-use-azcopy.md) och [överföra data med AzCopy i Linux](storage-use-azcopy-linux.md).

**Hur jag för att mappa en behållare mapp på en virtuell dator?**

Använda en Azure-filresurs.

**Hur kan jag säkerhetskopiera Azure file storage?**

Det finns ingen lösning för säkerhetskopiering. Azure Files stöder också asynkron kopia. Därför kan du kopiera filer:

- Från en resurs till en annan resurs i ett lagringskonto eller till ett annat lagringskonto.

- Från en resurs till en blob-behållare i ett lagringskonto eller till ett annat lagringskonto.

Mer information finns i [överföra data med AzCopy i Windows](storage-use-azcopy.md).

**Hur flyttar jag hanterade diskar till ett annat lagringskonto?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Följ de här stegen:

1.  Stoppa den virtuella dator som den hanterade disken är ansluten till.

2.  Kopiera den hanterade disken virtuell Hårddisk från ett område till en annan genom att köra följande Azure PowerShell-skript:

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Skapa en hanterad disk med hjälp av VHD-filen i en annan region dit du kopierade den virtuella Hårddisken. Gör detta genom att köra följande Azure PowerShell-skript:  

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

Läs mer om hur du distribuerar en virtuell dator från en hanterad disk, [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Hur kan jag hämta 1 – 2 TB data från Azure-portalen?**

Använda AzCopy för att hämta data. Mer information finns i [överföra data med AzCopy i Windows](storage-use-azcopy.md) och [överföra data med AzCopy i Linux](storage-use-azcopy-linux.md).

**Hur ändrar jag den sekundära platsen till regionen Europa för ett lagringskonto?**

När du skapar ett lagringskonto, väljer du den primära regionen för kontot. Valet av den sekundära regionen är baserad på den primära regionen och kan inte ändras. Mer information finns i [Geo-redundant lagring (GRS): Tvärregional replikering för Azure Storage](storage-redundancy.md).

**Var hittar jag mer information om Azure Storage Service Encryption (SSE)?**  
  
Se följande artiklar:

-  [Säkerhetsguide för Azure Storage](storage-security-guide.md)

-  [Azure Storage Service Encryption för vilande Data](storage-service-encryption.md)

**Hur jag för att flytta eller hämta data från ett lagringskonto?**

Använda AzCopy för att hämta data. Mer information finns i [överföra data med AzCopy i Windows](storage-use-azcopy.md) och [överföra data med AzCopy i Linux](storage-use-azcopy-linux.md).


**Hur jag för att kryptera data i ett lagringskonto?**

När du har aktiverat kryptering i ett lagringskonto, krypteras inte befintliga data. För att kryptera befintliga data, måste du överföra den igen till lagringskontot.

Använda AzCopy för att kopiera data till ett annat lagringskonto och sedan flytta data tillbaka. Du kan också använda [kryptering i vila](storage-service-encryption.md).

**Hur kan jag hämta en virtuell Hårddisk till en lokal dator, andra än med hjälp av alternativet ladda ned i portalen?**

Du kan använda [Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/) att ladda ned en virtuell Hårddisk.

**Finns det några krav för att ändra replikeringen av ett lagringskonto från geo-redundant lagring till lokalt redundant lagring?**

Nej. 

**Hur kommer jag åt Azure Files redundant lagring?**

Läsåtkomst till geografiskt redundant lagring krävs för att få åtkomst till redundant lagring. Azure Files stöder dock endast lokalt redundant lagring och standard geo-redundant lagring som inte tillåter läsbehörighet. 

**Hur flyttar jag från ett premium storage-konto till ett standardlagringskonto?**

Följ de här stegen:

1.  Skapa ett standardlagringskonto. (Eller använda ett befintligt standard storage-konto i din prenumeration.)

2.  Hämta AzCopy. Kör något av följande kommandon för AzCopy.
      
    Kopiera hela diskar i storage-konto:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    Kopiera bara en disk genom att ange namnet på disken i **mönstret**:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
Åtgärden kan ta flera timmar att slutföra.

För att säkerställa att överföringen har slutförts, granska Målbehållaren för storage-konto i Azure-portalen. När diskarna har kopierats till standard storage-konto, kan du koppla dem till den virtuella datorn som en befintlig disk. Mer information finns i [hur du kopplar en hanterad datadisk till en Windows-dator i Azure portal](../../virtual-machines/windows/attach-managed-disk-portal.md).  
  
**Hur jag för att omvandla till Azure Premium Storage för en filresurs?**

Premium Storage är inte tillåtet på en Azure-filresurs.

**Hur uppgraderar jag från ett standardlagringskonto till ett premium storage-konto? Hur jag Nedgradera från premium storage-konto till ett standardlagringskonto?**

Du måste skapa mållagringskontot, kopiera data från kontot källa till mål-konto och ta sedan bort källkontot. Du kan använda ett verktyg som AzCopy för att kopiera data.

Om du har virtuella datorer, måste du vidta ytterligare åtgärder innan du migrerar data för storage-konto. Mer information finns i [migrera till Azure Premium Storage (ohanterade diskar)](storage-migration-to-premium-storage.md).

**Hur flyttar jag från ett klassiskt lagringskonto till ett Azure Resource Manager-lagringskonto?**

Du kan använda den **flytta AzStorageAccount** cmdlet. Denna cmdlet har flera steg (validera, Förbered, genomför). Du kan validera flytten innan du gör den.

Om du har virtuella datorer, måste du vidta ytterligare åtgärder innan du migrerar data för storage-konto. Mer information finns i [migrera IaaS-resurser från klassisk till Azure Resource Manager med hjälp av Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Hur jag hämta data till en Linux-baserade dator från ett Azure storage-konto eller ladda upp data från en Linux-dator?**

Du kan använda Azure CLI.

- Hämta en enda blob:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Ladda upp en enda blob: 

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Hur kan jag ge andra användare åtkomst till min lagringsresurser?**

Att ge andra användare åtkomst till lagringsresurser:

-   Använd en delad åtkomst (SAS) signaturtoken för att ge åtkomst till en resurs. 

-   Ange en användare med den primära eller sekundära nyckeln för lagringskontot. Mer information finns i [hantera ditt lagringskonto](storage-account-manage.md#access-keys).

-   Ändra åtkomstprincipen för att tillåta anonym åtkomst. Mer information finns i [bevilja anonyma användare till behållare och blobbar](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Där är AzCopy installerad?**

-   Om du kommer åt AzCopy från Microsoft Azure Storage-kommandoraden skriver **AzCopy**. Kommandoraden installeras tillsammans med AzCopy.

-   Om du har installerat 32-bitarsversionen, är det här: **% ProgramFiles(x86) %\\Microsoft SDKs\\Azure\\AzCopy**.

-   Om du har installerat 64-bitarsversionen, är det här: **% ProgramFiles %\\Microsoft SDKs\\Azure\\AzCopy**.

**För en replikerad lagring-konto (till exempel zonredundant lagring, geo-redundant lagring eller read-access geo-redundant lagring), hur kommer jag åt data som lagras i den sekundära regionen?**

-   Om du använder zonredundant lagring eller geo-redundant lagring kan du inte kommer åt data från den sekundära regionen, såvida inte en redundansväxling. Läs mer om redundansprocessen [vad som händer om det uppstår redundans storage](storage-disaster-recovery-guidance.md#what-to-expect-if-a-storage-failover-occurs).

-   Om du använder läsåtkomst till geografiskt redundant lagring, kan du komma åt data från den sekundära regionen när som helst. Använd någon av följande metoder:  
      
    - **AzCopy**: Lägg till **-sekundär** till lagringskontonamn i URL-Adressen till den sekundära slutpunkten. Exempel:  
     
      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **SAS-token**: Använda en SAS-token för att komma åt data från slutpunkten. Mer information finns i [använda signaturer för delad åtkomst](storage-dotnet-shared-access-signature-part-1.md).

**Hur kan jag använda en anpassad domän HTTPS med mitt lagringskonto? Till exempel hur gör jag ”https://mystorageaccountname.blob.core.windows.net/images/image.gif” visas som ”https://www.contoso.com/images/image.gif”?**

SSL stöds inte för närvarande på storage-konton med anpassade domäner.
Men du kan använda anpassade domäner för icke-HTTPS. Mer information finns i [konfigurera ett anpassat domännamn för din Blob storage-slutpunkt](../blobs/storage-custom-domain-name.md).

**Hur kan jag använda FTP för att komma åt data som är i ett lagringskonto?**

Det går inte att komma åt ett storage-konto direkt med hjälp av FTP. Du kan dock konfigurera virtuella Azure-datorer och sedan installera en FTP-server på den virtuella datorn. Du kan ha FTP-servern lagrar filer på en Azure Files-resurs eller en datadisk som är tillgänglig för den virtuella datorn.

Om du vill att bara ladda ned data utan att använda Storage Explorer eller ett liknande program kanske du kan använda en SAS-token. Mer information finns i [använda signaturer för delad åtkomst](storage-dotnet-shared-access-signature-part-1.md).

**Hur migrerar jag Blobar från ett lagringskonto till en annan?**

 Du kan göra detta med hjälp av vår [Blob migreringsskriptet](../scripts/storage-common-transfer-between-storage-accounts.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.

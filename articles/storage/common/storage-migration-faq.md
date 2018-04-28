---
title: Azure Storage migration vanliga frågor och svar | Microsoft Docs
description: Svar på vanliga frågor om hur du migrerar Azure Storage
services: storage
documentationcenter: na
author: genlin
manager: timlt
editor: tysonn
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 11/16/2017
ms.author: genli
ms.openlocfilehash: 9bde8657e435f1c759eceb26682e650605012a89
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Vanliga frågor och svar om Azure Storage-migrering

Den här artikeln innehåller svar på vanliga frågor om Azure Storage-migrering. 

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Hur skapar ett skript för att kopiera filer från en behållare till en annan?**

Du kan använda AzCopy för att kopiera filer mellan behållare. Se följande exempel:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy använder den [kopiera Blob API](https://docs.microsoft.com/rest/api/storageservices/copy-blob) att kopiera varje fil i behållaren.  
  
Du kan använda en virtuell dator eller en lokal dator som har Internetåtkomst för att köra AzCopy. Du kan också använda ett Azure Batch-schema för att göra detta automatiskt, men den har mer komplicerade.  
  
Automatiseringsskriptet är utformad för Azure Resource Manager-distribution i stället för lagring innehåll manipulation. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Finns det en avgift för kopiering av data mellan två filresurser på samma lagringskonto i samma region?**

Nej. Det är gratis för den här processen.

**Hur säkerhetskopierar jag mina hela storage-konto till en annan storage-konto?**

Det går inte att säkerhetskopiera en hel storage-konto direkt. Men du kan flytta manuellt behållaren i det storage-kontot till ett annat konto med hjälp av AzCopy eller Lagringsutforskaren. Följande steg visar hur du använder AzCopy för att flytta behållaren:  
 

1.  Installera den [AzCopy](storage-use-azcopy.md) kommandoradsverktyget. Det här verktyget kan du flytta VHD-filen mellan lagringskonton.

2.  Efter installation av AzCopy i Windows med hjälp av installationsprogrammet, öppna ett kommandotolksfönster och bläddrar till installationsmappen för AzCopy på datorn. Som standard installeras AzCopy på **% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy** eller **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Kör följande kommando för att flytta behållaren. Du måste ersätta texten med verkliga värden.   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Ange URI: N för källa storage-konto (upp till behållaren).  
    - `/Dest`: Ange URI för mål-lagringskontot (upp till behållaren).  
    - `/SourceKey`: Ange den primära nyckeln för lagringskontot källa. Du kan kopiera den här nyckeln från Azure portal genom att välja lagringskontot.  
    - `/DestKey`: Ange den primära nyckeln för mål-lagringskontot. Du kan kopiera den här nyckeln från portalen genom att välja lagringskontot.

När du har kört kommandot flyttas behållaren filerna till mål-lagringskontot.

> [!NOTE]
> AzCopy CLI inte fungerar tillsammans med den **mönster** växeln när du kopierar från en Azure blob till en annan.
>
> Du kan direkt kopiera och redigera AzCopy-kommandot och dubbelkontrollera för att se till att **mönster** matchar källan. Kontrollera också att som **/S** jokertecken är aktiva. Mer information finns i [AzCopy parametrar](storage-use-azcopy.md).

**Hur flyttar data från en lagringsbehållare till en annan?**

Följ de här stegen:

1.  Skapa en behållare (mapp) i mål-blob.

2.  Använd [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) att kopiera innehållet från den ursprungliga blob-behållaren till en annan blob-behållare.

**Hur skapar ett PowerShell.skript för att flytta data från en Azure-filresursen till en annan i Azure Storage?**

Använd AzCopy för att flytta data från en Azure-filresursen till en annan i Azure Storage. Mer information finns i [överföra data med AzCopy på Windows](storage-use-azcopy.md) och [överföra data med AzCopy på Linux](storage-use-azcopy-linux.md).

**Hur överför stora CSV-filer till Azure Storage?**

Använda AzCopy för att överföra stora CSV-filer till Azure Storage. Mer information finns i [överföra data med AzCopy på Windows](storage-use-azcopy.md) och [överföra data med AzCopy på Linux](storage-use-azcopy-linux.md).

**Jag behöver flytta loggarna från enhet D till Azure storage-konto varje dag. Hur automatisera detta?**

Du kan använda AzCopy och skapa en uppgift i Schemaläggaren. Ladda upp filer till ett Azure storage-konto med hjälp av ett kommandoskript för AzCopy. Mer information finns i [hur du konfigurerar och kör Start-aktiviteter för en molntjänst](../../cloud-services/cloud-services-startup-tasks.md).

**Hur flytta mitt lagringskonto mellan prenumerationer?**

Använda AzCopy för att flytta ditt lagringskonto mellan prenumerationer. Mer information finns i [överföra data med AzCopy på Windows](storage-use-azcopy.md) och [överföra data med AzCopy på Linux](storage-use-azcopy-linux.md).

**Hur kan jag flytta 10 TB data till lagring på en annan region?**

Använd AzCopy för att flytta data. Mer information finns i [överföra data med AzCopy på Windows](storage-use-azcopy.md) och [överföra data med AzCopy på Linux](storage-use-azcopy-linux.md).

**Hur kopierar data från lokala till Azure Storage?**

Använd AzCopy för att kopiera data. Mer information finns i [överföra data med AzCopy på Windows](storage-use-azcopy.md) och [överföra data med AzCopy på Linux](storage-use-azcopy-linux.md).

**Hur kan jag flytta data från lokala till Azure Files?**

Använd AzCopy för att flytta data. Mer information finns i [överföra data med AzCopy på Windows](storage-use-azcopy.md) och [överföra data med AzCopy på Linux](storage-use-azcopy-linux.md).

**Hur jag för att mappa en behållare mapp på en virtuell dator?**

Använda en Azure-filresursen.

**Hur säkerhetskopierar jag Azure file storage?**

Det finns ingen lösning för säkerhetskopiering. Azure Files stöder också asynkron kopia. Därför kan du kopiera filerna:

- Från en resurs till en annan resurs i ett lagringskonto eller till ett annat lagringskonto.

- Från en resurs till en blobbbehållare i ett lagringskonto eller till ett annat lagringskonto.

Mer information finns i [överföra data med AzCopy på Windows](storage-use-azcopy.md).

**Hur flytta hanterade diskar till en annan storage-konto?**

Följ de här stegen:

1.  Stoppa den virtuella datorn som den hantera disken är kopplad till.

2.  Kopiera den hantera disken VHD från ett område till en annan genom att köra följande Azure PowerShell-skript:

    ```
    Connect-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionId <ID>

    $sas = Grant-AzureRmDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzureStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzureStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Skapa en hanterad disk med hjälp av VHD-filen i en annan region som du kopierade den virtuella Hårddisken. Kör följande Azure PowerShell-skript för att göra detta:  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ``` 

Mer information om hur du distribuerar en virtuell dator från en hanterad disk finns [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Hur kan jag hämta 1 – 2 TB data från Azure portal?**

Använda AzCopy för att hämta data. Mer information finns i [överföra data med AzCopy på Windows](storage-use-azcopy.md) och [överföra data med AzCopy på Linux](storage-use-azcopy-linux.md).

**Hur ändrar den sekundära platsen till Europa region för ett lagringskonto?**

När du skapar ett lagringskonto, väljer du den primära regionen för kontot. Valet av den sekundära regionen som är baserad på den primära regionen och kan inte ändras. Mer information finns i [Geo-redundant lagring (GRS): mellan regionala replikering för Azure Storage](storage-redundancy.md).

**Var kan jag få mer information om Azure Storage Service kryptering (SSE)?**  
  
Se följande artiklar:

-  [Säkerhetsguide för Azure Storage](storage-security-guide.md)

-  [Azure Storage Service-kryptering av vilande Data](storage-service-encryption.md)

**Hur flytta eller hämta data från ett lagringskonto?**

Använda AzCopy för att hämta data. Mer information finns i [överföra data med AzCopy på Windows](storage-use-azcopy.md) och [överföra data med AzCopy på Linux](storage-use-azcopy-linux.md).


**Hur jag för att kryptera data i ett lagringskonto?**

När du aktiverar kryptering i ett lagringskonto, krypteras inte befintliga data. Om du vill kryptera befintliga data måste du överföra den igen till lagringskontot.

Använd AzCopy för att kopiera data till ett annat lagringskonto och flytta tillbaka dessa data. Du kan också använda [kryptering i vila](storage-service-encryption.md).

**Hur kan jag hämta en virtuell Hårddisk till en lokal dator, andra än med hjälp av alternativet ladda ned i portalen**

Du kan använda [Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/) att ladda ned en virtuell Hårddisk.

**Finns det några krav för att ändra replikering av ett lagringskonto från geo-redundant lagring till lokalt redundant lagring?**

Nej. 

**Hur kommer jag åt redundant lagring med Azure-filer?**

Geo-redundant lagring med läsbehörighet krävs för att komma åt redundant lagring. Azure Files stöder dock bara lokalt redundant lagring och standard geo-redundant lagring som inte tillåter läsbehörighet. 

**Hur flyttar från ett premiumlagringskonto till ett standardlagringskonto?**

Följ de här stegen:

1.  Skapa ett standardlagringskonto. (Eller använda ett befintligt lagringskonto som standard i din prenumeration.)

2.  Hämta AzCopy. Kör något av följande kommandon för AzCopy.
      
    Kopiera hela diskar i storage-konto:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    Om du vill kopiera bara en disk, ange namnet på disken i **mönster**:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
Åtgärden kan ta flera timmar att slutföra.

Granska målbehållare för storage-konto i Azure-portalen för att säkerställa att överföringen har slutförts. När diskarna kopieras till standardlagringskontot, kan du ansluta dem till den virtuella datorn som en befintlig disk. Mer information finns i [hur du kopplar en datadisk hanterade till en Windows-dator i Azure portal](../../virtual-machines/windows/attach-managed-disk-portal.md).  
  
**Hur konverterar till Azure Premium-lagring för en filresurs?**

Premium-lagring är inte tillåten på en Azure-filresursen.

**Hur uppgraderar jag från ett standardlagringskonto till ett premium-lagringskonto? Hur jag Nedgradera från ett premiumlagringskonto till ett standardlagringskonto?**

Du måste skapa lagringskontot mål, kopiera data från källplatsens konto till mål-konto och ta bort kontot källa. Du kan använda ett verktyg som AzCopy för att kopiera data.

Om du har virtuella datorer, måste du vidta ytterligare åtgärder innan du migrerar data för storage-konto. Mer information finns i [migrera till Azure Premium-lagring (ohanterade diskar)](storage-migration-to-premium-storage.md).

**Hur flyttar från ett klassiska storage-konto till en Azure Resource Manager storage-konto?**

Du kan använda den **flytta AzureStorageAccount** cmdlet. Denna cmdlet har flera steg (validera, förbereda, genomför). Du kan validera flyttningen innan du gör den.

Om du har virtuella datorer, måste du vidta ytterligare åtgärder innan du migrerar data för storage-konto. Mer information finns i [migrera IaaS-resurser från klassiska till Azure Resource Manager med hjälp av Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Hur jag hämta data till en Linux-baserade dator från ett Azure storage-konto eller överföra data från en Linux-dator?**

Du kan använda Azure CLI.

- Hämta en enda blob:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Överför en enda blob: 

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Hur kan jag ge andra användare åtkomst till min lagringsresurser?**

Ge andra användare åtkomst till lagringsresurser:

-   Använda en token för delad åtkomst signatur (SAS) för att ge åtkomst till en resurs. 

-   Ange en användare med den primära och sekundära nycklarna för lagringskontot. Mer information finns i [hantera ditt lagringskonto](storage-create-storage-account.md#manage-your-storage-account).

-   Ändra åtkomstprincipen för att tillåta anonym åtkomst. Mer information finns i [bevilja anonyma användare behörighet till behållare och blobbar](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Var installeras AzCopy?**

-   Om du har åtkomst till AzCopy från Microsoft Azure Storage-kommandoraden skriver **AzCopy**. Kommandoraden är installerat tillsammans med AzCopy.

-   Om du har installerat 32-bitarsversionen, är det här: **% ProgramFiles(x86) %\\Microsoft SDKs\\Azure\\AzCopy**.

-   Om du har installerat 64-bitarsversionen, är det här: **% ProgramFiles %\\Microsoft SDKs\\Azure\\AzCopy**.

**För en replikerad lagringskonto (till exempel zonredundant lagring, geo-redundant lagring eller geo-redundant lagring med läsbehörighet), hur kommer jag åt data som lagras i den sekundära regionen?**

-   Om du använder zonredundant lagring eller geo-redundant lagring, kan du komma åt data från den sekundära regionen såvida inte en växling vid fel. Mer information om processen för växling vid fel finns [vad som händer om det uppstår redundans lagring](storage-disaster-recovery-guidance.md#what-to-expect-if-a-storage-failover-occurs).

-   Om du använder geo-redundant lagring med läsbehörighet kan komma åt data från den sekundära regionen när som helst. Använd någon av följande metoder:  
      
    - **AzCopy**: Lägg till **-sekundär** till lagringskontots namn i URL: en för den sekundära slutpunkten. Exempel:  
     
      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **SAS-token**: använda en SAS-token för att komma åt data från slutpunkten. Mer information finns i [använder signaturer för delad åtkomst](storage-dotnet-shared-access-signature-part-1.md).

**Hur använder en anpassad domän för HTTPS med mitt lagringskonto? Till exempel hur gör jag ”https://mystorageaccountname.blob.core.windows.net/images/image.gif” visas som ”https://www.contoso.com/images/image.gif”?**

SSL stöds inte på storage-konton med anpassade domäner.
Men du kan använda icke-HTTPS anpassade domäner. Mer information finns i [konfigurera ett anpassat domännamn för din slutpunkt för Blob-lagring](../blobs/storage-custom-domain-name.md).

**Hur använder jag FTP för att komma åt data som är i ett lagringskonto?**

Det går inte att komma åt ett storage-konto direkt med hjälp av FTP. Du kan dock konfigurera en virtuell Azure-dator och sedan installera en FTP-server på den virtuella datorn. Du kan ha FTP-servern lagrar filer på en resurs i Azure-filer eller på en datadisk som är tillgänglig för den virtuella datorn.

Om du bara vill ladda ned data utan att använda Lagringsutforskaren eller ett liknande program, kanske du kan använda en SAS-token. Mer information finns i [använder signaturer för delad åtkomst](storage-dotnet-shared-access-signature-part-1.md).

**Hur migrerar Blobbar från ett lagringskonto till en annan?**

 Du kan göra detta med hjälp av vår [Blob migreringsskriptet](../scripts/storage-common-transfer-between-storage-accounts.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande behöver hjälp [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.

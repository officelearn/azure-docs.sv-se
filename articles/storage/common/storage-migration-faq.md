---
title: "Azure Storage migration vanliga frågor och svar | Microsoft Docs"
description: "Svar på vanliga frågor om hur du migrerar Azure Storage"
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
ms.openlocfilehash: 516a0487afe11ef6915a002375661a23eaf13edc
ms.sourcegitcommit: 21a58a43ceceaefb4cd46c29180a629429bfcf76
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2017
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Vanliga frågor och svar om Azure Storage-migrering

Den här artikeln innehåller svar på vanliga frågor om Azure Storage-migrering. 

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Hur skapar ett skript för att kopiera filer från en behållare till en annan?**

Du kan använda AzCopy för att kopiera filer mellan behållare. Se följande exempel:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

Använder AzCopy [kopiera Blob API](https://docs.microsoft.com/rest/api/storageservices/copy-blob) om du vill kopiera för varje fil i behållaren.  
  
Du kan använda en virtuell dator eller en lokal dator som har Internetåtkomst för att köra AzCopy. Du kan också använda Azure Batch-schema för att göra detta automatiskt, men den har mer komplicerade.  
  
Automation-skriptet är utformad för Azure Resource Manager-distribution i stället för lagring innehåll manipulation. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Finns det en avgift som ingår för att kopiera data mellan två olika filresurser på samma lagringskonto i samma region?**

Nej. Det är gratis för den här processen.

**Hur säkerhetskopierar jag mina hela lagringskontot till andra storage-konto?**

Det går inte att säkerhetskopiera en hel storage-konto direkt. Men du kan flytta manuellt behållaren i det storage-kontot till ett annat konto med hjälp av AzCopy eller Lagringsutforskaren. Följande steg visar hur du använder AzCopy för att flytta behållaren:  
 

1.  Installera den [AzCopy](storage-use-azcopy.md) kommandoradsverktyget. Det här verktyget kan du flytta VHD-filen mellan lagringskonton.

2.  När du har installerat AzCopy i Windows med hjälp av installationsprogrammet, öppna ett fönster med kommandot POromprt och gå sedan till installationsmappen AzCopy på datorn. Som standard installeras AzCopy på **% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy** eller **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.   Kör följande kommando för att flytta behållaren. Du måste ersätta texten med det faktiska värdet.   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

        - / source: Ange källa lagringskonto URI (upp till behållaren)  
        - / dest: Ange mål-lagringskontot URI (upp till behållaren)  
        - /sourcekey: Ange källservern. lagring databaskontots primärnyckel, kan du kopiera den här nyckeln från portalen genom att välja lagringskontot.  
        - /destkey: Ange mål-lagringskontot databaskontots primärnyckel, kan du kopiera den här nyckeln från portalen genom att välja lagringskontot.

När du kör kommandot flyttas behållaren filerna till mål-lagringskontot.

**AzCopy CLI inte fungerar tillsammans med växeln ”mönstret” när du kopierar från en Azure blob till en annan.**

Du kan direkt kopiera och redigera AzCopy cmd och dubbelkontrollera för att kontrollera att mönstret matchar källan. Kontrollera också att som **/S** jokertecken är aktiva. Mer information finns i [AzCopy parametrar](storage-use-azcopy.md).

**Hur flyttar data från en lagringsbehållare till en annan?**

Det gör du genom att följa dessa steg:

1.  Skapa en behållare (mapp) i mål-blob.

2.  Använd [Azcopy](https://azure.microsoft.com/en-us/blog/azcopy-5-1-release/) att kopiera innehållet från den ursprungliga blob-behållaren till en annan blobbbehållare.

**Hur skapar jag en PowerShell.skript för att flytta data från en Azure-filresursen till en annan Azure-lagring**

Använd AzCopy för att flytta data från en Azure-filresursen till en annan azure-lagring. Mer information finns i [överföra data med AzCopy på Windows](storage-use-azcopy.md) och [överföra data med AzCopy på Linux](storage-use-azcopy-linux.md).

**Hur överför stora CSV-filer till Azure storage?**

Använda AzCopy för att överföra stora CSV-filer till Azure Storage. Mer information finns i [överföra data med AzCopy på Windows](storage-use-azcopy.md) och [överföra data med AzCopy på Linux](storage-use-azcopy-linux.md).

**Jag behöver flytta loggarna från ”enhet D” till Azure storage-konto varje dag. Hur automatisera detta?**

Du kan använda AzCopy och skapa en uppgift i Schemaläggaren. Ladda upp filer till ett Azure storage-konto med hjälp av ett kommandoskript för AzCopy. Mer information finns i [hur du konfigurerar och kör Start-aktiviteter för en molntjänst](../../cloud-services/cloud-services-startup-tasks.md).

**Hur flytta mitt lagringskonto mellan prenumerationer?**

Använda AzCopy för att flytta ditt lagringskonto mellan prenumerationer. Mer information finns i [överföra data med AzCopy på Windows](storage-use-azcopy.md) och [överföra data med AzCopy på Linux](storage-use-azcopy-linux.md).

**Hur kan jag flytta 10 TB data till lagring på en annan region?**

Använd AzCopy för att flytta data. Mer information finns i [överföra data med AzCopy på Windows](storage-use-azcopy.md) och [överföra data med AzCopy på Linux](storage-use-azcopy-linux.md).

**Hur kopierar data från lokala till Azure storage?**

Använd AzCopy för att kopiera data. Mer information finns i [överföra data med AzCopy på Windows](storage-use-azcopy.md) och [överföra data med AzCopy på Linux](storage-use-azcopy-linux.md).

**Hur kan jag flytta data från lokala till Azures Filtjänst?**

Använd AzCopy för att flytta data. Mer information finns mer information finns i [överföra data med AzCopy på Windows](storage-use-azcopy.md) och [överföra data med AzCopy på Linux](storage-use-azcopy-linux.md).

**Hur jag för att mappa en behållare mapp på en virtuell dator?**

Använd Azure-filresursen.

**Hur säkerhetskopierar jag Azure file storage?**

Det finns ingen lösning för säkerhetskopiering. Azure-filer stöder dock asynkrona. Så du kan kopiera filer från en resurs till en annan resurs (inom ett lagringskonto eller till ett annat lagringskonto) eller till en blob-behållare (inom ett lagringskonto eller till ett annat lagringskonto).
Mer information finns i [överföra data med AzCopy på Windows](storage-use-azcopy.md).

**Hur flytta hanterade diskar till en annan storage-konto?**

Det gör du genom att följa dessa steg:

1.  Stoppa den virtuella datorn som den hantera disken är kopplad till.

2.  Kopiera den hantera disken VHD från ett område till en annan genom att köra följande Azure PowerShell-skript:

    ```
    Login-AzureRmAccount

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

**Hur kan jag hämta om 1 – 2 TB data från Azure portal?**

Använda AzCopy för att hämta data. Mer information finns i [överföra data med AzCopy på Windows](storage-use-azcopy.md) och [överföra data med AzCopy på Linux](storage-use-azcopy-linux.md).

**Hur ändrar den sekundära platsen till Europa region för ett lagringskonto?**

När du skapar ett lagringskonto, väljer du den primära regionen för kontot. Valet av den sekundära regionen som är baserad på den primära regionen och kan inte ändras. Se [Azure Storage-replikering](storage-redundancy.md).

**Var kan jag få mer information om Azure Storage Service kryptering (SSE)?**  
  
Se följande artiklar:

-  [Azure Storage-säkerhetsguiden](storage-security-guide.md)

-   [Azure Storage Service-kryptering av vilande Data](storage-service-encryption.md)

**Hur flytta eller hämta data från ett lagringskonto?**

Använda AzCopy för att hämta data. Mer information finns mer information finns i [överföra data med AzCopy på Windows](storage-use-azcopy.md) och [överföra data med AzCopy på Linux](storage-use-azcopy-linux.md).


**Hur jag för att kryptera data i ett lagringskonto?**

När du aktiverar kryptering på lagringskontot, krypteras inte befintliga data. För att kryptera befintliga data, måste du överföra igen data till lagringskontot.  Det gör du genom att följa dessa steg:

Använd AZcopy för att kopiera data till ett annat lagringskonto och sedan flytta tillbaka till ett lagringskonto. Du kan också använda [kryptering i vila](storage-service-encryption.md).

**Hur kan jag hämta en virtuell Hårddisk till en lokal dator, andra än med hjälp av alternativet hämtning på portalen**

Du kan använda [Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/) att ladda ned en virtuell Hårddisk.

**Finns det några krav för att ändra replikering av ett lagringskonto från GRS till LRS?**

Nej. 

**Hur kommer jag åt redundant lagring med Azure-filer?**

Geo-Redundant lagring med läsbehörighet (RA-GRS) krävs för att komma åt redundant lagring. Azure Files stöder dock bara LRS och standard GRS som inte tillåter läsbehörighet. 

**Hur flyttar från Premium-lagring till standardlagring?**

Det gör du genom att följa dessa steg:

1.  Skapa ett nytt standardlagringskonto (eller du kan använda ett befintligt standardlagringskonto i din prenumeration).

2.  Hämta AzCopy. Kör något av följande kommandon för AzCopy.
      
    Kopiera hela diskar i storage-konto:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    Om du vill kopiera bara en disk, ange namnet på disken i mönstret

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
Det kan ta flera timmar att slutföra åtgärden.

Om du vill kontrollera att överföringen har slutförts kan undersöka du målbehållare för storage-konto i Azure-portalen. När diskarna kopieras till standardlagringskontot, kan du ansluta dem till den virtuella datorn som en befintlig disk. Mer information finns i [hur du kopplar en datadisk hanterade till en Windows-dator i Azure portal](../../virtual-machines/windows/attach-managed-disk-portal.md).  
  
**Hur konverterar till Premium-lagring för en filresurs?**

Premium-lagring är inte tillåtet på Azure-filresurs.

**Hur uppgraderar jag från en standardlagring till en Premium Storage-konto? Hur jag Nedgradera från en Premium-lagring till ett standardlagringskonto?**

- Du måste skapa lagringskontot mål, kopiera data från källplatsens konto till mål-konto och ta bort kontot källa.

- Du kan använda ett verktyg som AzCopy för att kopiera data.

- Om du även har virtuella datorer det finns flera ytterligare steg som du måste utföra innan du migrerar data för storage-konto. Mer information finns i [migrera till Azure Premium-lagring (ohanterade diskar)](storage-migration-to-premium-storage.md).

**Hur flyttar från ett klassiska storage-konto till en Azure Resource Manager Storage-konto?**

1.  Du kan använda cmdleten Move-AzureStorageAccount.

2.  Denna cmdlet har flera steg (Validera Prepare, Commit) och du kan kontrollera flyttningen innan du utför den.

3.  Om du även har virtuella datorer finns det flera ytterligare steg du måste utföra innan du migrerar data för storage-konto. Mer information finns i [migrera IaaS-resurser från klassiska till Azure Resource Manager med hjälp av Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Hur jag hämta data till en Linux-baserade dator från ett Azure storage-konto eller överföra data från en Linux-dator?**

Du kan använda Azure CLI.

-   Hämta en enda blob

        azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

-   Överför en enda blob: 

        azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Hur kan jag ge andra användare åtkomst till min lagringsresurser?**

Ge andra användare åtkomst till lagringsresurser:

-   Använda en delad signatur åtkomst (SAS)-token för att ge åtkomst till en resurs. 

-   Ange en användare med den primära och sekundära nycklarna för lagringskontot. Mer information finns i [hantera ditt lagringskonto](storage-create-storage-account.md#manage-your-storage-account).

-   Ändra åtkomstprincipen för att tillåta anonym åtkomst. Mer information finns i [bevilja anonyma användare behörighet till behållare och blobbar](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Var installeras AzCopy?**

-   Om du öppnar AzCopy från ”Microsoft Azure Storage kommandoraden” Skriv AzCopy. Kommandoraden är installerat tillsammans med AzCopy.

-   Om du har installerat 32-bitars version blir finns här: **% ProgramFiles(x86) %\\Microsoft SDKs\\Azure\\AzCopy.**

-   Om du har installerat 64-bitarsversionen blir finns här: **% ProgramFiles %\\Microsoft SDKs\\Azure\\AzCopy**.

**För en replikerad lagringskonto (till exempel ZRS-, GRS- eller RA-GRS), hur kommer jag åt data som lagras i den sekundära regionen?**

-   Om du använder Zonredundant lagring (ZRS) eller Geo-Redundant lagring (GRS) kan du komma åt data från den sekundära regionen såvida inte en växling vid fel. Mer information om processen för växling vid fel finns [vad som händer om det uppstår redundans lagring](storage-disaster-recovery-guidance.md#what-to-expect-if-a-storage-failover-occurs).

-   Om du använder Geo-Redundant lagring med läsbehörighet (**RA-GRS**), du kan komma åt data från den sekundära regionen när som helst. Om du vill göra detta använder du någon av följande metoder:  
      
    AzCopy: Lägg till '-sekundär ' till Lagringskontot namn i URL: en för den sekundära slutpunkten. Exempel:  
     
    https://storageaccountname-Secondary.BLOB.Core.Windows.NET/vhds/BlobName.VHD

    SAS-Token: använda en SAS-token för att komma åt data från slutpunkten. Mer information finns i [använder signaturer för delad åtkomst (SAS)](storage-dotnet-shared-access-signature-part-1.md).

**Hur använder en anpassad domän för HTTPS med mitt Lagringskonto? Till exempel hur gör jag ”https://mystorageaccountname.blob.core.windows.net/images/image.gif” visas som ”https://www.contoso.com/images/image.gif”?**

SSL stöds inte för närvarande på Storage-konton med anpassade domäner.
Men du kan använda icke-HTTPS anpassade domäner. Mer information finns i [konfigurera ett anpassat domännamn för din slutpunkt för Blob-lagring](../blobs/storage-custom-domain-name.md).

**Hur använder jag FTP för att komma åt data som är i ett Lagringskonto?**

Det går inte att komma åt ett storage-konto direkt med hjälp av FTP. Du kan dock konfigurera en virtuell Azure-dator och sedan installera en FTP-server på den virtuella datorn. Du kan ha FTP-servern lagrar filer i en resurs i Azure-filer eller i en datadisk som är tillgänglig för den virtuella datorn.
Om du bara vill ladda ned data utan att använda Lagringsutforskaren eller ett liknande program, kanske de kan använda en SAS-token. Mer information finns i [använder signaturer för delad åtkomst (SAS)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande behöver hjälp [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
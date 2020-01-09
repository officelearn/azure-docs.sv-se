---
title: Migrera virtuella datorer till Azure Premium Storage | Microsoft Docs
description: Migrera dina befintliga virtuella datorer till Azure Premium Storage. Premium Storage erbjuder disk support med hög prestanda och låg latens för I/O-intensiva arbets belastningar som körs på Azure Virtual Machines.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.reviewer: yuemlu
ms.subservice: common
ms.openlocfilehash: b8b3679676cf019a48c55211d81bee0523764db5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351242"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migrera till Azure Premium Storage (ohanterade diskar)

> [!NOTE]
> Den här artikeln beskriver hur du migrerar en virtuell dator som använder ohanterade standard diskar till en virtuell dator som använder ohanterade Premium diskar. Vi rekommenderar att du använder Azure Managed Disks för nya virtuella datorer och att du konverterar tidigare ohanterade diskar till hanterade diskar. Managed Disks hanterar de underliggande lagrings kontona så att du inte behöver det. Mer information finns i vår [Managed disks översikt](../../virtual-machines/windows/managed-disks-overview.md).
>

Azure Premium Storage ger stöd för hög prestanda och låg latens disk för virtuella datorer som kör I/O-intensiva arbets belastningar. Du kan dra nytta av hastigheten och prestandan för diskarna genom att migrera programmets VM-diskar till Azure Premium Storage.

Syftet med den här guiden är att hjälpa nya användare av Azure Premium Storage att förbereda en smidig över gång från det aktuella systemet till Premium Storage. Guiden behandlar tre viktiga komponenter i den här processen:

* [Planera för migreringen till Premium Storage](#plan-the-migration-to-premium-storage)
* [Förbereda och kopiera virtuella hård diskar (VHD) till Premium Storage](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Skapa en virtuell Azure-dator med hjälp av Premium Storage](#create-azure-virtual-machine-using-premium-storage)

Du kan antingen migrera virtuella datorer från andra plattformar till Azure Premium Storage eller migrera befintliga virtuella Azure-datorer från standard lagring till Premium Storage. Den här guiden beskriver steg för båda två scenarier. Följ de steg som anges i relevant avsnitt beroende på ditt scenario.

> [!NOTE]
> Du hittar en funktions översikt och priser för Premium-SSD i: [Välj en disktyp för virtuella IaaS-datorer](../../virtual-machines/windows/disks-types.md#premium-ssd). Vi rekommenderar att du migrerar alla virtuella dator diskar som kräver höga IOPS till Azure Premium Storage för bästa prestanda för ditt program. Om disken inte kräver hög IOPS kan du begränsa kostnaderna genom att underhålla den i standard lagring, som lagrar disk data för virtuella datorer på hård diskar (HDD) i stället för SSD.
>

Att slutföra migreringsprocessen i sin helhet kan kräva ytterligare åtgärder både före och efter de steg som beskrivs i den här hand boken. Exempel på detta är att konfigurera virtuella nätverk eller slut punkter eller göra kod ändringar i själva programmet, vilket kan kräva avbrott i ditt program. Dessa åtgärder är unika för varje program och du bör slutföra dem tillsammans med de steg som beskrivs i den här hand boken för att göra hela över gången till Premium Storage så sömlöst som möjligt.

## <a name="plan-the-migration-to-premium-storage"></a>Planera för migreringen till Premium Storage
Det här avsnittet säkerställer att du är redo att följa stegen i migreringen i den här artikeln och hjälper dig att fatta det bästa beslutet om VM och disk typer.

### <a name="prerequisites"></a>Krav
* Du behöver en Azure-prenumeration. Om du inte har någon kan du skapa en månads [kostnads fri utvärderings](https://azure.microsoft.com/pricing/free-trial/) prenumeration eller gå till [Azure-priser](https://azure.microsoft.com/pricing/) för fler alternativ.
* För att köra PowerShell-cmdlets behöver du Microsoft Azure PowerShell-modulen. Information om installationsplatser och installationsanvisningar finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).
* När du planerar att använda virtuella Azure-datorer som körs på Premium Storage måste du använda Premium Storage-kompatibla virtuella datorer. Du kan använda både standard-och Premium Storage diskar med Premium Storage-kompatibla virtuella datorer. Premium Storage-diskar är tillgängliga med fler VM-typer i framtiden. Mer information om alla tillgängliga disk typer och storlekar för virtuella Azure-datorer finns i [storlekar för virtuella datorer](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) och [storlekar för Cloud Services](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Överväganden
En virtuell Azure-dator har stöd för att bifoga flera Premium Storage diskar så att dina program kan ha upp till 256 TB lagrings utrymme per virtuell dator. Med Premium Storage kan dina program uppnå 80 000 IOPS (in-/utdata-åtgärder per sekund) per virtuell dator och 2000 MB per sekund disk data flöde per virtuell dator med extremt låg fördröjning för Läs åtgärder. Du har alternativ i en mängd olika virtuella datorer och diskar. Det här avsnittet är för att hjälpa dig hitta ett alternativ som passar din arbets belastning bäst.

#### <a name="vm-sizes"></a>VM-storlekar
Specifikationerna för Azure VM-storlek anges i [storlekar för virtuella datorer](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Granska prestanda egenskaperna för virtuella datorer som fungerar med Premium Storage och välj den lämpligaste VM-storlek som passar din arbets belastning bäst. Kontrol lera att det finns tillräckligt med bandbredd på den virtuella datorn för att köra disk trafiken.

#### <a name="disk-sizes"></a>Diskstorlekar
Det finns fem typer av diskar som kan användas med den virtuella datorn och var och en har vissa begränsningar för IOPs och data flöde. Ta hänsyn till dessa begränsningar när du väljer disk typen för den virtuella datorn baserat på programmets behov vad gäller kapacitet, prestanda, skalbarhet och högsta belastning.

| Typ av Premium diskar  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Diskstorlek           | 128 GB| 512 GB| 1 024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| IOPS per disk       | 500   | 2 300  | 5000           | 7500           | 7500           | 
| Dataflöde per disk | 100 MB per sekund | 150 MB per sekund | 200 MB per sekund | 250 MB per sekund | 250 MB per sekund |

Beroende på arbets belastningen avgör du om ytterligare data diskar krävs för den virtuella datorn. Du kan koppla flera beständiga data diskar till din virtuella dator. Om det behövs kan du Stripa över diskarna för att öka volymens kapacitet och prestanda. (Se vad är disk ränder [här](../../virtual-machines/windows/premium-storage-performance.md#disk-striping).) Om du stripar Premium Storage data diskar med [lagrings utrymmen][4]bör du konfigurera den med en kolumn för varje disk som används. Annars kan den utsträckta volymens övergripande prestanda vara lägre än förväntat på grund av ojämn fördelning av trafik över diskarna. För virtuella Linux-datorer kan du använda verktyget *mdadm* för att uppnå samma. Mer information finns i artikeln [Konfigurera programvaru-RAID på Linux](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

#### <a name="storage-account-scalability-targets"></a>Lagrings kontots skalbarhets mål
Premium Storage-konton har följande skalbarhets mål utöver [Azure Storage skalbarhets-och prestanda mål](storage-scalability-targets.md). Om dina program krav överskrider skalbarhets målen för ett enda lagrings konto skapar du programmet för att använda flera lagrings konton och partitionerar dina data över dessa lagrings konton.

| Total konto kapacitet | Total bandbredd för lokalt redundant lagrings konto |
|:--- |:--- |
| Disk kapacitet: 35TB<br />Ögonblicks bild kapacitet: 10 TB |Upp till 50 gigabit per sekund för inkommande och utgående |

Mer information om Premium Storage specifikationer finns i [skalbarhets mål för Premium Page Blob Storage-konton](../blobs/scalability-targets-premium-page-blobs.md).

#### <a name="disk-caching-policy"></a>Princip för diskcachelagring
Som standard är diskcachelagring *-principen skrivskyddad* för alla Premium-datadiskarna och *Läs-och skriv* behörighet för den Premium-operativsystems disk som är ansluten till den virtuella datorn. Den här konfigurations inställningen rekommenderas för att uppnå optimala prestanda för ditt programs IOs. För skrivskyddade eller skrivskyddade data diskar (till exempel SQL Server loggfiler) inaktiverar du diskcachelagring så att du kan uppnå bättre program prestanda. Cache-inställningarna för befintliga data diskar kan uppdateras med hjälp av [Azure Portal](https://portal.azure.com) -eller parametern *-HostCaching* i cmdleten *set-AzureDataDisk* .

#### <a name="location"></a>Location
Välj en plats där Azure Premium Storage är tillgängligt. Se [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services) för uppdaterad information om tillgängliga platser. Virtuella datorer som finns i samma region som det lagrings konto som lagrar diskarna för den virtuella datorn ger mycket bättre prestanda än om de är i olika regioner.

#### <a name="other-azure-vm-configuration-settings"></a>Andra konfigurations inställningar för Azure VM
När du skapar en virtuell Azure-dator uppmanas du att konfigurera vissa inställningar för virtuella datorer. Kom ihåg att några inställningar har åtgärd ATS för den virtuella datorns livstid, medan du kan ändra eller lägga till andra senare. Granska de här konfigurations inställningarna för Azure VM och se till att de konfigureras på rätt sätt för att matcha dina arbets belastnings krav.

### <a name="optimization"></a>Optimering
[Azure Premium Storage: design för höga prestanda](../../virtual-machines/windows/premium-storage-performance.md) ger rikt linjer för att skapa högpresterande program med Azure Premium Storage. Du kan följa rikt linjerna tillsammans med metod tips för prestanda som gäller för tekniker som används av ditt program.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Förbereda och kopiera virtuella hård diskar (VHD) till Premium Storage
Följande avsnitt innehåller rikt linjer för att förbereda virtuella hård diskar från din virtuella dator och att kopiera VHD: er till Azure Storage.

* [Scenario 1: "jag migrerar befintliga virtuella Azure-datorer till Azure Premium Storage."](#scenario1)
* [Scenario 2: "jag migrerar virtuella datorer från andra plattformar till Azure Premium Storage."](#scenario2)

### <a name="prerequisites"></a>Krav
För att förbereda de virtuella hård diskarna för migrering behöver du:

* En Azure-prenumeration, ett lagrings konto och en behållare i lagrings kontot som du kan kopiera din virtuella hård disk till. Observera att mål lagrings kontot kan vara ett standard-eller Premium Storage konto beroende på ditt krav.
* Ett verktyg för att generalisera den virtuella hård disken om du planerar att skapa flera VM-instanser från den. Till exempel Sysprep för Windows eller virt-Sysprep för Ubuntu.
* Ett verktyg för att överföra VHD-filen till lagrings kontot. Mer information finns i [överföra data med kommando rads verktyget AzCopy](storage-use-azcopy.md) eller använda en [Azure Storage Explorer](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Den här guiden beskriver hur du kopierar din virtuella hård disk med AzCopy-verktyget.

> [!NOTE]
> Om du väljer alternativet synkron kopiering med AzCopy, för bästa prestanda, kopierar du den virtuella hård disken genom att köra något av dessa verktyg från en virtuell Azure-dator som finns i samma region som mål lagrings kontot. Om du kopierar en virtuell hård disk från en virtuell Azure-dator i en annan region kan prestandan gå långsammare.
>
> Om du vill kopiera en stor mängd data över begränsad bandbredd bör du överväga att [använda Azure import/export-tjänsten för att överföra data till Blob Storage](../storage-import-export-service.md). på så sätt kan du överföra dina data genom att leverera hård diskar till ett Azure-datacenter. Du kan bara använda Azure import/export-tjänsten för att kopiera data till ett standard lagrings konto. När data finns på ditt standard lagrings konto kan du använda antingen [copy BLOB API](https://msdn.microsoft.com/library/azure/dd894037.aspx) eller AzCopy för att överföra data till ditt Premium Storage-konto.
>
> Observera att Microsoft Azure endast stöder VHD-filer med fast storlek. VHDX-filer eller dynamiska virtuella hård diskar stöds inte. Om du har en dynamisk virtuell hård disk kan du konvertera den till en fast storlek med hjälp av cmdleten [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) .
>
>

### <a name="scenario1"></a>Scenario 1: "jag migrerar befintliga virtuella Azure-datorer till Azure Premium Storage."
Om du migrerar befintliga virtuella Azure-datorer stoppar du den virtuella datorn, förbereder virtuella hård diskar per den typ av virtuell hård disk du vill ha och kopierar sedan den virtuella hård disken med AzCopy eller PowerShell.

Den virtuella datorn måste vara helt avstängd för att kunna migrera ett rent tillstånd. Det kommer att finnas en nedtid tills migreringen har slutförts.

#### <a name="step-1-prepare-vhds-for-migration"></a>Steg 1. Förbered virtuella hård diskar för migrering
Om du migrerar befintliga virtuella Azure-datorer till Premium Storage kan din virtuella hård disk vara:

* En generaliserad operativ Systems avbildning
* En unik operativ system disk
* En datadisk

Nedan går vi igenom de här tre scenarierna för att förbereda din virtuella hård disk.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Använd en generaliserad operativ systemets virtuella hård disk för att skapa flera VM-instanser
Om du överför en virtuell hård disk som ska användas för att skapa flera virtuella Azure VM-instanser måste du först generalisera virtuell hård disk med ett Sysprep-verktyg. Detta gäller för en virtuell hård disk som är lokal eller i molnet. Sysprep tar bort all datorspecifik information från den virtuella hård disken.

> [!IMPORTANT]
> Ta en ögonblicks bild eller säkerhetskopiera den virtuella datorn innan du generaliserar den. Genom att köra Sysprep stoppas och friallokeras den virtuella dator instansen. Följ stegen nedan för att Sysprep a Windows OS VHD. Observera att om du kör Sysprep-kommandot måste du stänga av den virtuella datorn. Mer information om Sysprep finns i [Översikt över Sysprep](https://technet.microsoft.com/library/hh825209.aspx) eller [teknisk referens för Sysprep](https://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Öppna ett kommando tolks fönster som administratör.
2. Ange följande kommando för att öppna Sysprep:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. I system förberedelse verktyget väljer du ange systemfunktions upplevelse (OOBE), markerar kryss rutan generalisera, väljer **Stäng**av och klickar sedan på **OK**, som du ser i bilden nedan. Sysprep kommer att generalisera operativ systemet och stänga av systemet.

    ![][1]

För en virtuell Ubuntu-dator använder du virt-Sysprep för att uppnå samma. Mer information finns i [virt-Sysprep](https://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) . Se även en del av [Linux server Provisioning-programvaran](https://www.cyberciti.biz/tips/server-provisioning-software.html) med öppen källkod för andra Linux-operativsystem.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Använd ett unikt operativ systemets virtuella hård disk för att skapa en enskild VM-instans
Om du har ett program som körs på den virtuella datorn som kräver datorspecifika data, generaliserar du inte den virtuella hård disken. En icke-generaliserad virtuell hård disk kan användas för att skapa en unik Azure VM-instans. Om du till exempel har en domänkontrollant på den virtuella hård disken, kommer körning av Sysprep att göra den inaktive rad som en domänkontrollant. Granska programmen som körs på den virtuella datorn och effekten av att köra Sysprep på dem innan du generaliserar den virtuella hård disken.

##### <a name="register-data-disk-vhd"></a>Registrera data disk-VHD
Om du har data diskar i Azure som ska migreras måste du se till att de virtuella datorerna som använder dessa data diskar är avstängda.

Följ stegen som beskrivs nedan för att kopiera en virtuell hård disk till Azure Premium Storage och registrera den som en etablerad data disk.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Steg 2. Skapa målet för din virtuella hård disk
Skapa ett lagrings konto för att underhålla dina virtuella hård diskar. Tänk på följande när du planerar var du vill lagra dina virtuella hård diskar:

* Mål Premium Storage-kontot.
* Lagrings kontots plats måste vara samma som Premium Storage kompatibla virtuella Azure-datorer som du skapar i det sista steget. Du kan kopiera till ett nytt lagrings konto eller planera att använda samma lagrings konto baserat på dina behov.
* Kopiera och spara lagrings konto nyckeln för mål lagrings kontot i nästa steg.

För data diskar kan du välja att behålla vissa data diskar på ett standard lagrings konto (t. ex. diskar med stark lagring), men vi rekommenderar starkt att du flyttar alla data för produktions arbets belastningen för att använda Premium Storage.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Steg 3. Kopiera virtuell hård disk med AzCopy eller PowerShell
Du måste hitta behållar Sök vägen och lagrings konto nyckeln för att kunna bearbeta något av dessa två alternativ. Du hittar container Sök väg och lagrings konto nyckel i **Azure Portal** > **Storage**. Behållar-URL: en är som "https:\//myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Alternativ 1: kopiera en virtuell hård disk med AzCopy (asynkron kopia)
Med hjälp av AzCopy kan du enkelt ladda upp den virtuella hård disken via Internet. Detta kan ta tid beroende på storleken på de virtuella hård diskarna. Kom ihåg att kontrol lera lagrings kontots ingångs-eller utgående gränser när du använder det här alternativet. Mer information finns i [Azure Storage skalbarhets-och prestanda mål](storage-scalability-targets.md) .

1. Hämta och installera AzCopy härifrån: [senaste versionen av AzCopy](https://aka.ms/downloadazcopy)
2. Öppna Azure PowerShell och gå till mappen där AzCopy är installerad.
3. Använd följande kommando för att kopiera VHD-filen från källa till mål.

   ```azcopy
   AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

    Exempel:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```
 
   Här följer beskrivningar av de parametrar som används i AzCopy-kommandot:

   * **/Source:** _&lt;käll&gt;:_ platsen för mappen eller lagrings behållarens URL som innehåller den virtuella hård disken.
   * **/SourceKey:** _&lt;käll konto nyckel&gt;:_ lagrings konto nyckeln för käll lagrings kontot.
   * **/Dest:** _&lt;mål&gt;:_ URL för lagrings behållare för att kopiera den virtuella hård disken till.
   * **/DestKey:** _&lt;mål konto nyckel&gt;:_ lagrings konto nyckeln för mål lagrings kontot.
   * **/Pattern:** _&lt;fil namn&gt;:_ ange fil namnet på den virtuella hård disk som ska kopieras.

Mer information om hur du använder AzCopy-verktyget finns i [överföra data med kommando rads verktyget AzCopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Alternativ 2: kopiera en virtuell hård disk med PowerShell (synkroniserad kopia)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Du kan också kopiera VHD-filen med PowerShell-cmdleten Start-AzStorageBlobCopy. Använd följande kommando på Azure PowerShell för att kopiera VHD. Ersätt värdena i < > med motsvarande värden från ditt käll-och mål lagrings konto. Om du vill använda det här kommandot måste du ha en behållare som kallas VHD: er på ditt mål lagrings konto. Om behållaren inte finns skapar du en innan du kör kommandot.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

Exempel:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario2"></a>Scenario 2: "jag migrerar virtuella datorer från andra plattformar till Azure Premium Storage."
Om du migrerar en virtuell hård disk från en icke-Azure-moln lagring till Azure måste du först exportera den virtuella hård disken till en lokal katalog. Ha fullständig käll Sök väg till den lokala katalogen där VHD är lagrat praktiskt och Använd sedan AzCopy för att ladda upp den till Azure Storage.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Steg 1. Exportera virtuell hård disk till en lokal katalog
##### <a name="copy-a-vhd-from-aws"></a>Kopiera en virtuell hård disk från AWS
1. Om du använder AWS exporterar du EC2-instansen till en virtuell hård disk i en Amazon S3-Bucket. Följ stegen som beskrivs i Amazon-dokumentationen för att exportera Amazon EC2-instanser för att installera verktyget Amazon EC2 kommando rads gränssnitt (CLI) och köra kommandot CREATE-instance-export-Task för att exportera EC2-instansen till en VHD-fil. Se till att använda **VHD** för disk&#95;image&#95;format-variabeln när du kör kommandot **create-instance-export-Task** . Den exporterade VHD-filen sparas i den Amazon S3-Bucket som du anger under den processen.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Ladda ned VHD-filen från S3-Bucket. Välj VHD-filen och sedan **åtgärder** > **Ladda ned**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Kopiera en virtuell hård disk från andra icke-Azure-moln
Om du migrerar en virtuell hård disk från en icke-Azure-moln lagring till Azure måste du först exportera den virtuella hård disken till en lokal katalog. Kopiera hela käll Sök vägen för den lokala katalog där VHD lagras.

##### <a name="copy-a-vhd-from-on-premises"></a>Kopiera en virtuell hård disk lokalt
Om du migrerar en virtuell hård disk från en lokal miljö behöver du den fullständiga käll Sök vägen där VHD lagras. Käll Sök vägen kan vara en server plats eller en fil resurs.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Steg 2. Skapa målet för din virtuella hård disk
Skapa ett lagrings konto för att underhålla dina virtuella hård diskar. Tänk på följande när du planerar var du vill lagra dina virtuella hård diskar:

* Mål lagrings kontot kan vara standard-eller Premium-lagring beroende på ditt program krav.
* Lagrings konto regionen måste vara samma som Premium Storage kompatibla virtuella Azure-datorer som du skapar i det sista steget. Du kan kopiera till ett nytt lagrings konto eller planera att använda samma lagrings konto baserat på dina behov.
* Kopiera och spara lagrings konto nyckeln för mål lagrings kontot i nästa steg.

Vi rekommenderar starkt att du flyttar alla data för produktions belastningen för att använda Premium Storage.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Steg 3. Överför den virtuella hård disken till Azure Storage
Nu när du har en virtuell hård disk i den lokala katalogen kan du använda AzCopy eller AzurePowerShell för att ladda upp VHD-filen till Azure Storage. Båda alternativen finns här:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Alternativ 1: Använd Azure PowerShell Add-AzureVhd för att ladda upp VHD-filen

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Ett exempel \<Uri > kan vara **_”https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd”_** . Ett exempel \<FileInfo > kan vara **_”C:\path\to\upload.vhd”_** .

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Alternativ 2: använda AzCopy för att ladda upp VHD-filen
Med hjälp av AzCopy kan du enkelt ladda upp den virtuella hård disken via Internet. Detta kan ta tid beroende på storleken på de virtuella hård diskarna. Kom ihåg att kontrol lera lagrings kontots ingångs-eller utgående gränser när du använder det här alternativet. Mer information finns i [Azure Storage skalbarhets-och prestanda mål](storage-scalability-targets.md) .

1. Hämta och installera AzCopy härifrån: [senaste versionen av AzCopy](https://aka.ms/downloadazcopy)
2. Öppna Azure PowerShell och gå till mappen där AzCopy är installerad.
3. Använd följande kommando för att kopiera VHD-filen från källa till mål.

   ```azcopy
      AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

   Exempel:

   ```azcopy
      AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
   ```

   Här följer beskrivningar av de parametrar som används i AzCopy-kommandot:

   * **/Source:** _&lt;käll&gt;:_ platsen för mappen eller lagrings behållarens URL som innehåller den virtuella hård disken.
   * **/SourceKey:** _&lt;käll konto nyckel&gt;:_ lagrings konto nyckeln för käll lagrings kontot.
   * **/Dest:** _&lt;mål&gt;:_ URL för lagrings behållare för att kopiera den virtuella hård disken till.
   * **/DestKey:** _&lt;mål konto nyckel&gt;:_ lagrings konto nyckeln för mål lagrings kontot.
   * **/BlobType: sida:** Anger att målet är en sid-blob.
   * **/Pattern:** _&lt;fil namn&gt;:_ ange fil namnet på den virtuella hård disk som ska kopieras.

Mer information om hur du använder AzCopy-verktyget finns i [överföra data med kommando rads verktyget AzCopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Andra alternativ för att ladda upp en virtuell hård disk
Du kan också ladda upp en virtuell hård disk till ditt lagrings konto på något av följande sätt:

* [Azure Storage Copy BLOB API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Azure Storage Explorer uppladdning av blobbar](https://azurestorageexplorer.codeplex.com/)
* [REST API referens för lagrings import/export-tjänsten](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Vi rekommenderar att du använder import/export-tjänsten om den uppskattade uppladdnings tiden är längre än 7 dagar. Du kan använda [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) för att beräkna tiden från data storlek och överförings enhet.
>
> Import/export kan användas för att kopiera till ett standard lagrings konto. Du måste kopiera från standard lagring till Premium Storage-kontot med ett verktyg som AzCopy.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Skapa virtuella Azure-datorer med Premium Storage
När den virtuella hård disken har laddats upp eller kopierats till det önskade lagrings kontot följer du anvisningarna i det här avsnittet för att registrera den virtuella hård disken som en operativ system avbildning eller OS-disk beroende på ditt scenario och sedan skapa en virtuell dator instans från den. Data diskens virtuella hård disk kan kopplas till den virtuella datorn när den har skapats.
Ett exempel på ett migreringsarkiv anges i slutet av det här avsnittet. Det här enkla skriptet matchar inte alla scenarier. Du kan behöva uppdatera skriptet så att det överensstämmer med ditt speciella scenario. Om du vill se om det här skriptet gäller för ditt scenario, se under [ett exempel på ett migreringsjobb](#a-sample-migration-script).

### <a name="checklist"></a>Checklista
1. Vänta tills alla VHD-diskar som har kopierats är slutförda.
2. Se till att Premium Storage är tillgänglig i den region som du migrerar till.
3. Välj den nya serien med virtuella datorer som du kommer att använda. Det bör vara en Premium Storage kapabel och storleken bör vara beroende av tillgängligheten i regionen och utifrån dina behov.
4. Bestäm den exakta VM-storlek som du ska använda. Storleken på den virtuella datorn måste vara tillräckligt stor för att stödja antalet data diskar som du har. T.ex. Om du har 4 data diskar måste den virtuella datorn ha två eller flera kärnor. Överväg också att bearbeta behov av strömförsörjning, minne och nätverks bandbredd.
5. Skapa ett Premium Storage-konto i mål regionen. Detta är det konto som du ska använda för den nya virtuella datorn.
6. Ha den aktuella VM-informationen praktisk, inklusive listan över diskar och motsvarande VHD-blobar.

Förbered ditt program för stillestånds tid. Om du vill göra en ren migrering måste du stoppa all bearbetning i det aktuella systemet. Det är bara du som kan ge den ett konsekvent tillstånd som du kan migrera till den nya plattformen. Stillestånds tiden beror på mängden data i diskarna som ska migreras.

> [!NOTE]
> Om du skapar en Azure Resource Manager virtuell dator från en specialiserad VHD-disk går du till [den här mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) för att distribuera den virtuella Resource Manager-datorn med hjälp av en befintlig disk.
>
>

### <a name="register-your-vhd"></a>Registrera din virtuella hård disk
Om du vill skapa en virtuell dator från OS VHD eller koppla en datadisk till en ny virtuell dator måste du först registrera dem. Följ stegen nedan beroende på din virtuella hård disks scenario.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generaliserat operativ systemets virtuella hård disk för att skapa flera virtuella Azure-instanser
När generaliserad OS-avbildningen har laddats upp till lagrings kontot registrerar du den som en **Azure VM-avbildning** så att du kan skapa en eller flera virtuella dator instanser från den. Använd följande PowerShell-cmdlets för att registrera din virtuella hård disk som en Azure VM OS-avbildning. Ange den fullständiga URL: en för behållaren där VHD har kopierats till.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Kopiera och spara namnet på den här nya Azure VM-avbildningen. I exemplet ovan är det *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Unikt operativ systemets virtuella hård disk för att skapa en enskild virtuell Azure-instans
När den unika OS-VHD: n har överförts till lagrings kontot registrerar du den som en **Azure OS-disk** så att du kan skapa en virtuell dator instans från den. Använd dessa PowerShell-cmdletar för att registrera din virtuella hård disk som en Azure OS-disk. Ange den fullständiga URL: en för behållaren där VHD har kopierats till.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Kopiera och spara namnet på den nya Azure OS-disken. I exemplet ovan är det *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Datadisk-VHD som ska anslutas till nya Azure VM-instanser
När den virtuella hård disken har laddats upp till lagrings kontot registrerar du den som en Azure-datadisk så att den kan kopplas till din nya DS-, DSv2-eller GS-serien Azure VM-instans.

Använd dessa PowerShell-cmdletar för att registrera din virtuella hård disk som en Azure-datadisk. Ange den fullständiga URL: en för behållaren där VHD har kopierats till.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Kopiera och spara namnet på den nya Azure-datadisken. I exemplet ovan är det *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Skapa en Premium Storage-kompatibel virtuell dator
När operativ system avbildningen eller OS-disken har registrerats skapar du en ny virtuell dator i DS-serien, DSv2-serien eller GS-serien. Du kommer att använda operativ system avbildningen eller disk namnet för operativ systemet som du har registrerat. Välj den virtuella dator typen från Premium Storage nivån. I exemplet nedan använder vi *Standard_DS2* VM-storlek.

> [!NOTE]
> Uppdatera disk storleken för att kontrol lera att den överensstämmer med dina kapacitets-och prestanda krav och de tillgängliga Azure-disk storlekarna.
>
>

Följ de stegvisa PowerShell-cmdletarna nedan för att skapa den nya virtuella datorn. Ange först de gemensamma parametrarna:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Börja med att skapa en moln tjänst där du kommer att vara värd för de nya virtuella datorerna.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Sedan kan du, beroende på ditt scenario, skapa Azure VM-instansen från antingen den OS-avbildning eller OS-disk som du har registrerat.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generaliserat operativ systemets virtuella hård disk för att skapa flera virtuella Azure-instanser
Skapa en eller flera nya VM-instanser för Azure-serien med hjälp av den **Azure OS-avbildning** som du har registrerat. Ange namnet på operativ system avbildningen i VM-konfigurationen när du skapar en ny virtuell dator enligt nedan.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Unikt operativ systemets virtuella hård disk för att skapa en enskild virtuell Azure-instans
Skapa en ny instans av Azure VM-serien i Azure med hjälp av den **Azure OS-disk** som du har registrerat. Ange det här operativ systemets disk namn i VM-konfigurationen när du skapar den nya virtuella datorn som visas nedan.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Ange övrig information om virtuella Azure-datorer, till exempel en moln tjänst, region, lagrings konto, tillgänglighets uppsättning och princip för cachelagring. Observera att den virtuella dator instansen måste vara samordnad med tillhör ande operativ system eller data diskar, så att den valda moln tjänsten, region och lagrings kontot måste finnas på samma plats som de underliggande diskarna.

### <a name="attach-data-disk"></a>Anslut en datadisk
Slutligen, om du har registrerat datadisk-VHD: ar, kopplar du dem till den nya Premium Storage kompatibla virtuella Azure-datorer.

Använd följande PowerShell-cmdlet för att koppla datadisk till den nya virtuella datorn och ange principen för cachelagring. I exemplet nedan är inställningen för cachelagring inställd på *ReadOnly*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Det kan finnas ytterligare steg som krävs för att stödja ditt program som inte omfattas av den här guiden.
>
>

### <a name="checking-and-plan-backup"></a>Kontrollerar och planerar säkerhets kopiering
När den nya virtuella datorn är igång är åtkomst till den med samma inloggnings-ID och lösen ord som den ursprungliga virtuella datorn och kontrol lera att allt fungerar som förväntat. Alla inställningar, inklusive stripe-volymer, finns i den nya virtuella datorn.

Det sista steget är att planera säkerhets kopierings-och underhålls schema för den nya virtuella datorn baserat på programmets behov.

### <a name="a-sample-migration-script"></a>Ett exempel på ett migrations skript
Om du har flera virtuella datorer som ska migreras, kommer automation via PowerShell-skript att vara till hjälp. Följande är ett exempel skript som automatiserar migreringen av en virtuell dator. Observera att skriptet nedan bara är ett exempel och det finns några antaganden om de aktuella VM-diskarna. Du kan behöva uppdatera skriptet så att det överensstämmer med ditt speciella scenario.

Antagandena är:

* Du skapar klassiska virtuella Azure-datorer.
* Käll-OS-diskarna och käll data diskarna finns i samma lagrings konto och samma behållare. Om dina operativ system diskar och data diskar inte finns på samma plats kan du använda AzCopy eller Azure PowerShell för att kopiera virtuella hård diskar över lagrings konton och behållare. Se föregående steg: [Kopiera virtuell hård disk med AZCopy eller PowerShell](#copy-vhd-with-azcopy-or-powershell). Att redigera skriptet så att det passar ditt scenario är ett annat alternativ, men vi rekommenderar att du använder AzCopy eller PowerShell eftersom det är enklare och snabbare.

Automation-skriptet anges nedan. Ersätt text med din information och uppdatera skriptet så att det överensstämmer med ditt speciella scenario.

> [!NOTE]
> Om du använder det befintliga skriptet bevaras inte nätverks konfigurationen för den virtuella käll datorn. Du måste återställa nätverks inställningarna på de migrerade virtuella datorerna.
>
>

```powershell
    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location "Southeast Asia"

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    https://azure.microsoft.com/documentation/articles/powershell-install-configure/
    https://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in seconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module version" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article https://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you'd like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the source vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting until the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more customization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a>Optimering
Din aktuella VM-konfiguration kan anpassas särskilt för att fungera bra med standard diskar. Till exempel för att öka prestandan genom att använda många diskar i en stripe-volym. I stället för att använda 4 diskar separat på Premium Storage kan du till exempel optimera kostnaden genom att ha en enda disk. Optimeringar som detta måste hanteras i ett fall med hjälp av fall och kräver anpassade steg efter migreringen. Observera också att den här processen kanske inte fungerar bra för databaser och program som är beroende av disklayouten som definierats i installationen.

##### <a name="preparation"></a>Förberedelse
1. Slutför den enkla migreringen enligt beskrivningen i det tidigare avsnittet. Optimeringar utförs på den nya virtuella datorn efter migreringen.
2. Definiera de nya disk storlekar som krävs för den optimerade konfigurationen.
3. Bestäm mappning av aktuella diskar/volymer till de nya diskarna.

##### <a name="execution-steps"></a>Körnings steg
1. Skapa de nya diskarna med rätt storlek på den virtuella datorn Premium Storage.
2. Logga in på den virtuella datorn och kopiera data från den aktuella volymen till den nya disken som mappar till volymen. Gör detta för alla aktuella volymer som måste mappas till en ny disk.
3. Ändra sedan program inställningarna så att de växlar till de nya diskarna och koppla bort de gamla volymerna.

Information om hur du justerar programmet för bättre disk prestanda finns i avsnittet optimera program prestanda i artikeln [utformning för hög prestanda](../../virtual-machines/windows/premium-storage-performance.md) .

### <a name="application-migrations"></a>Migrering av program
Databaser och andra komplexa program kan behöva särskilda steg som definieras av program leverantören för migreringen. Läs respektive program dokumentation. T.ex. vanliga databaser kan migreras genom säkerhets kopiering och återställning.

## <a name="next-steps"></a>Nästa steg
Se följande resurser för olika scenarier för att migrera virtuella datorer:

* [Migrera Azure Virtual Machines mellan lagrings konton](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Skapa och ladda upp en Windows Server VHD till Azure.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Skapa och ladda upp en virtuell Linux-hårddisk till Azure](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrera Virtual Machines från Amazon AWS till Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Se även följande resurser för att lära dig mer om Azure Storage och Azure Virtual Machines:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Välj en disktyp för virtuella IaaS-datorer](../../virtual-machines/windows/disks-types.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: https://technet.microsoft.com/library/hh831739.aspx

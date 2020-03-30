---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 72b6c1f86b18df172994827ec78eb109fe82454e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75913765"
---
## <a name="benefits-of-managed-disks"></a>Fördelar med hanterade diskar

Låt oss gå igenom några av de fördelar du får genom att använda hanterade diskar.

### <a name="highly-durable-and-available"></a>Mycket hållbar och tillgänglig

Hanterade diskar är utformade för 99,999 % tillgänglighet. Hanterade diskar uppnår detta genom att ge dig tre repliker av dina data, vilket möjliggör hög hållbarhet. Om en eller två repliker uppstår problem, bidrar de återstående replikerna till att säkerställa att dina data kvarstår och hög tolerans mot fel. Den här arkitekturen har hjälpt Azure att konsekvent leverera hållbarhet i företagsklass för infrastruktur som en tjänstdisk (IaaS), med en branschledande nollprocentsfrekvens för upprepade fel.

### <a name="simple-and-scalable-vm-deployment"></a>Enkel och skalbar VM-distribution

Med hjälp av hanterade diskar kan du skapa upp till 50 000 **VM-diskar** av en typ i en prenumeration per region, så att du kan skapa tusentals **virtuella datorer** i en enda prenumeration. Den här funktionen ökar också skalbarheten för [skalbara skalbara skalbara uppsättningar](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) av virtuella datorer genom att du kan skapa upp till 1 000 virtuella datorer i en skalmaskinsskalauppsättning med hjälp av en Marketplace-avbildning.

### <a name="integration-with-availability-sets"></a>Integrering med tillgänglighetsuppsättningar

Hanterade diskar är integrerade med tillgänglighetsuppsättningar för att säkerställa att diskarna [för virtuella datorer i en tillgänglighetsuppsättning](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) är tillräckligt isolerade från varandra för att undvika en enda felpunkt. Diskar placeras automatiskt i olika lagringsskalor (stämplar). Om en stämpel misslyckas på grund av maskinvaru- eller programvarufel misslyckas endast VM-instanser med diskar på dessa stämplar. Anta till exempel att du har ett program som körs på fem virtuella datorer och att de virtuella datorerna finns i en tillgänglighetsuppsättning. Diskarna för dessa virtuella datorer kommer inte alla att lagras i samma stämpel, så om en stämpel går ner, fortsätter de andra instanserna av programmet att köras.

### <a name="integration-with-availability-zones"></a>Integrering med tillgänglighetszoner

Hanterade diskar stöder [tillgänglighetszoner](../articles/availability-zones/az-overview.md), vilket är ett erbjudande med hög tillgänglighet som skyddar dina program från datacenterfel. Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner. Med tillgänglighetszonerna kan Azure erbjuda branschens bästa serviceavtal med en drifttid på 99,99 % för virtuella datorer.

### <a name="azure-backup-support"></a>Support för Azure Backup

För att skydda mot regionala katastrofer kan [Azure Backup](../articles/backup/backup-overview.md) användas för att skapa ett säkerhetskopieringsjobb med tidsbaserade säkerhetskopior och principer för lagring av säkerhetskopiering. Detta gör att du kan utföra enkla VM-återställningar efter behag. Azure Backup stöder diskstorlekar på upp till fyra tebibyte-diskar (TiB).  Azure Backup stöder säkerhetskopiering och återställning av hanterade diskar. [Läs mer](../articles/backup/backup-support-matrix-iaas.md) om azure vm-säkerhetskopieringsstöd.

### <a name="granular-access-control"></a>Detaljerad åtkomstkontroll

Du kan använda [Azure-rollbaserad åtkomstkontroll (RBAC)](../articles/role-based-access-control/overview.md) för att tilldela specifika behörigheter för en hanterad disk till en eller flera användare. Hanterade diskar visar en mängd olika åtgärder, inklusive läsa, skriva (skapa/uppdatera), ta bort och hämta en [SAS-URI (Shared Access Signature)](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) för disken. Du kan bevilja åtkomst till endast de åtgärder som en person behöver för att utföra sitt jobb. Om du till exempel inte vill att en person ska kopiera en hanterad disk till ett lagringskonto kan du välja att inte bevilja åtkomst till exportåtgärden för den hanterade disken. Om du inte vill att en person ska använda en SAS-URI för att kopiera en hanterad disk kan du välja att inte bevilja den behörigheten till den hanterade disken.

### <a name="upload-your-vhd"></a>Ladda upp din vhd

 Direktöverföring gör det enkelt att överföra din virtuella hårddisk till en Azure-hanterad disk. Tidigare var du tvungen att följa en mer involverad process som inkluderade mellanlagring av dina data i ett lagringskonto. Nu finns det färre steg. Det är enklare att ladda upp lokala virtuella datorer till Azure, ladda upp till stora hanterade diskar och säkerhetskopierings- och återställningsprocessen förenklas. Det minskar också kostnaderna genom att du kan ladda upp data till hanterade diskar direkt utan att koppla dem till virtuella datorer. Du kan använda direktuppladdning för att ladda upp virtuella hårddiskar upp till 32 TiB i storlek.

 Mer information om hur du överför din virtuella hårddisk till Azure finns i [CLI-](../articles/virtual-machines/linux/disks-upload-vhd-to-managed-disk-cli.md) eller [PowerShell-artiklarna.](../articles/virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md)

## <a name="encryption"></a>Kryptering

Hanterade diskar erbjuder två olika typer av kryptering. Den första är SSE (Server Side Encryption), som utförs av lagringstjänsten. Den andra är Azure Disk Encryption (ADE), som du kan aktivera på operativsystem och datadiskar för dina virtuella datorer.

### <a name="server-side-encryption"></a>Kryptering på serversidan

[Kryptering på Azure Server-sidan](../articles/virtual-machines/windows/disk-encryption.md) tillhandahåller kryptering i vila och skyddar dina data för att uppfylla dina organisatoriska säkerhets- och efterlevnadsåtaganden. Kryptering på serversidan är aktiverad som standard för alla hanterade diskar, ögonblicksbilder och avbildningar i alla regioner där hanterade diskar är tillgängliga. Du kan antingen tillåta Att Azure hanterar dina nycklar åt dig, det här är plattformshanterade nycklar eller så kan du hantera nycklarna själv, det här är kundhanterade nycklar. Besök [faq-sidan för hanterade diskar](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption) för mer information.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Med Azure Disk Encryption kan du kryptera operativsystem och datadiskar som används av en virtuell IaaS-dator. Den här krypteringen innehåller hanterade diskar. För Windows krypteras enheterna med hjälp av BitLocker-krypteringsteknik av branschstandard. För Linux krypteras diskarna med DM-Crypt-tekniken. Krypteringsprocessen är integrerad med Azure Key Vault så att du kan styra och hantera diskkrypteringsnycklarna. Mer information finns i [Azure Disk Encryption för virtuella IaaS-datorer](../articles/security/azure-security-disk-encryption-overview.md).

## <a name="disk-roles"></a>Diskroller

Det finns tre huvudsakliga diskroller i Azure: datadisken, OS-disken och den temporära disken. Dessa roller mappas till diskar som är kopplade till din virtuella dator.

![Diskroller i aktion](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Datadisk

En datadisk är en hanterad disk som är ansluten till en virtuell dator för att lagra programdata eller andra data som du behöver behålla. Datadiskar registreras som SCSI-enheter och är märkta med en bokstav som du väljer. Varje datadisk har en maximal kapacitet på 32 767 gibibyte (GiB). Storleken på den virtuella datorn avgör hur många datadiskar du kan koppla till den och vilken typ av lagring du kan använda för att vara värd för diskarna.

### <a name="os-disk"></a>OS-disk

Varje virtuell dator har en ansluten operativsystemdisk. Den OS-disken har ett förinstallerat operativsystem som valdes när den virtuella datorn skapades. Den här disken innehåller startvolymen.

Den här disken har en maximal kapacitet på 2 048 GiB.

### <a name="temporary-disk"></a>Tillfällig disk

Varje virtuell dator innehåller en tillfällig disk, som inte är en hanterad disk. Den tillfälliga disken tillhandahåller korttidslagring för program och processer och är endast avsedd att lagra data som sid- eller växlingsfiler. Data på den temporära disken kan gå förlorade under en [underhållshändelsehändelse](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) eller när du [distribuerar om en virtuell dator](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json). På virtuella Azure Linux-datorer är den temporära disken /dev/sdb som standard och på virtuella datorer i Windows är den temporära disken D: som standard. Under en lyckad standardomstart av den virtuella datorn kommer data på den temporära disken att finnas kvar.

## <a name="managed-disk-snapshots"></a>Hanterade ögonblicksbilder av disk

En hanterad diskögonblicksbild är en skrivskyddad kraschkonsekvent fullständig kopia av en hanterad disk som lagras som en vanlig hanterad disk som standard som standard. Med ögonblicksbilder kan du säkerhetskopiera dina hanterade diskar när som helst. Dessa ögonblicksbilder finns oberoende av källdisken och kan användas för att skapa nya hanterade diskar. 

Ögonblicksbilder faktureras baserat på den använda storleken. Om du till exempel skapar en ögonblicksbild av en hanterad disk med etablerad kapacitet på 64 GiB och den faktiska använda datastorleken på 10 GiB, faktureras ögonblicksbilden endast för den använda datastorleken 10 GiB. Du kan se den använda storleken på dina ögonblicksbilder genom att titta på [Azure-användningsrapporten](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). Om till exempel den använda datastorleken för en ögonblicksbild **daily** är 10 GiB visas 10 GiB/(31 dagar) = 0,3226 som förbrukad kvantitet.

Mer information om hur du skapar ögonblicksbilder för hanterade diskar finns i följande resurser:

* [Skapa en ögonblicksbild av en hanterad disk i Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Skapa en ögonblicksbild av en hanterad disk i Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Avbildningar

Hanterade diskar stöder också att skapa en hanterad anpassad avbildning. Du kan skapa en avbildning från din anpassade virtuella hårddisk i ett lagringskonto eller direkt från en allmän (sysprepped) VM. Den här processen fångar en enda bild. Den här avbildningen innehåller alla hanterade diskar som är associerade med en virtuell dator, inklusive både operativsystem och datadiskar. Den här hanterade anpassade avbildningen gör det möjligt att skapa hundratals virtuella datorer med din anpassade avbildning utan att behöva kopiera eller hantera lagringskonton.

Information om hur du skapar bilder finns i följande artiklar:

* [Så här tar du en hanterad avbildning av en allmän virtuell dator i Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Så här generaliserar och avbildar du en Linux-dator med hjälp av Azure CLI](../articles/virtual-machines/linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Bilder kontra ögonblicksbilder

Det är viktigt att förstå skillnaden mellan bilder och ögonblicksbilder. Med hanterade diskar kan du ta en avbildning av en generaliserad virtuell dator som har tagits bort. Den här avbildningen innehåller alla diskar som är anslutna till den virtuella datorn. Du kan använda den här avbildningen för att skapa en virtuell dator och den innehåller alla diskar.

En ögonblicksbild är en kopia av en disk vid den tidpunkt då ögonblicksbilden tas. Det gäller endast för en disk. Om du har en virtuell dator som har en disk (OS-disken) kan du ta en ögonblicksbild eller en avbildning av den och skapa en virtuell dator från antingen ögonblicksbilden eller avbildningen.

En ögonblicksbild har inte medvetenhet om någon disk förutom den den innehåller. Detta gör det problematiskt att använda i scenarier som kräver samordning av flera diskar, till exempel striping. Ögonblicksbilder måste kunna samordna med varandra och detta stöds för närvarande inte.

## <a name="disk-allocation-and-performance"></a>Diskallokering och prestanda

I följande diagram visas allokering av bandbredd i realtid och IOPS för diskar med hjälp av ett system med tre nivåer:

![Etableringssystem på tre nivåer som visar bandbredd och IOPS-allokering](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

Den första nivåetablering anger IOPS per disk och bandbreddtilldelning.  På den andra nivån implementerar beräkningsservervärden SSD-etablering och tillämpar den endast på data som lagras på serverns SSD, som innehåller diskar med cachelagring (ReadWrite och ReadOnly) samt lokala diskar och temp-diskar. Slutligen sker vm-nätverksetablering på den tredje nivån för alla I/O som beräkningsvärden skickar till Azure Storages serverd. Med det här schemat beror prestanda för en virtuell dator på en mängd olika faktorer, från hur den virtuella datorn använder den lokala SSD, till antalet anslutna diskar samt prestanda- och cachelagringstypen för de diskar som den har bifogat.

Som ett exempel på dessa begränsningar hindras en Standard_DS1v1 virtuell dator från att uppnå 5 000 IOPS-potentiella enheter för en P30-disk, oavsett om den cachelagras eller inte, på grund av begränsningar på SSD- och nätverksnivåer:

![Standard_DS1v1 exempelallokering](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

Azure använder prioriterad nätverkskanal för disktrafik, vilket får prioritet framför annan låg prioritet för nätverkstrafik. Detta hjälper diskar att behålla sina förväntade prestanda i händelse av nätverkskonkurrens. På samma sätt hanterar Azure Storage resursstrider och andra problem i bakgrunden med automatisk belastningsutjämning. Azure Storage allokerar nödvändiga resurser när du skapar en disk och tillämpar proaktiv och reaktiv utjämning av resurser för att hantera trafiknivån. Detta säkerställer ytterligare diskar kan upprätthålla sina förväntade IOPS och dataflöde mål. Du kan använda måtten på VM-nivå och disknivå för att spåra prestanda- och inställningsaviseringar efter behov.

Se vår [design för högpresterande](../articles/virtual-machines/windows/premium-storage-performance.md) artikel, för att lära dig de bästa metoderna för att optimera VM + Disk konfigurationer så att du kan uppnå önskad prestanda

## <a name="next-steps"></a>Nästa steg

Om du vill att en video ska gå in mer i detalj på hanterade diskar kan du läsa: [Bättre Azure VM-återhämtning med hanterade diskar](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).

Läs mer om de enskilda disktyperna som Azure erbjuder, vilken typ som passar dina behov, och lär dig mer om deras prestandamål i vår artikel om disktyper.

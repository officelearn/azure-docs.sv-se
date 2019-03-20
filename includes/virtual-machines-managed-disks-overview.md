---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 287ae3b8122dd2a1e43c31055ac0ea5b04dddb07
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190648"
---
## <a name="benefits-of-managed-disks"></a>Fördelarna med hanterade diskar

Vi gå igenom några av fördelarna du får genom att använda hanterade diskar.

### <a name="highly-durable-and-available"></a>Extremt tillförlitliga och tillgängliga

Hanterade diskar är utformade för 99,999% tillgänglighet. Hanterade diskar åstadkomma detta genom att ge dig tre repliker av dina data, vilket ger hög tålighet. Om en eller två repliker får problem kan återstående replik(er) ta över för att säkerställa beständigheten hos dina data och ge en hög tolerans mot fel. Den här arkitekturen har hjälpt Azure oavbrutet kunnat tillhandahålla företagsklass hållbarhet för infrastruktur som en tjänst (IaaS) diskar, med branschledande noll % årlig Felfrekvens.

### <a name="simple-and-scalable-vm-deployment"></a>Enkel och skalbar distribuering av virtuella datorer

Med hanterade diskar kan du skapa upp till 50 000 VM **diskar** av en typ i en prenumeration per region, så att du kan skapa tusentals **VMs** för en enskild prenumeration. Den här funktionen dessutom ytterligare ökar skalbarheten i [VM-skalningsuppsättningar](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) genom att du kan skapa upp till 1 000 virtuella datorer i en VM-skalningsuppsättning med en Marketplace-avbildning.

### <a name="integration-with-availability-sets"></a>Integrering med tillgänglighetsuppsättningar

Hanterade diskar är integrerade med tillgänglighetsuppsättningar för att se till att diskar på [virtuella datorer i en tillgänglighetsuppsättning](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) är tillräckligt isolerade från varandra för att undvika en enskild felpunkt. Disks placeras automatiskt i olika lagringsskalenheter (stämplar). Om en stämpel misslyckas på grund av maskin- eller programvarufel, inte bara VM-instanserna med diskar på de stämplarna. Exempel: Anta att du har ett program som körs på fem virtuella datorer och de virtuella datorerna är i en Tillgänglighetsuppsättning. Diskarna fortsätter för de virtuella datorerna inte alla lagras i samma stämpeln, så om en stämpel stängs av andra instanser av programmet att köras.

### <a name="azure-backup-support"></a>Azure Backup-support

Att skydda mot regionala problem [Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md) kan användas för att skapa ett säkerhetskopieringsjobb med tidsbaserade säkerhetskopior och lagringsprinciper för säkerhetskopiering. På så sätt kan du utföra enkla VM återställningar när du vill. Azure Backup stöder för närvarande diskstorlekar på upp till fyra tebibyte (TiB)-diskar. Mer information finns i [med hjälp av Azure Backup för virtuella datorer med hanterade diskar](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

### <a name="granular-access-control"></a>Detaljerad åtkomstkontroll

Du kan använda [Azure rollbaserad åtkomstkontroll (RBAC)](../articles/role-based-access-control/overview.md) att tilldela specifika behörigheter för en hanterad disk till en eller flera användare. Hanterade diskar visar flera olika åtgärder, inklusive läsa, skriva (skapa/uppdatera), ta bort och hämta en [signatur för delad åtkomst (SAS) URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) för disken. Du kan bevilja åtkomst till endast de åtgärder som en person behöver för att utföra sitt arbete. Till exempel om du inte vill att en person att kopiera en hanterad disk till en storage-konto, kan du inte bevilja åtkomst till åtgärden för att exportera den hanterade disken. På samma sätt, om du inte vill att en person som du använder en SAS-URI för att kopiera en hanterad disk, kan du inte bevilja behörighet till den hantera disken.

## <a name="disk-roles"></a>Disk-roller

### <a name="data-disks"></a>Datadiskar

En datadisk är en hanterad disk som är kopplad till en virtuell dator för att lagra programdata eller andra data som du behöver. Datadiskar är registrerade som SCSI-enheter och är märkta med en bokstav som du väljer. Varje datadisk har en maxkapacitet på 4095 gibibyte (GiB). Storleken på den virtuella datorn avgör hur många datadiskar som du kan koppla till det och vilken typ av lagring som du kan använda som värd för diskarna.

### <a name="os-disks"></a>OS-diskar

Varje virtuell dator har en ansluten operativsystemdisk. Den OS-disken har en förinstallerad OS som valdes när den virtuella datorn skapades.

Den här disken har en maxkapacitet på 2 048 GiB.

### <a name="temporary-disk"></a>Temporär disk

Varje virtuell dator innehåller en temporär disk, vilket inte är en hanterad disk. Den temporära disken tillhandahåller kortsiktig lagring för program och processer och är avsedd att endast lagra data, till exempel växlingsfiler. Data på den temporära disken kan gå förlorade under en [underhållshändelse](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) händelse eller när du [distribuera om en virtuell dator](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json). Data på den temporära enheten behålls under en lyckad standard omstart av den virtuella datorn. 

## <a name="managed-disk-snapshots"></a>Hanterade ögonblicksbilder

En hanterad disk ögonblicksbild är en skrivskyddad fullständig kopia av en hanterad disk som lagras som en hanterad disk som standard som standard. Med ögonblicksbilder, kan du säkerhetskopiera dina hanterade diskar när som helst i tid. De här ögonblicksbilderna finnas oberoende av källdisken och kan användas för att skapa nya hanterade diskar. De faktureras baserat på Använd storlek. Till exempel om du skapar en ögonblicksbild av en hanterad disk med etablerad kapacitet för 64 GiB och storlek för data som används av 10 GiB, är den ögonblicksbilden debiteras endast för data som används storleken på 10 GiB.  

Mer information om hur du skapar ögonblicksbilder med hanterade diskar finns i följande resurser:

* [Skapa kopia av virtuell Hårddisk som lagras som en hanterad disk med hjälp av ögonblicksbilder i Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Skapa kopia av virtuell Hårddisk som lagras som en hanterad disk med hjälp av ögonblicksbilder i Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Avbildningar

Hanterade diskar stöder även skapa en hanterad anpassad avbildning. Du kan skapa en avbildning från en anpassad virtuell Hårddisk i ett lagringskonto eller direkt från en generaliserad (syspreppad) VM. Den här processen samlar in en enda avbildning. Den här avbildningen innehåller alla hanterade diskar som är associerade med en virtuell dator, inklusive både för OS- och diskar. Den här anpassade avbildningar kan skapa hundratals virtuella datorer med den anpassade avbildningen utan att behöva kopiera eller hantera alla lagringskonton.

Information om hur du skapar avbildningar finns i följande artiklar:

* [Så här avbildar du en hanterad avbildning av en generaliserad virtuell dator i Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Hur du generalisera och avbildar en Linux-dator med hjälp av Azure CLI](../articles/virtual-machines/linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Bilder med ögonblicksbilder

Det är viktigt att förstå skillnaden mellan bilder och ögonblicksbilder. Med hanterade diskar kan du ta en bild av en generaliserad virtuell dator har frigjorts. Den här avbildningen innehåller alla diskar som är kopplade till den virtuella datorn. Du kan använda den här bilden för att skapa en virtuell dator och den innehåller alla diskar.

En ögonblicksbild är en kopia av en disk vid punkten i tiden ögonblicksbilden tas. Gäller endast för en disk. Om du har en virtuell dator som har en disk (OS-disk) kan du ta en ögonblicksbild eller en bild av den och skapa en virtuell dator från ögonblicksbilden eller avbildningen.

En ögonblicksbild har inte medvetenhet om en disk utom den som den innehåller. På så sätt blir det problematiskt att använda i scenarier som kräver samordning av flera diskar, till exempel striping. Ögonblicksbilder måste kunna koordineras och detta stöds inte för tillfället.

## <a name="next-steps"></a>Nästa steg

Mer information om de enskilda disktyper som Azure erbjuder och vilken typ är ett bra alternativ för dina behov i vår artikel om disktyper.

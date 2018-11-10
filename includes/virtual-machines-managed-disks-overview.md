---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 40396462cc568a4ad7dea798fc30d72dcd75fd07
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51208853"
---
# <a name="azure-managed-disks-overview"></a>Översikt över Azure Managed Disks

Azure Managed Disks förenklar Diskhanteringen för virtuella Azure IaaS-datorer genom att hantera den [lagringskonton](../articles/storage/common/storage-introduction.md) som är associerade med de Virtuella diskarna. Du behöver bara ange typ ([Standard HDD](../articles/virtual-machines/windows/standard-storage.md), [Standard SSD](../articles/virtual-machines/windows/disks-standard-ssd.md), eller [Premium SSD](../articles/virtual-machines/windows/premium-storage.md)) och storleken på disken som du behöver och Azure skapar och hanterar disken åt dig.

## <a name="benefits-of-managed-disks"></a>Fördelarna med hanterade diskar

Låt oss ta en titt på några av fördelarna du får genom att använda hanterade diskar från och med den här videon på Channel 9 får [bättre Azure VM-känslighet med Managed Disks](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).
<br/>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency/player]

### <a name="simple-and-scalable-vm-deployment"></a>Enkel och skalbar distribuering av virtuella datorer

Managed Disks hanterar lagring för dig i bakgrunden. Tidigare var du tvungen att skapa lagringskonton för att lagra diskar (VHD-filer) för virtuella datorer i Azure. När du ökar, var du tvungen att kontrollera att du har skapat ytterligare lagringskonton så att du inte överskrider IOPS-gränsen för lagring med någon av dina diskar. Med Managed Disks hanterar lagring, är du inte längre begränsad lagringskontogränser (till exempel 20 000 IOPS / kontot). Du har också inte längre att kopiera dina anpassade avbildningar (VHD-filer) till flera lagringskonton. Du kan hantera dem på en central plats – ett lagringskonto per Azure-region – och använda dem för att skapa hundratals virtuella datorer i en prenumeration.

Hanterade diskar kan du skapa upp till 50 000 VM **diskar** av en typ i en prenumeration per region, vilket innebär att du kan skapa tusentals **VMs** för en enskild prenumeration. Den här funktionen dessutom ytterligare ökar skalbarheten i [Virtual Machine Scale Sets](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) genom att du kan skapa upp till tusen virtuella datorer i en VM-skalningsuppsättning med en Marketplace-avbildning.

### <a name="better-reliability-for-availability-sets"></a>Bättre tillförlitlighet för Tillgänglighetsuppsättningar

Hanterade diskar ger bättre tillförlitlighet för Tillgänglighetsuppsättningar genom att säkerställa att diskar på [virtuella datorer i en Tillgänglighetsuppsättning](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) är tillräckligt isolerade från varandra för att undvika enskilda felpunkter. Disks placeras automatiskt i olika lagringsskalenheter (stämplar). Om en stämpel misslyckas på grund av maskin- eller programvarufel, inte bara VM-instanserna med diskar på de stämplarna. Exempel: Anta att du har ett program som körs på fem virtuella datorer och de virtuella datorerna är i en Tillgänglighetsuppsättning. Diskarna fortsätter för de virtuella datorerna inte alla lagras i samma stämpeln, så om en stämpel stängs av andra instanser av programmet att köras.

### <a name="highly-durable-and-available"></a>Extremt tillförlitliga och tillgängliga

Azure-diskar har en tillförlitlighet på 99,999 %. Förvissad om att du har tre repliker av dina data som möjliggör hög tålighet. Om en eller två repliker får problem kan återstående replik(er) ta över för att säkerställa beständigheten hos dina data och ge en hög tolerans mot fel. Tack vare den här arkitekturen har Azure oavbrutet kunnat tillhandahålla tillförlitlighet på storföretagsnivå för sina IaaS-diskar. Azure är branschledande inom detta område med 0 % driftstopp per år. 

### <a name="granular-access-control"></a>Detaljerad åtkomstkontroll

Du kan använda [rollbaserad åtkomstkontroll (RBAC)](../articles/role-based-access-control/overview.md) att tilldela specifika behörigheter för en hanterad disk till en eller flera användare. Hanterade diskar visar flera olika åtgärder, inklusive läsa, skriva (skapa/uppdatera), ta bort och hämta en [signatur för delad åtkomst (SAS) URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) för disken. Du kan bevilja åtkomst till endast de åtgärder som en person behöver för att utföra sitt arbete. Till exempel om du inte vill att en person att kopiera en hanterad disk till en storage-konto, kan du inte bevilja åtkomst till åtgärden för att exportera den hanterade disken. På samma sätt, om du inte vill att en person som du använder en SAS-URI för att kopiera en hanterad disk, kan du inte bevilja behörighet till den hantera disken.

### <a name="azure-backup-service-support"></a>Support för Azure Backup-tjänsten

Använd Azure Backup-tjänsten med Managed Disks för att skapa ett säkerhetskopieringsjobb med tidsbaserade säkerhetskopior, enkel återställning av virtuell dator och lagringsprinciper för säkerhetskopiering. Hanterade diskar har endast stöd för lokalt Redundant lagring (LRS) som replikeringsalternativ. Tre kopior av data hålls inom en enda region. För regional haveriberedskap, måste du säkerhetskopiera dina VM-diskar i en annan region med hjälp av [Azure Backup-tjänsten](../articles/backup/backup-introduction-to-azure-backup.md) och ett GRS-lagringskonto som säkerhetskopieringsvalv. Azure Backup stöder för närvarande diskstorlekar upp till 4 TB diskar. Du behöver [uppgradera säkerhetskopieringsstack för virtuell dator till V2](../articles/backup/backup-upgrade-to-vm-backup-stack-v2.md) om stöd för 4 TB diskar. Mer information finns i [med hjälp av Azure Backup-tjänsten för virtuella datorer med Managed Disks](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="pricing-and-billing"></a>Priser och fakturering

När du använder Managed Disks, gäller följande för debitering:

* Lagringstyp

* Diskstorlek

* Antal transaktioner

* Utgående dataöverföringar

* Hanterade ögonblicksbilder (fullständig diskkopieringen)

Låt oss ta en närmare titt på de här alternativen.

**Lagringstyp:** Managed Disks har 3 prestandanivåer: [Standard HDD](../articles/virtual-machines/windows/standard-storage.md), [Standard SSD](../articles/virtual-machines/windows/disks-standard-ssd.md), och [Premium](../articles/virtual-machines/windows/premium-storage.md). Faktureringen för en hanterad disk beror på vilken typ av lagring som du har valt för disken.

**Diskstorlek**: faktureringen för hanterade diskar är beroende av den etablerade storleken på disken. Azure maps-Allokerad storlek (avrundas uppåt) till närmaste Managed Disks-alternativ som anges i tabellerna nedan. Varje hanterad disk mappar till en av de etablerade storlekarna som stöds och debiteras därefter. Till exempel att om du skapar en hanterad disk som standard och ange en etablerad storlek på 200 GB, kommer du att debiteras enligt priserna för S15 disktypen.

Här är de diskar som är tillgängliga för en premium-hanterad disk:

| **Premium SSD hanteras <br>disktyp** | **P4** | **P6** | **P10** | **P15** | **P20** | **P30** | **P40** | **P50** | **P60** | **P70** | **P80** |
|------------------|---------|---------|--------|--------|--------|----------------|----------------|----------------|----------------|----------------|----------------|
| Diskstorlek        | 32 giB  | 64 giB  | 128 GiB | 256 GB | 512 GiB | 1 024 giB (1 TiB) | 2 048 giB (2 TiB) | 4 095 giB (4 TiB) | 8 192 giB (8 TiB) | 16 384 giB (16 TiB) | 32 767 giB (TiB) |

Här är de diskar som är tillgängliga för en standard hanterad SSD-disk:

| **Standard SSD hanteras <br>disktyp** | **E10** | **E15** | **E20** | **E30** | **E40** | **E50** | **E60** | **E70** | **E80** |
|------------------|--------|--------|--------|----------------|----------------|----------------|----------------|----------------|----------------|
| Diskstorlek        | 128 GiB | 256 GB | 512 GiB | 1 024 giB (1 TiB) | 2 048 giB (2 TiB) | 4 095 giB (4 TiB) | 8 192 giB (8 TiB) | 16 384 giB (16 TiB) | 32 767 giB (TiB) |

Här är de diskar som är tillgängliga för en standard HDD hanterad disk:

| **Standard HDD hanteras <br>disktyp** | **S4** | **S6** | **S10** | **S15** | **S20** | **S30** | **S40** | **S50** | **S60** | **S70** | **S80** |
|------------------|---------|---------|--------|--------|--------|----------------|----------------|----------------|----------------|----------------|----------------|
| Diskstorlek        | 32 giB  | 64 giB  | 128 GiB | 256 GB | 512 GiB | 1 024 giB (1 TiB) | 2 048 giB (2 TiB) | 4 095 giB (4 TiB) | 8 192 giB (8 TiB) | 16 384 giB (16 TiB) | 32 767 giB (TiB) |

**Antalet transaktioner**: debiteras du för antalet transaktioner som du kan utföra på en hanterad disk som standard.

Standard SSD-diskar använder storlek på i/o på 256KB. Om de data som överförs är mindre än 256 KB, betraktas den 1 i/o-enhet. Större i/o-storlekar räknas som flera I/o för storlek på 256 KB. Till exempel räknas en 1 100 KB-i/o som fem i/o-enheter.

Det kostar ingenting för transaktioner för en hanterad disk i premium.

**Utgående dataöverföringar**: [utgående dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/) (data som går ut från Azures datacenter) debiteras för bandbreddsanvändning.

Detaljerad information om priser för Managed Disks finns i [hanterade diskar priser](https://azure.microsoft.com/pricing/details/managed-disks).


## <a name="managed-disk-snapshots"></a>Hanterade ögonblicksbilder

En hanterad ögonblicksbild är en skrivskyddad fullständig kopia av en hanterad disk som lagras som en hanterad disk som standard som standard. Med ögonblicksbilder, kan du säkerhetskopiera dina hanterade diskar när som helst i tid. De här ögonblicksbilderna finnas oberoende av källdisken och kan användas för att skapa nya Managed Disks. De faktureras baserat på Använd storlek. Om du skapar en ögonblicksbild av en hanterad disk med etablerad kapacitet för 64 GiB och storlek för data som används av 10 GiB, till exempel debiteras ögonblicksbild endast för data som används storleken på 10 GiB.  

[Inkrementella ögonblicksbilder](../articles/virtual-machines/windows/incremental-snapshots.md) stöds inte för närvarande för Managed Disks.

Mer information om hur du skapar ögonblicksbilder med Managed Disks finns i följande resurser:

* [Skapa kopia av en virtuell hårddisk som lagras som en hanterad disk med hjälp av ögonblicksbilder i Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Skapa kopia av en virtuell hårddisk som lagras som en hanterad disk med hjälp av ögonblicksbilder i Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

## <a name="images"></a>Avbildningar

Hanterade diskar stöder även skapa en hanterad anpassad avbildning. Du kan skapa en avbildning från en anpassad virtuell Hårddisk i ett lagringskonto eller direkt från en generaliserad VM (sys-förberedd). Den här processen hämtar in i en enda avbildning alla hanterade diskar som är associerade med en virtuell dator, inklusive både för OS- och diskar. Den här anpassade avbildningar kan skapa hundratals virtuella datorer med den anpassade avbildningen utan att behöva kopiera eller hantera alla lagringskonton.

Information om hur du skapar avbildningar finns i följande artiklar:

* [Så här avbildar du en hanterad avbildning av en generaliserad virtuell dator i Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Hur du generalisera och avbildar en Linux-dator med hjälp av Azure CLI](../articles/virtual-machines/linux/capture-image.md)

## <a name="images-versus-snapshots"></a>Bilder med ögonblicksbilder

Du ser ofta ordet ”bild” användes med virtuella datorer och nu ser du ”ögonblicksbilder” samt. Det är viktigt att förstå skillnaden mellan dessa villkor. Med Managed Disks kan du ta en bild av en generaliserad virtuell dator har frigjorts. Den här avbildningen innehåller alla diskar som är kopplade till den virtuella datorn. Du kan använda den här bilden för att skapa en ny virtuell dator och den innehåller alla diskar.

En ögonblicksbild är en kopia av en disk vid punkten i tiden tas den. Det gäller bara för en disk. Om du har en virtuell dator som bara har en disk (OS) kan du ta en ögonblicksbild eller en bild av den och skapa en virtuell dator från ögonblicksbilden eller avbildningen.

Vad händer om en virtuell dator har fem diskar och de stripe används? Du kan ta en ögonblicksbild av var och en av diskarna, men det finns inga medvetenhet i den virtuella datorn i tillståndet för diskar – ögonblicksbilder känner bara till en disk. I det här fallet ögonblicksbilder måste samordnas med varandra och som inte stöds.

## <a name="managed-disks-and-encryption"></a>Hanterade diskar och kryptering

Det finns två typer av kryptering för att diskutera tillståndsbaserad hanterade diskar. Den första är Storage Service Encryption (SSE), vilket utförs med storage-tjänsten. Den andra är Azure Disk Encryption, där du kan aktivera på Operativsystemet och datadiskarna för dina virtuella datorer.

### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

[Azure Storage Service Encryption](../articles/storage/common/storage-service-encryption.md) tillhandahåller kryptering i vila och skyddar dina data så att du uppfyller din organisations säkerhet och efterlevnad. SSE är aktiverat som standard för alla hanterade diskar, ögonblicksbilder och avbildningar i alla regioner där hanterade diskar är tillgängliga. Från och med den 10 juni 2017 alla nya hanterade diskar/ögonblicksbilder/avbildningar och nya data som skrivs till befintliga hanterade diskar är automatiskt krypterade i vila med nycklar som hanteras av Microsoft som standard. Gå till den [hanterade diskar FAQ-sida](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption) för mer information.

### <a name="azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE)

Azure Disk Encryption kan du kryptera OS- och diskar som används av en IaaS-dator. Den här krypteringen innehåller hanterade diskar. För Windows krypteras enheterna som med BitLocker-kryptering branschstandard. För Linux krypteras diskar med hjälp av teknik för DM-Crypt. Krypteringsprocessen är integrerat med Azure Key Vault så att du kan styra och hantera krypteringsnycklar för disken. Mer information finns i [Azure Disk Encryption för Windows och Linux IaaS-datorer](../articles/security/azure-security-disk-encryption.md).

## <a name="next-steps"></a>Nästa steg

Mer information om Managed Disks finns i följande artiklar.

### <a name="get-started-with-managed-disks"></a>Kom igång med Managed Disks

* [Skapa en virtuell dator med Resource Manager och PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md)

* [Skapa en virtuell Linux-dator med hjälp av Azure CLI](../articles/virtual-machines/linux/quick-create-cli.md)

* [Ansluta en hanterad datadisk till en Windows virtuell dator med hjälp av PowerShell](../articles/virtual-machines/windows/attach-disk-ps.md)

* [Lägga till en hanterad disk till en virtuell Linux-dator](../articles/virtual-machines/linux/add-disk.md)

* [Hanterade diskar PowerShell-exempelskript](https://github.com/Azure-Samples/managed-disks-powershell-getting-started)

* [Använd Managed Disks i Azure Resource Manager-mallar](../articles/virtual-machines/windows/using-managed-disks-template-deployments.md)

### <a name="compare-managed-disks-storage-options"></a>Jämför lagringsalternativ för Managed Disks

* [Premium SSD-diskar](../articles/virtual-machines/windows/premium-storage.md)

* [Standard SSD och HDD-diskar](../articles/virtual-machines/windows/standard-storage.md)

### <a name="operational-guidance"></a>Driftvägledning

* [Migrera från AWS och andra plattformar till Managed Disks i Azure](../articles/virtual-machines/windows/on-prem-to-azure.md)

* [Konvertera virtuella Azure-datorer till managed disks i Azure](../articles/virtual-machines/windows/migrate-to-managed-disks.md)

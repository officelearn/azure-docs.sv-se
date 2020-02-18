---
title: Vanliga frågor om migrering av Azure Migrate Server
description: Få svar på vanliga frågor om migrering av datorer med Azure Migrate Server-migrering
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 0c967027457b925b45ea19d994cfadfdbd0b8ab3
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425841"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migrering av Azure Migrate Server: vanliga frågor

I den här artikeln besvaras vanliga frågor om verktyget Azure Migrate: Migreringsverktyg för Server. Om du har fler frågor när du har läst den här artikeln kan du läsa följande artiklar:

- [Allmänna frågor](resources-faq.md) om Azure Migrate.
- [Frågor](common-questions-appliance.md) om Azure Migrate-enheten.
- [Frågor](common-questions-discovery-assessment.md) om identifiering, utvärdering och beroende visualisering.
- Publicera frågor i [Azure Migrate-forumet](https://aka.ms/AzureMigrateForum).


## <a name="how-does-agentless-vmware-replication-work"></a>Hur fungerar agent lös VMware-replikering?

Metoden för att lösa in utan agent för VMware använder VMware-ögonblicksbilder och KANALBINDNINGSTOKEN (VMware changed block tracking).

1. När du startar replikering schemaläggs en inledande replikeringscykel. I den första cykeln tas en ögonblicks bild av den virtuella datorn. Den här ögonblicks bilden används för att replikera VM-VMDK: er (diskar). 
2. När den inledande replikeringen har slutförts schemaläggs delta-replikering med jämna mellanrum.
    - Under delta-replikering tas en ögonblicks bild och data block som har ändrats sedan den tidigare replikeringen replikeras.
    - VMware KANALBINDNINGSTOKEN används för att avgöra vilka block som har ändrats sedan den senaste cykeln.
    - Frekvensen av de periodiska replikeringarna hanteras automatiskt av Azure Migrate, beroende på hur många andra virtuella datorer/diskar som replikeras samtidigt från samma data lager. På ideala villkor konvergerar replikeringen slutligen till en cykel per timme för varje virtuell dator.

När du migrerar schemaläggs en replikering på begäran för datorn för att samla in återstående data. Du kan välja att stänga av datorn under migreringen för att säkerställa ingen data förlust och program konsekvens.

## <a name="why-isnt-resynchronization-exposed"></a>Varför är inte omsynkronisering exponerad?

Vid en fullständig migrering skrivs skillnaden mellan den aktuella ögonblicks bilden och den tidigare tagna ögonblicks bilden. Eftersom det alltid är skillnaden mellan ögonblicks bilder kan du vika data i. Om en viss sektor skrivs N gånger mellan ögonblicks bilder måste bara den senaste skrivningen överföras, eftersom vi bara är intresserade av den senaste synkroniseringen. Detta skiljer sig från den agentbaserade replikeringen, där vi spårar och tillämpar alla Skriv åtgärder. Det innebär att varje delta cykel är en omsynkronisering. Det finns därför inget alternativ för omsynkronisering visas. Om diskarna inte synkroniseras på grund av ett problem, korrigeras det i nästa cykel. 

## <a name="how-does-churn-rate-impact-agentless-replication"></a>Hur påverkar omsättnings takten för en misslyckad replikering?

Eftersom det är ett viktat datum för replikering av agenten, är omsättnings mönstret viktigare än omsättnings takten. När en fil skrivs igen och återigen har hastigheten ingen effekt. Men ett mönster där varje annan sektor skrivs orsakar hög omsättning i nästa cykel. Eftersom vi minimerar mängden data som vi överför, tillåter vi att data viks så mycket som möjligt innan nästa cykel schemaläggs.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Hur ofta schemaläggs en replikeringscykel?

Formeln för att schemalägga nästa replikeringscykel: (föregående cykel tid/2) eller 1 timme, beroende på vilket som är högre.

Om en virtuell dator till exempel tar fyra timmar för en delta cykel, schemaläggs nästa cykel på två timmar och inte under nästa timma. Detta skiljer sig direkt efter den inledande replikeringen, där den första delta cykeln schemaläggs omedelbart.

## <a name="how-does-agentless-replication-impact-vmware-servers"></a>Hur påverkar agenten replikeringen VMware-servrar?

Det finns vissa prestanda påverkan på vCenter Server/ESXi-värdar. Eftersom den agentbaserade replikeringen använder ögonblicks bilder, förbrukar sig IOPs på lagrings utrymme och viss bandbredd för IOPS krävs. Vi rekommenderar inte att du använder replikering utan Agent om det finns begränsningar för lagring/IOPs i din miljö.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Kan jag utföra en agent lös migrering av virtuella UEFI-datorer till Azure gen 2?

Nej. Använd Azure Site Recovery för att migrera de virtuella datorerna till virtuella datorer i Azure. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Kan jag fästa virtuella datorer på Azure-tillgänglighetszoner när jag migrerar?

Nej, Azure-tillgänglighetszoner stöds inte.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Vilket transport protokoll används av Azure Migrate under replikeringen?

Azure Migrate använder protokollet för Network Block Device (NBD) med SSL-kryptering.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Vilken lägsta vCenter Server version krävs för migrering?

Du måste ha minst vCenter Server 5,5 och VMware vSphere ESXi-värd version 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Kan kunder migrera sina virtuella datorer till ohanterade diskar?

Nej. Azure Migrate stöder endast migrering till Managed disks (standard hård disk, Premium SSD).

## <a name="how-many-vms-can-i-replicate-together-with-agentless-migration"></a>Hur många virtuella datorer kan jag replikera tillsammans med en agent utan migrering?

För närvarande kan du migrera 100-VM: ar per vCenter Server samtidigt. Migrera i batchar med 10 virtuella datorer.
 




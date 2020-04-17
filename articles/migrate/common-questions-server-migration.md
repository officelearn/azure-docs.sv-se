---
title: Vanliga frågor och svar om migrering av Azure Migrera server
description: Få svar på vanliga frågor om hur du använder Azure Migrate Server Migration för att migrera datorer.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: bf33c276c721f3be16d75b964c2b223e90c2a560
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529814"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrera servermigrering: Vanliga frågor

Den här artikeln besvarar vanliga frågor om verktyget Azure Migrate: Server Migration. Om du har andra frågor kontrollerar du dessa resurser:

- [Allmänna frågor](resources-faq.md) om Azure Migrate
- Frågor om [Azure Migrate-enheten](common-questions-appliance.md)
- Frågor om [identifiering, bedömning och beroendevisualisering](common-questions-discovery-assessment.md)
- Få svar på frågor i [Azure Migrate-forumet](https://aka.ms/AzureMigrateForum)

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Vilka geografiska områden stöds för migrering med Azure Migrate?

Granska de geografiska områden som stöds för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [statliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="how-does-agentless-vmware-replication-work"></a>Hur fungerar agentless VMware-replikering?

Agentless replication-metoden för VMware använder VMware-ögonblicksbilder och VMware Changed Block Tracking (KBT).

Så här ser processen ut:

1. När du startar replikering schemaläggs en inledande replikeringscykel. I den inledande cykeln tas en ögonblicksbild av den virtuella datorn. Ögonblicksbilden används för att replikera virtuella datorer VMDK (diskar). 
2. När den inledande replikeringscykeln är klar schemaläggs deltareplikeringscykler regelbundet.
    - Under deltareplikering tas en ögonblicksbild och datablock som har ändrats sedan den föregående replikeringscykeln replikeras.
    - VMware KBT används för att bestämma block som har ändrats sedan den senaste cykeln.
    - Frekvensen för periodiska replikeringscykler hanteras automatiskt av Azure Migrate och beror på hur många andra virtuella datorer och diskar replikeras samtidigt från samma datalager. Under idealiska förhållanden konvergerar replikering så småningom till en cykel per timme för varje virtuell dator.

När du migrerar schemaläggs en replikeringscykel på begäran för att datorn ska kunna samla in återstående data. För att säkerställa noll dataförlust och programkonsekvens kan du välja att stänga av datorn under migreringen.

## <a name="why-isnt-resynchronization-exposed"></a>Varför avslöjas inte omsynkronisering?

Under agentlös migrering skrivs skillnaden mellan den aktuella ögonblicksbilden och den tidigare tagna ögonblicksbilden i varje deltacykel. Det är alltid skillnaden mellan ögonblicksbilder, vikning data i. Om en viss sektor skrivs *N* gånger mellan ögonblicksbilder, behöver endast den sista skrivningen överföras eftersom vi bara är intresserade av den senaste synkroniseringen. Processen skiljer sig från agentbaserad replikering, under vilken vi spårar och tillämpar varje skrivning. I den här processen är varje deltacykel en omsynkronisering. Så, ingen omsynkronisering alternativ exponeras. Om diskarna aldrig synkroniseras på grund av ett fel, är det fast i nästa cykel. 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Hur påverkar omsättningshastigheten agentless replication?

Eftersom agentless replication viks i data är *omsättningsmönstret* viktigare än *omsättningshastigheten*. När en fil skrivs om och om igen har hastigheten inte mycket effekt. Men ett mönster där alla andra sektorer är skrivna orsakar hög omsättning i nästa cykel. Eftersom vi minimerar mängden data vi överför tillåter vi att data viks så mycket som möjligt innan vi schemalägger nästa cykel.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Hur ofta schemaläggs en replikeringscykel?

Formeln för att schemalägga nästa replikeringscykel är (föregående cykeltid / 2) eller en timme, beroende på vilket som är högst.

Om en virtuell dator till exempel tar fyra timmar för en deltacykel schemaläggs nästa cykel om två timmar och inte i nästa timme. Processen är annorlunda omedelbart efter den första replikeringen, när den första deltacykeln schemaläggs omedelbart.

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Hur påverkar agentless replication VMware-servrar?

Agentlös replikering resulterar i viss prestandapåverkan på VMware vCenter Server- och VMware ESXi-värdar. Eftersom agentless replication använder ögonblicksbilder förbrukar den IOPS vid lagring, så viss IOPS-lagringsbandbredd krävs. Vi rekommenderar inte att du använder agentless replikering om du har begränsningar för lagring eller IOPs i din miljö.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Kan jag göra agentless migrering av UEFI virtuella datorer till Azure Gen 2?

Nej. Använd Azure Site Recovery för att migrera dessa virtuella datorer till virtuella virtuella azure-datorer i Gen 2. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Kan jag fästa virtuella datorer i Azure-tillgänglighetszoner när jag migrerar?

Nej. Azure-tillgänglighetszoner stöds inte för Azure Migrera migrera.

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Vilket transportprotokoll använder Azure Migrate under replikeringen?

Azure Migrate använder NBD-protokollet (Network Block Device) med SSL-kryptering.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Vilken är den minsta vCenter Server-versionen som krävs för migrering?

Du måste ha minst vCenter Server 5.5 och vSphere ESXi värd version 5.5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Kan kunder migrera sina virtuella datorer till ohanterat diskar?

Nej. Azure Migrate stöder migrering endast till hanterade diskar (Standard HDD, Premium SSD).

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Hur många virtuella datorer kan jag replikera samtidigt med hjälp av agentless migration?

För närvarande kan du migrera 100 virtuella datorer per instans av vCenter Server samtidigt. Migrera i grupper om 10 virtuella datorer.

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Hur begränsar jag replikering i att använda Azure Migrate-installation för agentlös VMware-replikering?  

Du kan begränsa med NetQosPolicy. Ett exempel:

Det AppNamePrefix som ska användas i NetQosPolicy är "GatewayWindowsService.exe". Du kan skapa en princip för Azure Migrate-enheten för att begränsa replikeringstrafiken från installationen genom att skapa en princip som den här:
 
New-NetQosPolicy -Name "ThrottleReplication" -AppPathNameMatchCondition "GatewayWindowsService.exe" -ThrottleRateActionBitsPerSecond 1MB

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>När migrerar jag maskiner som fysiska servrar?

Migrera maskiner genom att behandla dem som fysiska servrar är användbart i ett antal scenarier:

- När du migrerar lokala fysiska servrar.
- Om du migrerar virtuella datorer som virtualiseras av plattformar som Xen, KVM.
- Om du vill migrera virtuella virtuella datorer med Hyper-V eller VMware kan du inte använda standardmigreringsprocessen för [Hyper-V](tutorial-migrate-hyper-v.md)eller [VMware-migrering.](server-migrate-overview.md) Till exempel om du inte kör VMware vCenter och bara använder ESXi-värdar.
- Så här migrerar du virtuella datorer som körs i privata moln till Azure
- Om du vill migrera virtuella datorer som körs i offentliga moln som Amazon Web Services (AWS) eller Google Cloud Platform (GCP) till Azure.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliance"></a>Jag har distribuerat två (eller flera) enheter för att identifiera virtuella datorer i min vCenter Server. Men när jag försöker migrera de virtuella datorerna ser jag bara virtuella datorer som motsvarar en av apparaten.

Även om detta kan vara ett bra användningsfall stöder vi för närvarande inte det. Om du distribuerar två (eller flera) enheter för att identifiera samma uppsättning virtuella datorer uppstår ett serviceproblem där vm-ägarskapet fortsätter att växla mellan de två apparaterna. Det är därför du ser virtuella datorer som visas och försvinner. I sådana fall måste du ta bort en apparat och göra en hård uppdatering för att lösa problemet.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Behöver jag VMware vCenter för att migrera virtuella datorer med VMware?
Om du vill [migrera virtuella virtuella datorer](server-migrate-overview.md) med VMware-agentbaserad eller agentlös migrering måste ESXi-värdar där virtuella datorer finns hanteras av vCenter Server. Om du inte har vCenter Server kan du migrera virtuella virtuella datorer med VMware genom att migrera dem som fysiska servrar. [Läs mer](migrate-support-matrix-physical-migration.md).
 
## <a name="next-steps"></a>Nästa steg

Läs [översikten över Azure Migrate](migrate-services-overview.md).

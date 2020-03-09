---
title: Vanliga frågor och svar om migrering av Azure Migrate Server
description: Få svar på vanliga frågor om att använda Azure Migrate Server-migrering för att migrera datorer.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 273ef746e685afcf9f3654963dd9c6bd5b855b24
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927518"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migrering av Azure Migrate Server: vanliga frågor

I den här artikeln besvaras vanliga frågor om verktyget Azure Migrate: Migreringsverktyg för Server. Om du har andra frågor kontrollerar du följande resurser:

- [Allmänna frågor](resources-faq.md) om Azure Migrate
- Frågor om [Azure Migrate-enheten](common-questions-appliance.md)
- Frågor om [identifiering, utvärdering och beroende visualisering](common-questions-discovery-assessment.md)
- Få svar på frågor i [Azure Migrate-forumet](https://aka.ms/AzureMigrateForum)

## <a name="how-does-agentless-vmware-replication-work"></a>Hur fungerar agent lös VMware-replikering?

Metoden för att lösa in utan agent för VMware använder VMware-ögonblicksbilder och VMware-KANALBINDNINGSTOKEN (changed block tracking).

Här är processen:

1. När du startar replikering schemaläggs en inledande replikeringscykel. I den första cykeln tas en ögonblicks bild av den virtuella datorn. Ögonblicks bilden används för att replikera VM-VMDK: er (diskar). 
2. När den inledande replikeringen har slutförts schemaläggs delta-replikering med jämna mellanrum.
    - Under delta-replikering tas en ögonblicks bild och data block som har ändrats sedan den tidigare replikeringen replikeras.
    - VMware KANALBINDNINGSTOKEN används för att avgöra vilka block som har ändrats sedan den senaste cykeln.
    - Frekvensen av de periodiska replikeringarna hanteras automatiskt av Azure Migrate och är beroende av hur många andra virtuella datorer och diskar som replikeras samtidigt från samma data lager. På ideala villkor konvergerar replikeringen slutligen till en cykel per timme för varje virtuell dator.

När du migrerar schemaläggs en replikering på begäran för att datorn ska kunna samla in återstående data. För att förhindra data förlust och program konsekvens kan du välja att stänga av datorn under migreringen.

## <a name="why-isnt-resynchronization-exposed"></a>Varför är inte omsynkronisering exponerad?

I varje delta cykel skrivs skillnaden mellan den aktuella ögonblicks bilden och den tidigare tagna ögonblicks bilden under en migrering utan agent. Det är alltid skillnaden mellan ögonblicks bilder, vik data i. Om en särskild sektor skrivs *N* gånger mellan ögonblicks bilder måste bara den senaste skrivningen överföras eftersom vi bara är intresserade av den senaste synkroniseringen. Processen skiljer sig från agent-baserad replikering under vilken vi spårar och tillämpar varje skrivning. I den här processen är varje delta cykel en omsynkronisering. Därför visas inget alternativ för omsynkronisering. Om diskarna aldrig synkroniseras på grund av ett problem, korrigeras det i nästa cykel. 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Hur påverkar omsättnings takten för en misslyckad replikering?

Eftersom mikroreplikeringen är i data, är det *omsättnings mönster* som är viktigare än *omsättnings takten*. När en fil skrivs igen och återigen har hastigheten ingen effekt. Men ett mönster där varje annan sektor skrivs orsakar hög omsättning i nästa cykel. Eftersom vi minimerar mängden data som vi överför, tillåter vi att data viks så mycket som möjligt innan vi schemalägger nästa cykel.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Hur ofta schemaläggs en replikeringscykel?

Formeln för att schemalägga nästa replikeringscykel är (föregående cykel tid/2) eller en timme, beroende på vilket som är högre.

Om en virtuell dator till exempel tar fyra timmar för en delta cykel, schemaläggs nästa cykel på två timmar och inte under nästa timma. Processen skiljer sig direkt efter den inledande replikeringen, när den första delta cykeln har schemalagts direkt.

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Hur påverkar agenten replikeringen VMware-servrar?

Replikeringen utan agent medför att vissa prestanda påverkas av VMware vCenter Server och VMware ESXi värdar. Eftersom en agent utan replikering använder ögonblicks bilder, förbrukar den IOPS på lagrings utrymme, så viss bandbredd för IOPS krävs. Vi rekommenderar inte att du använder replikering utan Agent om du har begränsningar på lagring eller IOPs i din miljö.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Kan jag utföra en agent lös migrering av virtuella UEFI-datorer till Azure gen 2?

Nej. Använd Azure Site Recovery för att migrera de virtuella datorerna till virtuella datorer i Azure. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Kan jag fästa virtuella datorer på Azure-tillgänglighetszoner när jag migrerar?

Nej. Azure-tillgänglighetszoner stöds inte för Azure Migrate migrering.

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Vilket transport protokoll använder Azure Migrate under replikeringen?

Azure Migrate använder protokollet för Network Block Device (NBD) med SSL-kryptering.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Vilken lägsta vCenter Server version krävs för migrering?

Du måste ha minst vCenter Server 5,5 och vSphere ESXi Host version 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Kan kunder migrera sina virtuella datorer till ohanterade diskar?

Nej. Azure Migrate stöder endast migrering till hanterade diskar (Standard HDD, Premium SSD).

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Hur många virtuella datorer kan jag replikera på en gång med hjälp av en agent utan migrering?

För närvarande kan du migrera 100-VM: ar per instans av vCenter Server samtidigt. Migrera i batchar med 10 virtuella datorer.
 
## <a name="next-steps"></a>Nästa steg

Läs [Azure Migrate översikt](migrate-services-overview.md).

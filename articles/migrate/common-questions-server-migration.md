---
title: Vanliga frågor om migrering av Azure Migrate Server
description: Få svar på vanliga frågor om migrering av Azure Migrate Server
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: bae3447f0fada18de5473e1ef1a1c1d431535f63
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77067388"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migrering av Azure Migrate Server: vanliga frågor

I den här artikeln besvaras vanliga frågor om Azure Migrate: Server-migrering. Om du har fler frågor efter att ha läst den här artikeln kan du publicera dem i [Azure Migrate-forumet](https://aka.ms/AzureMigrateForum). Om du har andra frågor kan du läsa följande artiklar:

- [Allmänna frågor](resources-faq.md) om Azure Migrate.
- [Frågor](common-questions-appliance.md) om Azure Migrate-enheten.
- [Frågor](common-questions-discovery-assessment.md) om identifiering, utvärdering och beroende visualisering.


## <a name="how-does-agentless-vmware-replication-work"></a>Hur fungerar agent lös VMware-replikering?

Metoden för att lösa in utan agent för VMware använder VMware-ögonblicksbilder och KANALBINDNINGSTOKEN (VMware changed block tracking). En inledande replikeringscykel schemaläggs när användaren startar replikeringen. I den inledande replikeringen tas en ögonblicks bild av den virtuella datorn och den här ögonblicks bilden används för att replikera VM-VMDK: er (diskar). När den inledande replikeringen har slutförts schemaläggs delta-replikering med jämna mellanrum. I delta-replikeringen tas en ögonblicks bild och data block som har ändrats sedan den tidigare replikerings cykeln replikeras. VMware-spårning av ändrade block används för att avgöra vilka block som har ändrats sedan den senaste cykeln.
Frekvensen av de periodiska replikeringarna hanteras automatiskt av tjänsten, beroende på hur många andra virtuella datorer/diskar som samtidigt replikeras från samma data lager och på ideala villkor konvergeras till en cykel per timme per virtuell dator.

När du migrerar schemaläggs en replikering på begäran för den virtuella datorn för att samla in återstående data. Du kan välja att stänga av den virtuella datorn som en del av migreringen för att säkerställa ingen data förlust och program konsekvens.

## <a name="why-is-the-resynchronization-option-not-exposed-in-agentless-stack"></a>Varför visas inte alternativet omsynkronisering i en agent utan agent?

I en agent utan agent överför vi skillnaden mellan den aktuella ögonblicks bilden och den tidigare ögonblicks bilden som vi har tagit i varje delta cykel. Eftersom det alltid är en skillnad mellan ögonblicks bilder, ger detta möjlighet att vika data (dvs. om en viss sektor har skrivits mellan ögonblicks bilderna, behöver vi bara överföra den senaste skrivningen eftersom vi bara är intresserade av den senaste synkroniseringen). Detta skiljer sig från den agentbaserade stacken där vi spårar varje skrivning och tillämpar dem. Det innebär att varje delta cykel är en omsynkronisering. Det finns därför inget alternativ för omsynkronisering visas. 

Om diskarna inte är synkroniserade på grund av ett problem, korrigeras de i nästa cykel. 

## <a name="what-is-the-impact-of-churn-rate-if-i-use-agentless-replication"></a>Vad är effekten av omsättningen i omsättningen om jag använder en agentisk replikering?

Eftersom stacken är beroende av att data viks, mer än omsättnings takten, är flödes mönstret det som är viktigt i stacken. Ett mönster där en fil skrivs igen och återigen inte har stor inverkan. Ett mönster där varje annan sektor skrivs kommer dock att orsaka hög omsättning i nästa cykel. Eftersom vi minimerar mängden data som vi överför, tillåter vi att data viks så mycket som möjligt innan nästa cykel schemaläggs.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Hur ofta schemaläggs en replikeringscykel?

Formeln för att schemalägga nästa replikeringscykel är här: (föregående cykeltid/2) eller 1 timme, beroende på vilket som är högre. Om till exempel en virtuell dator tog fyra timmar för en delta cykel, kommer vi att schemalägga nästa cykel på 2 timmar och inte under nästa timma. Detta skiljer sig när cykeln är omedelbart efter IR, där vi schemalägger den första delta cykeln direkt.

## <a name="what-is-the-impact-on-performance-of-vcenter-server-or-esxi-host-while-using-agentless-replication"></a>Vad är påverkan på prestanda för vCenter Server-eller ESXi-värden samtidigt som du använder en agentisk replikering?

Eftersom den agentbaserade replikeringen använder ögonblicks bilder, finns det en IOPs-förbrukning på lagrings utrymmet och kunder behöver lite disk utrymme på lagrings platsen. Vi rekommenderar inte att du använder den här stacken i Storage/IOPs-begränsade miljöer.

## <a name="does-agentless-migration-stack-support-migration-of-uefi-vms-to-azure-gen-2-vms"></a>Stöder agenten för migrering av virtuella UEFI-datorer till virtuella datorer i Azure gen 2?

Nej, du måste använda Azure Site Recovery för att migrera de virtuella datorerna till generation 2 virtuella Azure-datorer. 

## <a name="can-i-pin-my-vms-to-azure-availability-zones-when-i-migrate"></a>Kan jag fästa mina virtuella datorer på Azure-tillgänglighetszoner när jag migrerar?

Nej, support för Azure-tillgänglighetszoner finns inte där.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Vilket transport protokoll används av Azure Migrate under replikeringen?

Azure Migrate använder protokollet för Network Block Device (NBD) med SSL-kryptering.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Vilken lägsta vCenter Server version krävs för migrering?

Du måste ha minst vCenter Server 5,5 och VMware vSphere ESXi-värd version 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Kan kunder migrera sina virtuella datorer till ohanterade diskar?

Nej. Azure Migrate stöder endast migrering till Managed disks (standard hård disk, Premium SSD).

## <a name="how-many-vms-can-replicate-simultaneously-using-agentless-vmware-stack"></a>Hur många virtuella datorer kan replikera samtidigt med en agent utan VMware-stack?

För närvarande kan kunderna migrera 100 VM: ar per vCenter Server samtidigt. Detta kan göras i batchar med 10 virtuella datorer.





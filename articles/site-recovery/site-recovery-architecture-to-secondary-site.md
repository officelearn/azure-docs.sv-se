---
title: "Hur fungerar lokal datorreplikering till en sekundär lokal plats i Azure Site Recovery? | Microsoft Docs"
description: "Den här artikeln innehåller en översikt över komponenter och arkitektur som används för att replikera lokala virtuella datorer och fysiska servrar till en sekundär plats med tjänsten Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/29/2017
ms.author: raynew
ms.openlocfilehash: fca95c63964b955db7ddfbe53250702cc8af122e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-does-on-premises-machine-replication-to-a-secondary-site-work-in-site-recovery"></a>Hur fungerar lokal datorreplikering till en sekundär plats i Site Recovery?

Den här artikeln beskriver de komponenter och processer som är inblandade vid replikering av lokala virtuella datorer och fysiska servrar till Azure med hjälp av tjänsten [Azure Site Recovery](site-recovery-overview.md).

Du kan replikera följande till din sekundära lokala plats:
- Lokala virtuella Hyper-V-datorer, virtuella Hyper-V-datorer på Hyper-V-kluster och fristående värdar som hanteras i System Center Virtual Machine Manager (VMM)-moln.
- Lokala virtuella VMware-datorer och fysiska Windows- och Linux-servrar. I det här scenariot hanteras replikeringen av Scout.

Skriv dina kommentarer längst ned i den här artikeln eller i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="replicate-hyper-v-vms-to-a-secondary-on-premises-site"></a>Replikera virtuella Hyper-V-datorer till en sekundär lokal plats


### <a name="architectural-components"></a>Arkitekturkomponenter

Följande behöver du för att replikera virtuella Hyper-V-datorer till en sekundär plats.

**Komponent** | **Plats** | **Detaljer**
--- | --- | ---
**Azure** | Du behöver ett konto hos Microsoft. |
**VMM-server** | Vi rekommenderar att det finns en VMM-server på den primära platsen och en på den sekundära platsen | Varje VMM-server ska vara ansluten till internet.<br/><br/> Varje server bör ha minst ett VMM-privat moln, med Hyper-V-funktionsprofilen inställld.<br/><br/> Du installerar Azure Site Recovery-providern på VMM-servern. Providern samordnar och styr replikeringen med Site Recovery-tjänsten via Internet. Kommunikationen mellan providern och Azure är säker och krypterad.
**Hyper-V-server** |  En eller flera Hyper-V-värdservrar i de primära och sekundära VMM-molnen.<br/><br/> Servrarna ska vara anslutna till internet.<br/><br/> Data replikeras mellan de primära och sekundära Hyper-V-värdservrarna via LAN eller VPN med hjälp av Kerberos eller certifikatautentisering.  
**Virtuella Hyper-V-datorer** | Finns på Hyper-V-värdgästservern. | Källvärdservern måste ha minst en virtuell dator som du vill replikera.

### <a name="replication-process"></a>Replikeringsprocessen

1. Du ställer in Azure-kontot.
2. Du skapar ett Replication Services-valv för Site Recovery och konfigurerar valvinställningar, inklusive:

    - Replikeringskällan och -målet (primära och sekundära platser).
    - Installation av Azure Site Recovery-Provider och Microsoft Azure Recovery Services-agenten. Providern är installerad på VMM-servrar och agenten på varje Hyper-V-värd.
    - Du skapar en replikeringsprincip för VMM-källmolnet. Principen tillämpas på alla virtuella datorer som befinner sig på värdar i molnet.
    - Du aktiverar replikering för virtuella Hyper-V-datorer. Inledande replikering sker i enlighet med inställningarna för replikeringsprincipen.
4. Dataändringar spåras och replikering av deltaändringar till Azure påbörjas efter att den inledande replikeringen har slutförts. Spårade ändringar för ett objekt lagras i en .hrl-fil.
5. Du kör ett redundanstest för att kontrollera att allt fungerar.

**Bild 1: VMM till VMM-replikering**

![Lokal till lokal](./media/site-recovery-components/arch-onprem-onprem.png)

### <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

1. Du kan köra en planerad eller oplanerad [redundansväxling](site-recovery-failover.md) mellan lokala platser. Om du kör en planerad redundansväxling stängs de virtuella källdatorerna av för att säkerställa att inga data går förlorade.
2. Du kan redundansväxla en enskild dator eller skapa [återställningsplaner](site-recovery-create-recovery-plans.md) för att samordna redundans för flera datorer.
4. Om du utför en oplanerad redundansväxling till en sekundär plats efter att redundansdatorerna på den sekundära platsen inte aktiverats för skydd eller replikering. Om du utför en planerad redundans skyddas datorerna på den sekundära platsen efter redundansen.
5. Sedan kan du etablera redundansen för att få åtkomst till arbetsbelastningen från den virtuella replikdatorn.
6. När din primära plats är tillgänglig igen, kan du initiera omvänd replikering för att replikera från den sekundära platsen till den primära. Omvänd replikering skyddar de virtuella datorerna, men det sekundära datacentret är fortfarande den aktiva platsen.
7. För att göra den primära platsen till den aktiva platsen igen, initierar du en planerad redundans från sekundär till primär, följt av en till omvänd replikering.




## <a name="replicate-vmware-vmsphysical-servers-to-a-secondary-site"></a>Replikera virtuella VMware-datorer/fysiska servrar till en sekundär plats

Du replikerar virtuella VMware-datorer eller fysiska servrar till en sekundär plats med InMage Scout, med hjälp av dessa arkitekturkomponenter:


### <a name="architectural-components"></a>Arkitekturkomponenter

**Komponent** | **Plats** | **Detaljer**
--- | --- | ---
**Azure** | InMage Scout. | För att hämta InMage Scout behöver du en Azure-prenumeration.<br/><br/> När du har skapat ett Recovery Services-valv laddar du ned InMage Scout och installerar de senaste uppdateringarna för att konfigurera distributionen.
**Processervern** | Finns på primär plats | Du distribuerar processervern för att hantera cachelagring, komprimering och dataoptimering.<br/><br/> Den hanterar också push-installationen av Unified Agent till de datorer som du vill skydda.
**Konfigurationsserver** | Finns på sekundär plats | Konfigurationsservern hanterar, konfigurerar och övervakar distributionen, antingen med hjälp av hanteringswebbplatsen eller vContinuum-konsolen.
**vContinuum-server** | Valfri. Installeras på samma plats som konfigurationsservern. | Den innehåller en konsol för att hantera och övervaka din skyddade miljö.
**Huvudmålservern** | Finns på den sekundära platsen | Huvudmålservern innehåller replikerade data. Den tar emot data från processervern, skapar en replikdator på den sekundära platsen och innehåller datakvarhållningspunkterna.<br/><br/> Hur många huvudmålservrar du behöver beror på hur många datorer du skyddar.<br/><br/> Om du vill växla tillbaka till den primära platsen behöver du en huvudmålserver även där. Unified-agenten är installerad på den här servern.
**VMware ESX/ESXi och vCenter-server** |  Virtuella datorer finns på ESX-/ESXi-värdar. Värdar hanteras med en vCenter-server | Du behöver en VMware-infrastruktur för att replikera virtuella VMware-datorer.
**Virtuella datorer/fysiska servrar** |  Unified Agent installeras på virtuella VMware-datorer eller fysiska servrar som du vill replikera. | Agenten fungerar som en kommunikationsprovider mellan alla komponenter.


### <a name="replication-process"></a>Replikeringsprocessen

1. Du konfigurerar komponentservrarna på varje plats (konfiguration, process, huvudmålserver) och installerar Unified Agent på de datorer som du vill replikera.
2. Efter den första replikeringen skickar agenten på varje dator förändringsreplikeringar till processervern.
3. Processervern optimerar data och överför dem till huvudmålservern på den sekundära platsen. Konfigurationsservern hanterar replikeringen.

**Bild 2: VMware till VMware-replikering**

![VMware till VMware](./media/site-recovery-components/vmware-to-vmware.png)


## <a name="next-steps"></a>Nästa steg

Granska [supportmatrisen](site-recovery-support-matrix-to-sec-site.md)

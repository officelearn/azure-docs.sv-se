---
title: Arkitektur för Hyper-V-haveriberedskap till en sekundär lokal plats med Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller en översikt över arkitekturen för haveriberedskap för lokala Hyper-V-datorer till en sekundär System Center VMM-plats med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/19/2019
ms.author: raynew
ms.openlocfilehash: 553ecefc9f7588de0ee9056f2b2385f9a0ede50f
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316175"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Arkitektur - Hyper-V-replikering till en sekundär plats

Den här artikeln beskriver de komponenter och processer som ingår i replikeringen av lokala virtuella Hyper-V-datorer i System Center Virtual Machine Manager-moln (VMM) till en sekundär VMM-plats med tjänsten [Azure Site Recovery](site-recovery-overview.md) på Azure-portalen.


## <a name="architectural-components"></a>Arkitekturkomponenter

Följande tabell och bild ger en översikt över de komponenter som används för Hyper-V-replikering till en sekundär plats.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | Azure-prenumeration | Du kan skapa ett Recovery Services-valv i Azure-prenumerationen för att dirigera och hantera replikeringen mellan VMM-platser.
**VMM-server** | Du behöver en primär och sekundär VMM-plats. | Vi rekommenderar att det finns en VMM-server på den primära platsen och en på den sekundära platsen.
**Hyper-V-server** |  En eller flera Hyper-V-värdservrar i de primära och sekundära VMM-molnen. | Data replikeras mellan de primära och sekundära Hyper-V-värdservrarna via LAN eller VPN med hjälp av Kerberos eller certifikatautentisering.  
**Virtuella Hyper-V-datorer** | På Hyper-V-värdservern. | Källvärdservern måste ha minst en virtuell dator som du vill replikera.

**Lokalt till lokalt arkitektur**

![Lokal till lokal](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Replikeringsprocessen

1. När den inledande replikeringen utlöses en [Hyper-V VM-ögonblicksbild](https://technet.microsoft.com/library/dd560637.aspx) ögonblicksbilden tas.
2. Virtuella hårddiskar på den virtuella datorn är replikeras en i taget, till den sekundära platsen.
3. Om diskändringar inträffar under den inledande replikeringen pågår, spårar ändringarna i den spårningsverktyget för Hyper-V-replikering som Hyper-V-replikeringsloggar (.hrl). Dessa loggfiler finns i samma mapp som diskarna. Varje disk har en associerad hrl-fil som skickas till den sekundära platsen. Ögonblicksbilden och loggfilerna använder diskresurser när den inledande replikeringen pågår.
4. När den inledande replikeringen är klar tas VM-ögonblicksbilden bort och deltareplikering börjar.
5. Diskförändringarna (delta) i loggen synkroniseras och sammanfogas till den överordnade disken.


## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

- Du kan redundansväxla en enskild dator eller skapa återställningsplaner för att samordna redundans för flera datorer.
- Du kan köra en planerad eller oplanerad redundans mellan lokala platser. Om du kör en planerad redundansväxling stängs de virtuella källdatorerna av för att säkerställa att inga data går förlorade.
    - Om du utför en oplanerad redundansväxling till en sekundär plats efter att redundansdatorerna på den sekundära platsen inte är skyddade.
    - Om du utför en planerad redundans skyddas datorerna på den sekundära platsen efter redundansen.
- När den inledande redundansen körs etablerar du den för att få åtkomst till arbetsbelastningen från den Virtuella replikdatorn.
- När den primära platsen är tillgänglig igen, kan du återställa dit.
    - Du initiera omvänd replikering för att börja replikera från den sekundära platsen till primärt. Omvänd replikering skyddar de virtuella datorerna, men det sekundära datacentret är fortfarande den aktiva platsen.
    - För att göra den primära platsen till den aktiva platsen igen, initierar du en planerad redundans från sekundär till primär, följt av en till omvänd replikering.



## <a name="next-steps"></a>Nästa steg


Följ [den här självstudien](hyper-v-vmm-disaster-recovery.md) att aktivera Hyper-V-replikering mellan VMM-moln.

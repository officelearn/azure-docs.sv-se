---
title: "Granska arkitekturen för Hyper-V-replikering till en sekundär plats med Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln innehåller en översikt över arkitekturen för replikering av lokala virtuella Hyper-V-datorer till en sekundär System Center VMM-plats med Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 26475782-a21a-408a-b089-35382d7e010e
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: raynew
ms.openlocfilehash: a7a493097a4eaacc2c8d8449906b4a57eb411827
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="hyper-v-replication-to-a-secondary-site"></a>Hyper-V-replikering till en sekundär plats

Den här artikeln beskriver de komponenter och processer som ingår i replikeringen av lokala virtuella Hyper-V-datorer i System Center Virtual Machine Manager-moln (VMM) till en sekundär VMM-plats med tjänsten [Azure Site Recovery](site-recovery-overview.md) på Azure-portalen.


## <a name="architectural-components"></a>Arkitekturkomponenter

Följande tabell och bild ger en övergripande bild av de komponenter som används för Hyper-V-replikering till en sekundär plats.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | Azure-prenumeration | Du kan skapa ett Recovery Services-valv i Azure-prenumerationen för att dirigera och hantera replikeringen mellan VMM-platser.
**VMM-server** | Du behöver en primär och sekundär VMM-plats. | Vi rekommenderar att det finns en VMM-server på den primära platsen och en på den sekundära platsen.
**Hyper-V-server** |  En eller flera Hyper-V-värdservrar i de primära och sekundära VMM-molnen. | Data replikeras mellan de primära och sekundära Hyper-V-värdservrarna via LAN eller VPN med hjälp av Kerberos eller certifikatautentisering.  
**Virtuella Hyper-V-datorer** | På Hyper-V-värdservern. | Källvärdservern måste ha minst en virtuell dator som du vill replikera.

**Lokal till lokal arkitektur**

![Lokal till lokal](./media/concepts-hyper-v-to-secondary-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Replikeringsprocessen

1. När den inledande replikeringen utlöses en [Hyper-V VM-ögonblicksbild](https://technet.microsoft.com/library/dd560637.aspx) ögonblicksbilden tas.
2. Virtuella hårddiskar på den virtuella datorn är replikeras en i taget, till den sekundära platsen.
3. Om det inträffar under den inledande replikeringen pågår 
4. När den inledande replikeringen är klar börjar deltareplikering. Hyper-V-replikering replikering spåraren spårar ändringar som Hyper-V-replikeringsloggar (.hrl). Dessa loggfiler finns i samma mapp som diskarna. Varje disk har en associerad hrl-fil som skickas till den sekundära platsen. Ögonblicksbilden och loggfilerna använder diskresurser när den inledande replikeringen pågår.
5. Diskförändringarna (delta) i loggen synkroniseras och sammanfogas till den överordnade disken.
6. 

## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

1. Du kan växla över en enskild dator eller skapa återställningsplaner för att samordna växling vid fel på flera datorer.
2. Du kan köra en planerad eller oplanerad växling mellan lokala platser. Om du kör en planerad redundansväxling stängs de virtuella källdatorerna av för att säkerställa att inga data går förlorade.
    - Om du gör en oplanerad redundansväxling till en sekundär plats när failover-datorer i den sekundära platsen inte är skyddade.
    - Om du utför en planerad redundans skyddas datorerna på den sekundära platsen efter redundansen.
3. Efter den inledande redundansen körs genomföra den för att starta åt arbetsbelastningen från replikerade virtuella datorn.

När den primära platsen är tillgänglig igen, kan du växla tillbaka.

1. Du kan starta omvänd replikering för att starta replikering från den sekundära platsen till den primära servern. Omvänd replikering skyddar de virtuella datorerna, men det sekundära datacentret är fortfarande den aktiva platsen.
2. För att göra den primära platsen till den aktiva platsen igen, initierar du en planerad redundans från sekundär till primär, följt av en till omvänd replikering.



## <a name="next-steps"></a>Nästa steg

Granska supportmatrisen Följ guiden för att aktivera Hyper-V-replikering mellan VMM-moln.
Kör en redundans och återställning efter fel.

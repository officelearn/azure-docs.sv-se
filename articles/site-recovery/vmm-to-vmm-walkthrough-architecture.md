---
title: "Granska arkitekturen för Hyper-V-replikering till en sekundär plats med Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln innehåller en översikt över arkitekturen för replikering av lokala virtuella Hyper-V-datorer till en sekundär System Center VMM-plats med Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 07099161-4cc7-4f32-8eb9-2a71bbf0750b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: b78cd0d5a5395873afaddc8856004775f447e8ea
ms.contentlocale: sv-se
ms.lasthandoff: 08/01/2017

---
# <a name="step-1-review-the-architecture-for-hyper-v-replication-to-a-secondary-site"></a>Steg 1: Granska arkitekturen för Hyper-V-replikering till en sekundär plats

Den här artikeln beskriver de komponenter och processer som ingår i replikeringen av lokala virtuella Hyper-V-datorer i System Center Virtual Machine Manager-moln (VMM) till en sekundär VMM-plats med tjänsten [Azure Site Recovery](site-recovery-overview.md) på Azure-portalen.

Skriv dina kommentarer längst ned i den här artikeln eller i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="architectural-components"></a>Arkitekturkomponenter

Du behöver följande för att replikera virtuella Hyper-V-datorer till en sekundär VMM-plats.

**Komponent** | **Plats** | **Detaljer**
--- | --- | ---
**Azure** | Prenumeration på Azure. | Du kan skapa ett Recovery Services-valv i Azure-prenumerationen för att dirigera och hantera replikeringen mellan VMM-platser.
**VMM-server** | Du behöver en primär och sekundär VMM-plats. | Vi rekommenderar att det finns en VMM-server på den primära platsen och en på den sekundära platsen 
**Hyper-V-server** |  En eller flera Hyper-V-värdservrar i de primära och sekundära VMM-molnen. | Data replikeras mellan de primära och sekundära Hyper-V-värdservrarna via LAN eller VPN med hjälp av Kerberos eller certifikatautentisering.  
**Virtuella Hyper-V-datorer** | På Hyper-V-värdservern. | Källvärdservern måste ha minst en virtuell dator som du vill replikera.

## <a name="replication-process"></a>Replikeringsprocessen

1. Du konfigurerar Azure-kontot, skapar ett Recovery Services-valv och anger vad du vill replikera.
2. Du konfigurerar replikeringsinställningarna för källan och målet. Som en del i det här steget installerar du Azure Site Recovery-providern på VMM-servrarna och Microsoft Azure Recovery Services-agenten på varje Hyper-V-värd.
3. Du skapar en replikeringsprincip för VMM-källmolnet. Principen tillämpas på alla virtuella datorer som befinner sig på värdar i molnet.
4. Du aktiverar replikering för varje VMM. Den första replikeringen av en virtuell dator sker baserat på de inställningar du väljer.
5. Efter den första replikeringen börjar replikeringen av deltaändringar. Spårade ändringar för ett objekt lagras i en .hrl-fil.


![Lokal till lokal](./media/vmm-to-vmm-walkthrough-architecture/arch-onprem-onprem.png)

## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

1. Du kan köra en planerad eller oplanerad [redundansväxling](site-recovery-failover.md) mellan lokala platser. Om du kör en planerad redundansväxling stängs de virtuella källdatorerna av för att säkerställa att inga data går förlorade.
2. Du kan redundansväxla en enskild dator eller skapa [återställningsplaner](site-recovery-create-recovery-plans.md) för att samordna redundans för flera datorer.
4. Om du utför en oplanerad redundansväxling till en sekundär plats efter att redundansdatorerna på den sekundära platsen inte aktiverats för skydd eller replikering. Om du utför en planerad redundans skyddas datorerna på den sekundära platsen efter redundansen.
5. Sedan kan du etablera redundansen för att få åtkomst till arbetsbelastningen från den virtuella replikdatorn.
6. När din primära plats är tillgänglig igen, kan du initiera omvänd replikering för att replikera från den sekundära platsen till den primära. Omvänd replikering skyddar de virtuella datorerna, men det sekundära datacentret är fortfarande den aktiva platsen.
7. För att göra den primära platsen till den aktiva platsen igen, initierar du en planerad redundans från sekundär till primär, följt av en till omvänd replikering.



## <a name="next-steps"></a>Nästa steg

Gå till [Step 2: Granska kraven och begränsningarna](vmm-to-vmm-walkthrough-prerequisites.md).


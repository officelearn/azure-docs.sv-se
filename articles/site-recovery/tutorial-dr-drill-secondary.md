---
title: "Kör en disaster recovery-gå till en sekundär lokal plats med Azure Site Recovery | Microsoft Docs"
description: "Lär dig mer om med disaster recovery visa till din sekundär lokal plats med Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/07/2018
ms.author: raynew
ms.openlocfilehash: 2e5f8dce1ca2f728d15161622fb9ff2afb4b6c86
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="run-a-disaster-recovery-drill-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>Kör en katastrof återställningsgranskning för Hyper-V virtuella datorer till en sekundär lokal plats

Den [Azure Site Recovery](site-recovery-overview.md) tjänsten bidrar till din strategi för katastrofåterställning genom att hantera och samordna replikering, redundans och återställning efter fel för lokala datorer och virtuella Azure-datorer (VM).

Den här kursen visar hur du kör en katastrof återställningsgranskning för Hyper-V virtuella datorer till en sekundär lokal plats. Hyper-V virtuella datorer måste hanteras i ett privat moln i System Center Virtual Machine Manager (VMM). Visa validerar din replikeringsstrategi för utan dataförlust eller driftstopp och påverkar inte påverkar din produktionsmiljö. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Förbereda förutsättningar för att testa redundans
> * Köra ett redundanstest för en enskild dator


## <a name="prerequisites"></a>Förutsättningar

- Du kan köra ett redundanstest med ett par alternativ för nätverk på den sekundära platsen. Du kan köra redundans utan ett nätverk med ett befintligt nätverk eller låt Site Recovery automatiskt skapa ett testnätverk. 
**Om du vill använda ett befintligt produktionsnätverk för att testa redundans:**:
    - Den primära virtuella datorn måste stängas av när du utför redundanstestet. Annars körs två virtuella datorer med samma identitet i samma nätverk på samma gång. 
    - Om du gör ändringar att testa VMs försvinner ändringarna när du rensar bort redundanstestningen. Ändringarna replikeras inte tillbaka till den primära virtuella datorn.
    - Testa ett produktionsnätverk orsakar driftstopp för produktionsarbetsbelastningar. Be användarna inte att använda relaterade appar när disaster recovery ökad pågår. 
- Testnätverket repliken behöver inte matcha typ av VMM logiskt nätverk som används för att testa redundans. Men vissa kombinationer fungerar inte. Till exempel om repliken använder DHCP- och VLAN-baserad isolering kan du inte använda Windows-Nätverksvirtualisering för nätverket, eftersom den måste IP-adresspooler. 
- Vi rekommenderar att du inte använder det nätverk som du har valt för nätverksmappning för att testa redundans.


## <a name="run-a-test-failover-for-a-vm"></a>Köra ett redundanstest för en virtuell dator

1. I **replikerade objekt**, klicka på den virtuella datorn > **Redundanstest**.
2. I **Redundanstestning**, ange hur test virtuella datorer ansluts till nätverk efter redundanstestet. Vi rekommenderar att för självstudierna kan du låta Site Recovery automatiskt skapa ett testnätverk. [Läs mer](site-recovery-test-failover-vmm-to-vmm.md#network-options-in-site-recovery).
3. Starta redundansväxlingen genom att klicka på **OK**. Följa förloppet på den **jobb** fliken.
4. När redundansväxlingen är klar, kontrollera att testet virtuella datorer starta korrekt.
5. När du är klar klickar du på **Rensa redundanstestet**. Detta tar bort test virtuella datorer och de nätverk som skapades under testning av redundans.
6. I **anteckningar**, registrera och spara observationer från redundanstestningen. 


## <a name="next-steps"></a>Nästa steg

[Kör en produktions-redundans](tutorial-vmm-to-vmm-failover-failback.md)







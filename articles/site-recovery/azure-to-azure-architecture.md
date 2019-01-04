---
title: Arkitektur för Azure till Azure replikering i Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller en översikt över komponenter och arkitektur som används när du konfigurerar haveriberedskap mellan Azure-regioner för virtuella Azure-datorer med hjälp av Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: bb67051ae969497b9e191c0ceb6c0271375e094e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53787686"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Azure-datorer disaster recovery-arkitekturen


Den här artikeln beskriver den arkitektur som används när du distribuerar haveriberedskap med replikering, redundans och återställning av virtuella Azure-datorer (VM) mellan Azure-regioner, med hjälp av den [Azure Site Recovery](site-recovery-overview.md) service.




## <a name="architectural-components"></a>Arkitekturkomponenter

Följande bild visar en övergripande bild av en virtuell dator i Azure-miljö i en viss region (i det här exemplet platsen East USA). I en virtuell dator i Azure-miljö:
- Appar kan köras på virtuella datorer med hanterade diskar eller icke-hanterade diskar som är fördelade på storage-konton.
- De virtuella datorerna kan ingå i ett eller flera undernät inom ett virtuellt nätverk.


**Azure till Azure replikering**

![kund-miljö](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>Replikeringsprocessen

### <a name="step-1"></a>Steg 1

När du aktiverar replikering av virtuella Azure-datorer skapas i följande resurser automatiskt i målregionen, baserat på de nationella inställningarna för källan. Du kan anpassa Målinställningar för resurser som krävs.

![Aktivera replikeringsprocessen, steg 1](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**Resurs** | **Detaljer**
--- | ---
**Målresursgrupp** | Den resursgrupp som replikerade virtuella datorerna tillhör efter en redundansväxling. Platsen för den här resursgruppen kan finnas i alla Azure-regioner utom Azure-regionen som är värd för virtuella källdatorer.
**Virtuellt Målnätverk** | Det virtuella nätverket där replikerade virtuella datorer är placerade efter en redundansväxling. En nätverksmappning skapas mellan käll- och virtuella nätverk och vice versa.
**Cachelagringskonton** | Innan ändringar av datakällan VM replikeras till ett mållagringskonto, de spåras och skickas till cachelagringskontot på källplatsen. Det här steget säkerställer minimal inverkan på produktionsprogram som körs på den virtuella datorn.
**Mållagringskonton (om källan Virtuella måldatorn inte använder hanterade diskar)**  | Storage-konton på målplatsen som data replikeras.
** Hanterade replikeringsdiskar (om källan virtuella datorn finns på hanterade diskar) **  | Hanterade diskar på målplatsen som data ska replikeras.
**Tillgänglighetsuppsättningar för mål**  | Tillgänglighetsuppsättningar i vilka de replikerade virtuella datorerna är placerade efter en redundansväxling.

### <a name="step-2"></a>Steg 2

När replikering har aktiverats, installeras automatiskt mobilitetstjänsten för Site Recovery-tillägget på den virtuella datorn:

1. Den virtuella datorn har registrerats med Site Recovery.

2. Kontinuerlig replikering har konfigurerats för den virtuella datorn. Skrivna data på VM-diskarna överförs kontinuerligt till cachelagringskontot på källplatsen.

   ![Aktivera replikeringsprocessen, steg 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)


 Site Recovery behöver aldrig inkommande anslutningar till den virtuella datorn. Endast utgående anslutning krävs för följande.

 - Site Recovery service-URL: er/IP-adresser
 - Office 365-autentisering-URL: er/IP-adresser
 - IP-adresser för cache storage-konto

Om du aktiverar konsekvens för flera virtuella datorer kommunicerar datorer i replikeringsgruppen med varandra på port 20004. Se till att det inte finns någon brandvägg som blockerar den interna kommunikationen mellan de virtuella datorerna på port 20004.

> [!IMPORTANT]
Om du vill att virtuella datorer med Linux ska vara med i en replikeringsgrupp måste du se till att du manuellt öppnar för utgående trafik på port 20004 enligt riktlinjerna för den specifika Linux-versionen.

### <a name="step-3"></a>Steg 3

När kontinuerlig replikering pågår, Diskskrivningar omedelbart att överföras till cachelagringskontot. Site Recovery bearbetar data och skickar dem till mål-lagringskontot eller repliken hanterade diskar. När data har bearbetats kan skapas återställningspunkter i mållagringskontot några minuters mellanrum.

## <a name="failover-process"></a>Redundans

När du har initierat en redundansväxling kan de virtuella datorerna skapas i målresursgruppen, målets virtuella nätverk, målundernätet, och i tillgänglighetsuppsättningen för målet. Du kan använda någon annan återställningspunkt under en redundansväxling.

![Redundans](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Nästa steg

[Replikera snabbt](azure-to-azure-quickstart.md) en Azure-dator till en sekundär region.

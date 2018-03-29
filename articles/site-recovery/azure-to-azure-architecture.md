---
title: Replikering i Azure till Azure-arkitekturen i Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller en översikt över komponenter och arkitektur som används när du replikerar virtuella Azure-datorer mellan Azure-regioner med hjälp av Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/07/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 111217e9335b16659c93da88731e0b7ce6d5fecd
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="azure-to-azure-replication-architecture"></a>I Azure till Azure-replikeringsarkitektur


Den här artikeln beskriver arkitekturen som används när du replikera växla över och återställa virtuella Azure-datorer (VM) mellan Azure-regioner, med hjälp av den [Azure Site Recovery](site-recovery-overview.md) service.

>[!NOTE]
>Azure VM-replikeringen med Site Recovery-tjänsten är för närvarande under förhandsgranskning.



## <a name="architectural-components"></a>Arkitekturkomponenter

Bilden nedan ger en övergripande bild av en Azure VM-miljö i en viss region (i det här exemplet östra USA platsen). I en miljö med virtuella Azure-datorn:
- Appar kan köras på virtuella datorer med hanterade diskar eller icke-hanterade diskar som är fördelade på storage-konton.
- De virtuella datorerna kan ingå i en eller flera undernät i ett virtuellt nätverk.


**Azure till Azure-replikering**

![customer-environment](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>Replikeringsprocessen

### <a name="step-1"></a>Steg 1

När du aktiverar Azure VM-replikering skapas i följande resurser automatiskt i målregionen, baserat på inställningarna för datakälla region. Du kan anpassa inställningar i target-resurser som krävs.

![Aktivera replikeringen steg 1](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**Resurs** | **Detaljer**
--- | ---
**Målresursgruppen** | Resursgruppen som tillhör replikerade virtuella datorer efter redundans.
**Mål virtuellt nätverk** | Det virtuella nätverket som replikerade virtuella datorer finns efter växling vid fel. En nätverksmappning skapas mellan käll- och virtuella nätverk och vice versa.
**Cache-lagringskonton** | De är spåras och skickas till cache-lagringskonto i källplats innan ändringar av datakällan VM replikeras till en mål-lagringskontot. Det här steget säkerställer minimal inverkan på produktionsprogram som körs på den virtuella datorn.
**Rikta storage-konton (om datakällan inte använder VM-hanterade diskar)**  | Storage-konton på målplatsen data replikeras.
** Replik hanterade diskar (om källa VM finns på hanterade diskar) **  | Hanterade diskar på målplatsen data replikeras.
**Mål-tillgänglighetsuppsättningar**  | Tillgänglighetsuppsättningar i som de replikerade virtuella datorerna är placerade efter växling vid fel.

### <a name="step-2"></a>Steg 2

Eftersom replikering är aktiverat, installeras automatiskt Site Recovery-tillägget mobilitetstjänsten på den virtuella datorn:

1. Den virtuella datorn har registrerats med Site Recovery.

2. Kontinuerlig replikering har konfigurerats för den virtuella datorn. Skrivning av data på Virtuella diskar överförs kontinuerligt till lagringskontot cache på källplatsen.

   ![Aktivera replikeringen steg 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)


 Site Recovery behöver aldrig inkommande anslutning till den virtuella datorn. Endast utgående anslutning krävs för följande.

 - Site Recovery-tjänsten URL-adresser/IP-adresser
 - URL: er/IP-adresser och Office 365-autentisering
 - Cache-lagring konto IP-adresser

Om du aktiverar konsekvens för flera virtuella datorer kommunicerar datorer i replikeringsgruppen med varandra på port 20004. Se till att det inte finns någon brandvägg som blockerar den interna kommunikationen mellan de virtuella datorerna på port 20004.

> [!IMPORTANT]
Om du vill att virtuella datorer med Linux ska vara med i en replikeringsgrupp måste du se till att du manuellt öppnar för utgående trafik på port 20004 enligt riktlinjerna för den specifika Linux-versionen.

### <a name="step-3"></a>Steg 3

När kontinuerlig replikering pågår överföras Diskskrivningar omedelbart till cache-lagringskontot. Site Recovery bearbetar data och skickar den till mål-lagringskontot eller replik hanterade diskar. När data har bearbetats skapas återställningspunkter i mål-lagringskontot med några minuters mellanrum.

## <a name="failover-process"></a>Failover-processen

När du initierar en växling vid fel, virtuella datorer skapas i målresursgruppen, virtuella målnätverket, undernätet mål, och i tillgänglighetsuppsättningen för mål. Du kan använda valfri återställningspunkt under en redundansväxling.

![Failover-processen](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Nästa steg

[Replikera snabbt](azure-to-azure-quickstart.md) en Azure VM till en sekundär region.

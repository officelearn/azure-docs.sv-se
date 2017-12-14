---
title: "Granska arkitektur för replikering av virtuella Azure-datorer mellan Azure-regioner | Microsoft Docs"
description: "Den här artikeln innehåller en översikt över komponenter och arkitektur som används när du replikerar virtuella Azure-datorer mellan Azure-regioner med hjälp av Azure Site Recovery-tjänsten."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/08/2017
ms.author: raynew
ms.openlocfilehash: 8251534b2e1e0d223f5e1df5dbd33831604615cb
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="azure-to-azure-replication-architecture"></a>I Azure till Azure-replikeringsarkitektur


Den här artikeln beskriver arkitektur och processer som används när du replikera växla över och återställa virtuella Azure-datorer (VM) mellan Azure-regioner, med hjälp av den [Azure Site Recovery](site-recovery-overview.md) service.

>[!NOTE]
>Azure VM-replikeringen med Site Recovery-tjänsten är för närvarande under förhandsgranskning.



## <a name="architectural-components"></a>Arkitekturkomponenter

Bilden nedan ger en övergripande bild av en Azure VM-miljö i en viss region (i det här exemplet östra USA platsen). I en miljö med virtuella Azure-datorn:
- Appar kan köras på virtuella datorer med diskar som är fördelade på storage-konton.
- De virtuella datorerna kan ingå i en eller flera undernät i ett virtuellt nätverk.


**Azure till Azure-replikering**

![kund-miljö](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>Replikeringsprocessen

### <a name="step-1"></a>Steg 1

När du aktiverar replikering av Azure Virtuella skapas resurser som visas nedan automatiskt i målregionen, baserat på inställningarna för datakälla region. Du kan anpassa inställningar i target-resurser som krävs. 

![Aktivera replikeringen steg 1](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**Resurs** | **Detaljer**
--- | ---
**Målresursgruppen** | Resursgruppen som tillhör replikerade virtuella datorer efter redundans.
**Mål virtuellt nätverk** | Det virtuella nätverket som replikerade virtuella datorer finns efter växling vid fel. En nätverksmappning skapas mellan käll- och virtuella nätverk och vice versa.
**Cache-lagringskonton** | Innan ändringar av datakällan virtuella datorer replikeras till en mål-lagringskontot de spåras och skickas till cachen storage-konto på målplatsen. Detta säkerställer minimal inverkan på produktion appar som körs på den virtuella datorn.
**Mål-lagringskonton**  | Storage-konton på målplatsen data replikeras.
**Mål-tillgänglighetsuppsättningar**  | Tillgänglighetsuppsättningar i som de replikerade virtuella datorerna är placerade efter växling vid fel.

### <a name="step-2"></a>Steg 2

Eftersom replikering är aktiverat, installeras automatiskt Site Recovery-tillägget mobilitetstjänsten på den virtuella datorn:

1. Den virtuella datorn har registrerats med Site Recovery.

2. Kontinuerlig replikering har konfigurerats för den virtuella datorn. Skrivning av data på Virtuella diskar överförs kontinuerligt till lagringskontot cache på källplatsen.

   ![Aktivera replikeringen steg 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

  
 Site Recovery behöver aldrig inkommande anslutning till den virtuella datorn. Endast utgående anslutning krävs till Site Recovery-tjänsten URL-adresser/IP-adresser, Office 365-autentisering URL: er/IP-adresser och IP-adresser för cache storage-konto.

### <a name="step-3"></a>Steg 3

När kontinuerlig replikering pågår överföras Diskskrivningar omedelbart till cache-lagringskontot. Site Recovery bearbetar data och skickar det till mål-lagringskontot. När data har bearbetats skapas återställningspunkter i mål-lagringskontot med några minuters mellanrum.

## <a name="failover-process"></a>Failover-processen

När du initierar en växling vid fel, virtuella datorer skapas i målresursgruppen, virtuella målnätverket, undernätet mål, och i tillgänglighetsuppsättningen för mål. Du kan använda valfri återställningspunkt under en redundansväxling.

![Failover-processen](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Nästa steg

Granska supportmatrisen Följ guiden för att aktivera replikering för virtuella Azure-datorn till en sekundär region.
Kör en redundans och återställning efter fel.
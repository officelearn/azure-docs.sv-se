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
ms.date: 07/12/2017
ms.author: raynew
ms.openlocfilehash: f471add4f4dee26482e2820fb06d010d6c0c0e88
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="step-1-review-the-architecture-for-azure-vm-replication-between-azure-regions"></a>Steg 1: Granska arkitektur för Azure VM replikering mellan Azure-regioner


När du har granskat den [översikt över stegen](azure-to-azure-walkthrough-overview.md) för den här distributionen den här artikeln för att förstå komponenterna och processer som används när replikering och återställa virtuella Azure-datorer (VM) från en Azure-region till en annan, med hjälp av [ Azure Site Recovery](site-recovery-overview.md).

- När du är klar artikeln bör du ha en förståelse för hur fungerar Azure VM replikering till en annan region.
- Skriv dina kommentarer eller frågor längst ned i den här artikeln eller i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

>[!NOTE]
>Azure VM-replikeringen med Site Recovery-tjänsten är för närvarande under förhandsgranskning.



## <a name="architectural-components"></a>Arkitekturkomponenter

Följande diagram ger en övergripande bild av en Azure VM-miljö i en viss region (i det här exemplet östra USA platsen). I en miljö med virtuella Azure-datorn:
- Appar kan köras på virtuella datorer med diskar som är fördelade på storage-konton.
- De virtuella datorerna kan ingå i en eller flera undernät i ett virtuellt nätverk.

![kund-miljö](./media/azure-to-azure-walkthrough-architecture/source-environment.png)

## <a name="replication-process"></a>Replikeringsprocessen

### <a name="step-1"></a>Steg 1

När du aktiverar Azure VM-replikering i Azure-portalen, skapas automatiskt resurserna som visas i följande diagram och tabellen i mål-region. Som standard skapas resurser baserat på inställningarna för datakälla region. Du kan anpassa Målinställningar efter behov. [Läs mer](site-recovery-replicate-azure-to-azure.md).

![Aktivera replikeringen steg 1](./media/azure-to-azure-walkthrough-architecture/enable-replication-step-1.png)

**Resurs** | **Detaljer**
--- | ---
**Målresursgruppen** | Resursgruppen som tillhör replikerade virtuella datorer efter redundans.
**Mål virtuellt nätverk** | Det virtuella nätverket som replikerade virtuella datorer finns efter växling vid fel. En nätverksmappning skapas mellan käll- och virtuella nätverk och vice versa.
**Cache-lagringskonton** | Innan ändringar på virtuella källdatorer replikeras till mål-lagringskontot de spåras och skickas till cachen storage-konto på målplatsen. Detta säkerställer minimal inverkan på produktion appar som körs på den virtuella datorn.
**Mål-lagringskonton**  | Storage-konton på målplatsen data replikeras.
**Mål-tillgänglighetsuppsättningar**  | Tillgänglighetsuppsättningar i som de replikerade virtuella datorerna är placerade efter växling vid fel.

### <a name="step-2"></a>Steg 2

Eftersom replikering är aktiverat, installeras automatiskt Site Recovery-tillägget mobilitetstjänsten på den virtuella datorn. Inträffar följande:

1. Den virtuella datorn har registrerats med Site Recovery.

2. Kontinuerlig replikering har konfigurerats för den virtuella datorn. Skrivning av data på Virtuella diskar överförs kontinuerligt till lagringskontot cache på källplatsen.

   ![Aktivera replikeringen steg 2](./media/azure-to-azure-walkthrough-architecture/enable-replication-step-2.png)

  
  Observera att Site Recovery aldrig ha inkommande anslutning till den virtuella datorn. Endast krävs utgående anslutning till Site Recovery-tjänsten URL-adresser/IP-adresser, Office 365 autentisering URL: er/IP-adresser och IP-adresser för cache storage-konto. 

## <a name="continuous-replication-process"></a>Kontinuerlig replikeringsprocess

När kontinuerlig replikering fungerar överföras Diskskrivningar omedelbart till cache-lagringskontot. Site Recovery bearbetar data och skickar det till mål-lagringskontot. När data har bearbetats skapas återställningspunkter i mål-lagringskontot med några minuters mellanrum.

## <a name="failover-process"></a>Failover-processen

När du påbörja en växling virtuella datorer skapas i målresursgruppen, virtuella målnätverket mål undernät och mål-tillgänglighet. Du kan använda valfri återställningspunkt under en redundansväxling.

![Failover-processen](./media/azure-to-azure-walkthrough-architecture/failover.png)

## <a name="next-steps"></a>Nästa steg

Gå till [steg 2: Verifiera förutsättningar och begränsningar](azure-to-azure-walkthrough-prerequisites.md)

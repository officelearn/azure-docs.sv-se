---
title: Hur fungerar Azure replikeringen mellan Azure-regioner i Azure Site Recovery?  | Microsoft Docs
description: "Den här artikeln innehåller en översikt över komponenter och arkitektur som används när du replikerar virtuella Azure-datorer mellan Azure-regioner med hjälp av Azure Site Recovery-tjänsten."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.openlocfilehash: 7a3e1eb129db530295cbf28bbce1dbe48575d50d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-does-azure-vm-replication-work-in-site-recovery"></a>Hur fungerar Azure VM-replikering i Site Recovery?


Den här artikeln beskrivs de komponenter och processer som är involverad i replikera och återställa virtuella Azure-datorer (VM) från en region till ett annat med hjälp av den [Azure Site Recovery](site-recovery-overview.md) service.

>[!NOTE]
>Azure VM-replikeringen med Site Recovery-tjänsten är för närvarande under förhandsgranskning.

Skriv dina kommentarer eller frågor längst ned i den här artikeln eller i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="architectural-components"></a>Arkitekturkomponenter

Följande diagram ger en övergripande bild av en Azure VM-miljö i en viss region (i det här exemplet östra USA platsen). I en miljö med virtuella Azure-datorn:
- Appar kan köras på virtuella datorer med diskar som är fördelade på storage-konton.
- De virtuella datorerna kan ingå i en eller flera undernät i ett virtuellt nätverk.

![kund-miljö](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Lär dig mer om kraven för distribution och kraven i de [supportmatrisen](site-recovery-support-matrix-azure-to-azure.md).

## <a name="replication-process"></a>Replikeringsprocessen

### <a name="step-1"></a>Steg 1

När du aktiverar Azure VM-replikering i Azure-portalen, skapas automatiskt resurserna som visas i följande diagram och tabellen i mål-region. Som standard skapas resurser baserat på inställningarna för datakälla region. Du kan anpassa Målinställningar efter behov. [Läs mer](site-recovery-replicate-azure-to-azure.md).

![Aktivera replikeringen steg 1](./media/site-recovery-azure-to-azure-architecture/enable-replication-step-1.png)

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

   ![Aktivera replikeringen steg 2](./media/site-recovery-azure-to-azure-architecture/enable-replication-step-2.png)

   >[!IMPORTANT]
   > Site Recovery behöver aldrig inkommande anslutning till den virtuella datorn. Den virtuella datorn måste bara utgående anslutning till Site Recovery-tjänsten URL-adresser/IP-adresser, Office 365 autentisering URL: er/IP-adresser och IP-adresser för cache storage-konto. Mer information finns i [nätverk vägledning för att replikera virtuella datorer i Azure](site-recovery-azure-to-azure-networking-guidance.md) artikel.

## <a name="continuous-replication-process"></a>Kontinuerlig replikeringsprocess

När kontinuerlig replikering fungerar överföras Diskskrivningar omedelbart till cache-lagringskontot. Site Recovery bearbetar data och skickar det till mål-lagringskontot. När data har bearbetats skapas återställningspunkter i mål-lagringskontot med några minuters mellanrum.

## <a name="failover-process"></a>Failover-processen

När du påbörja en växling virtuella datorer skapas i målresursgruppen, virtuella målnätverket mål undernät och mål-tillgänglighet. Du kan använda valfri återställningspunkt under en redundansväxling.

![Failover-processen](./media/site-recovery-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [nätverk](site-recovery-azure-to-azure-networking-guidance.md) för Azure VM-replikering.
- Följ en genomgång till [replikera virtuella datorer i Azure.](site-recovery-azure-to-azure.md)

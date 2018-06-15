---
title: Hantera anslutna partnerlösningar i Azure Security Center | Microsoft Docs
description: I det här dokumentet går vi igenom hur du i Azure Security Center  enkelt kan övervaka hälsostatusen på de partnerlösningar som är integrerade i Azureprenumerationen.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: terrylan
ms.openlocfilehash: 181e1e00716987732ee809df6171c2f71087f3e1
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
ms.locfileid: "23891029"
---
# <a name="managing-connected-partner-solutions-with-azure-security-center"></a>Hantera anslutna partnerlösningar med Azure Security Center
Den här artikeln får du hjälp att hantera och övervaka anslutna säkerhetslösningar i Azure Security Center.

## <a name="monitoring-partner-solutions"></a>Övervaka partnerlösningar
Att övervaka hälsotillståndet för anslutna säkerhetslösningar och utföra grundläggande hantering:

1. Under **Security Center - översikt**väljer **säkerhetslösningar**.

  ![Välj säkerhetslösningar][1]

  Den **anslutna lösningar** beskrivs säkerhetslösningar som är anslutna till Security Center och information om hälsostatus för varje lösning.

  ![Partnerlösningar][2]

   Status för en partnerlösning kan vara:

   * Felfritt (grönt) - det finns inga problem.
   * Inte väl skyddad (röd): Det finns säkerhetsproblem som måste åtgärdas omedelbart.
   * Problem (orange): lösningen har slutat rapportera dess hälsa.
   * Inga rapporter (grå) - lösningen har inte rapporterat något ännu, status för en lösning kan vara orapporterad om det nyligen har anslutits och fortfarande distribuera eller finns inga tillgängliga hälsotillståndsdata.

   > [!NOTE]
   > Om hälsa statusdata inte är tillgänglig visar Security Center datum och tid för senaste händelsen togs emot som anger om lösningen reporting eller inte. Om inga hälsa data är tillgängliga och inga aviseringar tas emot under de senaste 14 dagarna, anger Security Center att lösningen är ogiltigt eller inte reporting.
   >
   >

2. Välj **visa** för ytterligare information och alternativ, vilket inkluderar:

  - **Lösning konsolen**. Öppnar hanteringen av den här lösningen.
  - **Länka VM**. Öppnar bladet länken program. Här kan du ansluta resurser till partnerlösningen.
  - **Ta bort lösning**.
  - **Konfigurera**.

   ![Partnerlösningsinformation][3]

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du hantera och övervaka anslutna säkerhetslösningar i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Översikt över lösningar med säkerhet](security-center-partner-integration.md) – Lär dig att ansluta och hantera lösningar för informationssäkerhet.
* [Ansluta Microsoft Advanced Threat Analytics (ATA)](security-center-ata-integration.md) – Lär dig hur du ansluter aviseringar från ATA.
* [Ansluta Azure Active Directory (AD) identitetsskydd ](security-center-aadip-integration.md) – Lär dig hur du ansluter aviseringar från Azure AD Identity Protection.
* [Integrering med partner och lösningar](security-center-partner-integration.md) -få en översikt över integrering av andra lösningar för informationssäkerhet.
* [Hantera och åtgärda säkerhetsaviseringar](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/partner-solutions.png
[3]: ./media/security-center-partner-solutions/partner-solutions-detail.png

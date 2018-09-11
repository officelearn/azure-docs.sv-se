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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/20/2018
ms.author: terrylan
ms.openlocfilehash: 3174f2d6562030702b14ef1fa3708a1a4e8e373b
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301957"
---
# <a name="managing-connected-partner-solutions-with-azure-security-center"></a>Hantera anslutna partnerlösningar med Azure Security Center
Den här artikeln får du veta hur att hantera och övervaka anslutna säkerhetslösningar i Azure Security Center.

## <a name="monitoring-partner-solutions"></a>Övervaka partnerlösningar
Övervaka hälsotillståndet för anslutna säkerhetslösningar och utföra grundläggande hanteringsåtgärder:

1. Under **Security Center – översikt**väljer **säkerhetslösningar**.

  ![Välj säkerhetslösningar][1]

  Den **anslutna lösningar** innehåller säkerhetslösningar som är anslutna till Security Center och information om hälsostatus för varje lösning.

  ![Partnerlösningar][2]

   Status för en partnerlösning kan vara:

   * Felfritt (grönt) - det finns inga problem.
   * Inte väl skyddad (röd): Det finns säkerhetsproblem som måste åtgärdas omedelbart.
   * Problem med hälsotillstånd (orange) - lösningen har slutat rapportera dess hälsa.
   * Inga rapporter (grå) - lösningen har inte rapporterat något ännu, status för en lösning kan vara orapporterad om den nyligen har anslutits och fortfarande pågår eller inga health-data är tillgängliga.

   > [!NOTE]
   > Om hälsodata status inte är tillgänglig visar Security Center datum och tid för senaste händelsen togs emot för att indikera om lösningen rapporterar eller inte. Om inga health-data är tillgängliga och inga aviseringar tas emot inom de senaste 14 dagarna, anger Security Center att lösningen är defekt eller inte reporting.
   >
   >

2. Välj **visa** för ytterligare information och alternativ, vilket inkluderar:

  - **Lösningskonsol**. Öppnar hanteringsupplevelse för den här lösningen.
  - **Länka VM**. Öppnas bladet länka program. Här kan du ansluta resurser till partnerlösningen.
  - **Ta bort lösningen**.
  - **Konfigurera**.

   ![Partnerlösningsinformation][3]

## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig att hantera och övervaka anslutna säkerhetslösningar i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Översikt över lösningar med säkerhet](security-center-partner-integration.md) – Lär dig att ansluta och hantera säkerhetslösningar.
* [Ansluta Microsoft Advanced Threat Analytics (ATA)](security-center-ata-integration.md) – Lär dig hur du ansluter aviseringar från ATA.
* [Ansluta Azure Active Directory (AD) Identity Protection ](security-center-aadip-integration.md) – Lär dig hur du ansluter aviseringar från Azure AD Identity Protection.
* [Partnerintegrering](security-center-partner-integration.md) – få en översikt över integrering av andra säkerhetslösningar.
* [Hantera och åtgärda säkerhetsaviseringar](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/partner-solutions.png
[3]: ./media/security-center-partner-solutions/partner-solutions-detail.png

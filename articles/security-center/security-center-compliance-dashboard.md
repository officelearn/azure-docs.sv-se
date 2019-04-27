---
title: Förbättra din regelefterlevnad med hjälp av Azure Security Center | Microsoft Docs
description: 'Självstudier: Lär dig hur du kan förbättra din regelefterlevnad med hjälp av Azure Security Center.'
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 2e73cbd1de4336a08e26a609dfe704da0a068860
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60706031"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Självstudier: Förbättra regelefterlevnaden
---

Azure Security Center hjälper till att förenkla processen för att uppfylla efterlevnadskrav med instrumentpanelen för regelefterlevnad. I instrumentpanelen ger dig Security Center insikter om din efterlevnadsstatus baserat på kontinuerliga utvärderingar av Azure-miljön. Utvärderingar som utförs av Security Center analyserar riskfaktorer i hybridmolnmiljön i enlighet med bästa praxis för säkerhet. Dessa utvärderingar mappas till kompatibilitetskontroller från en uppsättning standarder som stöds. I instrumentpanelen för regelefterlevnad har du en överblick över statusen för alla dessa utvärderingar i din miljö när det gäller en viss standard eller förordning. Du kan se att din efterlevnadsstatus förbättras när du arbetar med rekommendationerna och minskar riskfaktorer i din miljö.

I den här självstudien får du lära dig hur man:

-   Utvärdera din regelefterlevnad med hjälp av instrumentpanelen för regelefterlevnad

-   Förbättra din efterlevnadsstatus genom att vidta åtgärder enligt rekommendationer

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att gå igenom funktionerna i den här självstudien måste du ha standardnivån i Security Center. Du kan prova Security Center Standard utan kostnad.
Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/). Snabbstarten för att [registrera Azure-prenumerationen till Security Center Standard](https://docs.microsoft.com/azure/security-center/security-center-get-started) vägleder dig genom uppgraderingen till Standard.

##  <a name="assess-your-regulatory-compliance"></a>Utvärdera din regelefterlevnad

Security Center utvärderar kontinuerligt konfigurationen av dina resurser för att identifiera säkerhetsproblem. Dessa utvärderingar presenteras som rekommendationer som fokuserar på att förbättra din säkerhetshygien. På instrumentpanelen för regelefterlevnad kan du visa en uppsättning efterlevnadsstandarder med deras krav, där krav som stöds är mappade till tillämpliga säkerhetsutvärderingar. Det gör att du kan visa din efterlevnadsstatus med avseende på standard, baserat på statusen för dessa utvärderingar.

Instrumentpanelens vy för efterlevnad hjälper dig att fokusera din uppmärksamhet på efterlevnadsluckor i enlighet med en standard eller förordning som är viktig för dig. Med den här fokuserade vyn kan du övervaka din kompatibilitetspoäng över tid i dynamiska moln- och hybridmiljöer.

>[!NOTE]
> Regelverk som stöds för närvarande är: Azure CIS, PCI DSS 3.2, ISO 27001 och SOC TSP. Ytterligare standarder återspeglas i instrumentpanelen i takt med att de utvecklas.

1.  På Security Center-huvudmenyn under **PRINCIP OCH EFTERLEVNAD** väljer du **instrumentpanelen för regelefterlevnad**. <br>
Överst på skärmen visas en instrumentpanel med en översikt över din efterlevnadsstatus med en uppsättning efterlevnadsföreskrifter som stöds. Du kan se din övergripande kompatibilitetspoäng och antalet godkända och icke godkända utvärderingar som är associerade med varje standard.

    ![hög exakthet för datorbeskrivning](./media/security-center-compliance-dashboard/compliance-dashboard.png)


2.  Välj en flik för en efterlevnadsstandard som är relevant för dig. Du ser en lista över alla kontroller för den standarden. För tillämpliga kontroller kan du visa information om godkända och icke godkända utvärderingar som är associerade med denna kontroll. En del kontroller är nedtonade. De här kontrollerna har inga Security Center-utvärderingar associerade till sig. Du behöver analysera kraven för dem och utvärdera dem i din miljö på egen hand. Vissa av dem kan vara processrelaterade och inte tekniska.

    ![efterlevnadsflik](./media/security-center-compliance-dashboard/compliance-pci.png)

3.  Välj fliken **Alla** för att se en vy över alla relevanta rekommendationer i Security Center och deras tillhörande standarder. Den här vyn kan användas för att identifiera alla olika standarder som påverkas av en viss rekommendation. <br> Du kan eventuellt använda den här vyn för att prioritera rekommendationer som du måste åtgärda. Exempel: Om du ser att rekommendationen **Enable MFA for accounts with owner permissions on your subscription** (Aktivera MFA för konton med ägarbehörigheter för din prenumeration) misslyckas på flera resurser och är associerad med flera standarder får den rekommendationen hög inverkan på din övergripande kompatibilitetspoäng.

    ![kompatibilitetspoängpåverkan](./media/security-center-compliance-dashboard/compliance-all-tabs.png)

## <a name="improve-your-compliance-posture"></a>Förbättra din efterlevnadsstatus


Med tanke på informationen i instrumentpanelen för regelefterlevnad kan du förbättra din efterlevnadsstatus genom att utföra rekommendationer direkt i instrumentpanelen.

1.  Klicka dig igenom några icke godkända utvärderingar som visas i instrumentpanelen för att visa information om den här rekommendationen. Varje rekommendation innehåller en uppsättning steg som ska följas för att lösa problemet.

2.  Du kan välja en viss resurs för att visa mer information och lösa rekommendationerna för den resursen. <br>På exempelvis fliken **Azure CIS-standard** kan du klicka på rekommendationen **Require secure transfer to storage account** (Kräv säker överföring till lagringskonto).

    ![rekommendation för efterlevnad](./media/security-center-compliance-dashboard/compliance-recommendation.png)

3. När du klickar dig igenom rekommendationens information och väljer en resurs som inte är felfri leder den dig direkt till miljön för att aktivera **säker lagringsöverföring** i Azure-portalen.<br>Mer information om hur du utför rekommendationerna finns i artikeln [Utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md).

    ![rekommendation för efterlevnad](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

4.  När du vidtar åtgärder för att lösa rekommendationerna visas din påverkan i rapporten i instrumentpanelen för efterlevnad eftersom din kompatibilitetspoäng ökar.

    > [!NOTE]
    > Utvärderingar körs ungefär var 12:e timme, så du ser din påverkan på dina efterlevnadsdata när utvärderingarna har körts.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om hur du använder instrumentpanelen för Security Center-regelefterlevnad för att:

-   Visa och övervaka din efterlevnadsstatus i förhållande till standarder och föreskrifter som är viktiga för dig.

-   Förbättra din kompatibilitetsstatus genom att utföra relevanta rekommendationer och se dina kompatibilitetspoäng öka.

Instrumentpanelen för regelefterlevnad kan förenkla processen för regelefterlevnad och avsevärt minska den tid som krävs för att samla in bevis på efterlevnad för Azure- och hybrid-miljön.

I följande avsnitt kan du lära dig mer om Security Center:

-   [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.

-   [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig att skydda dina Azure-resurser i Azure Security Center med hjälp av rekommendationer.

-   [Förbättra dina säkerhetspoäng i Azure Security Center](security-center-secure-score.md) – Lär dig hur du prioriterar säkerhetsproblem och säkerhetsrekommendationer och på bästa sätt förbättrar din säkerhetshållning.

-   [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.

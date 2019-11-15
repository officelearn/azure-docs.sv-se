---
title: 'Självstudie: kontroll av regelefterlevnad – Azure Security Center'
description: 'Självstudie: Lär dig hur du kan förbättra din efterlevnad med hjälp av Azure Security Center.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2019
ms.author: memildin
ms.openlocfilehash: 01c0f1199ab8c376fe5bb5e864f81a2730879ed1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112314"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Självstudie: förbättra din regelefterlevnad
---

Azure Security Center hjälper till att effektivisera processen för att uppfylla krav på efterlevnad av efterlevnad, med hjälp av **instrument panelen för kontroll av efterlevnad**. I instrumentpanelen ger dig Security Center insikter om din efterlevnadsstatus baserat på kontinuerliga utvärderingar av Azure-miljön. Security Center analyserar riskfaktorer i din hybrid moln miljö enligt rekommenderade säkerhets metoder. Dessa utvärderingar mappas till kompatibilitetskontroller från en uppsättning standarder som stöds. På instrument panelen för kontroll av efterlevnad kan du se status för alla utvärderingar i din miljö i samband med en viss standard eller regel. När du arbetar med rekommendationerna och minskar riskfaktorerna i din miljö förbättras position.

I den här självstudien får du lära dig hur man:

-   Utvärdera din regelefterlevnad med hjälp av instrumentpanelen för regelefterlevnad

-   Förbättra din efterlevnadsstatus genom att vidta åtgärder enligt rekommendationer

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att gå igenom funktionerna i den här självstudien måste du ha standardnivån i Security Center. Du kan prova Security Center Standard utan kostnad.
Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/). Snabbstarten för att [registrera Azure-prenumerationen till Security Center Standard](https://docs.microsoft.com/azure/security-center/security-center-get-started) vägleder dig genom uppgraderingen till Standard.

##  <a name="assess-your-regulatory-compliance"></a>Utvärdera din regelefterlevnad

Security Center utvärderar kontinuerligt konfigurationen av dina resurser för att identifiera säkerhetsproblem. Dessa utvärderingar presenteras som rekommendationer som fokuserar på att förbättra din säkerhetshygien. På instrumentpanelen för regelefterlevnad kan du visa en uppsättning efterlevnadsstandarder med deras krav, där krav som stöds är mappade till tillämpliga säkerhetsutvärderingar. Det gör att du kan visa din efterlevnadsstatus med avseende på standard, baserat på statusen för dessa utvärderingar.

Instrumentpanelens vy för efterlevnad hjälper dig att fokusera din uppmärksamhet på efterlevnadsluckor i enlighet med en standard eller förordning som är viktig för dig. Med den här fokuserade vyn kan du övervaka din kompatibilitetspoäng över tid i dynamiska moln- och hybridmiljöer.

>[!NOTE]
> Som standard stöder Security Center följande reglerande standarder: Azure CIS, PCI DSS 3,2, ISO 27001 och SOC TSP. 
>
> Med funktionen [Dynamic Compliance packages (för hands version)](update-regulatory-compliance-packages.md) kan du uppgradera de standarder som visas på instrument panelen för kontroll av efterlevnad till de nya *dynamiska* paketen. Du kan också använda samma förhands gransknings funktion för att lägga till nya efterlevnadsprinciper och övervaka efterlevnaden av ytterligare standarder. 

1.  I Security Center huvud menyn, under **princip & regelefterlevnad** väljer du **regelefterlevnad.** <br>
Överst på skärmen visas en instrumentpanel med en översikt över din efterlevnadsstatus med en uppsättning efterlevnadsföreskrifter som stöds. Du kan se din övergripande kompatibilitetspoäng och antalet godkända och icke godkända utvärderingar som är associerade med varje standard.

    ![hög exakthet för datorbeskrivning](./media/security-center-compliance-dashboard/compliance-dashboard.png)

2.  Välj en flik för en efterlevnadsstandard som är relevant för dig. Du ser en lista över alla kontroller för den standarden. För tillämpliga kontroller kan du visa information om godkända och icke godkända utvärderingar som är associerade med denna kontroll. Vissa kontroller är nedtonade. De här kontrollerna har inga associerade Security Center-utvärderingar. Kontrol lera kraven för dessa och utvärdera dem i din miljö på egen hand. Vissa av dem kan vara processrelaterade och inte tekniska.

    ![efterlevnadsflik](./media/security-center-compliance-dashboard/compliance-pci.png)

1. Klicka på **Hämta rapport**om du vill skapa och ladda ned en PDF-rapport som sammanfattar din aktuella kompatibilitetsstatus för en viss standard.

    Rapporten ger en översikt över din kompatibilitetsstatus för den valda standarden baserat på Security Center utvärderings data och organiseras enligt kontrollerna i denna specifika standard. Rapporten kan delas med relevanta intressenter och kan användas för att tillhandahålla bevis till interna och externa revisorer.

    ![ladda ned](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>Förbättra din efterlevnadsstatus

Med tanke på informationen i instrumentpanelen för regelefterlevnad kan du förbättra din efterlevnadsstatus genom att utföra rekommendationer direkt i instrumentpanelen.

1.  Klicka dig igenom några icke godkända utvärderingar som visas i instrumentpanelen för att visa information om den här rekommendationen. Varje rekommendation innehåller en uppsättning steg som ska följas för att lösa problemet.

1.  Du kan välja en viss resurs för att visa mer information och lösa rekommendationerna för den resursen. <br>På exempelvis fliken **Azure CIS-standard** kan du klicka på rekommendationen **Require secure transfer to storage account** (Kräv säker överföring till lagringskonto).

    ![rekommendation för efterlevnad](./media/security-center-compliance-dashboard/compliance-recommendation.png)

1. När du klickar dig igenom rekommendationens information och väljer en resurs som inte är felfri leder den dig direkt till miljön för att aktivera **säker lagringsöverföring** i Azure-portalen.

    Mer information om hur du utför rekommendationerna finns i artikeln [Utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md).

    ![rekommendation för efterlevnad](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

1.  När du vidtar åtgärder för att lösa rekommendationerna visas din påverkan i rapporten i instrumentpanelen för efterlevnad eftersom din kompatibilitetspoäng ökar.

    > [!NOTE]
    > Utvärderingar körs ungefär var 12:e timme, så du ser din påverkan på dina efterlevnadsdata när utvärderingarna har körts.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om hur du använder instrumentpanelen för Security Center-regelefterlevnad för att:

-   Visa och övervaka din efterlevnadsstatus i förhållande till standarder och föreskrifter som är viktiga för dig.

-   Förbättra din kompatibilitetsstatus genom att utföra relevanta rekommendationer och se dina kompatibilitetspoäng öka.

Instrumentpanelen för regelefterlevnad kan förenkla processen för regelefterlevnad och avsevärt minska den tid som krävs för att samla in bevis på efterlevnad för Azure- och hybrid-miljön.

Mer information finns i:

-   [Uppdatera till dynamiska efterlevnadsprinciper i din instrument panel för efterlevnad (för hands version)](update-regulatory-compliance-packages.md) – Läs mer om den här förhands gransknings funktionen som gör att du kan uppdatera de standarder som visas på instrument panelen för regelefterlevnad till de nya *dynamiska* paketen. Du kan också använda samma förhands gransknings funktion för att lägga till nya efterlevnadsprinciper och övervaka efterlevnaden av ytterligare standarder. 

-   [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md) – lär dig hur du övervakar Azure-resursernas hälsa.

-   [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md) – lär dig hur du använder rekommendationer i Azure Security Center för att skydda dina Azure-resurser.

-   [Förbättra dina säkra poäng i Azure Security Center](security-center-secure-score.md) – lär dig hur du prioriterar sårbarheter och säkerhets rekommendationer för att förbättra din säkerhets position.

-   [Azure Security Center FAQ](security-center-faq.md) – hitta vanliga frågor om att använda tjänsten.

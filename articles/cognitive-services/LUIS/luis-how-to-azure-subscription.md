---
title: Hantera Azure endpoint prenumeration | Microsoft Docs
description: I den här artikeln kan du skapa en förbrukade endpoint-nyckel för din THOMAS konto för att tillhandahålla obegränsade trafik till slutpunkten efter en betalningsplan.
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: v-geberr
ms.openlocfilehash: 3526871f126ac975f323fe84b14883b361b684ae
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355767"
---
# <a name="manage-azure-endpoint-subscription-keys"></a>Hantera Azure endpoint prenumeration nycklar

För att testa och prototyp endast använda den kostnadsfria nivån (F0). Produktionssystem, använda en [betald](https://aka.ms/luis-price-tier) nivå. 

> [!NOTE]
> Använd inte den [redigering nyckeln](luis-concept-keys.md#authoring-key) för slutpunkten frågor i produktion.

<a name="create-luis-service"></a>
## <a name="create-luis-endpoint-key"></a>Skapa THOMAS slutpunktsnyckel

1. Logga in på  **[Microsoft Azure](https://ms.portal.azure.com/)** 
2. Klickar du på gröna **+** logga in övre vänstra panelen och Sök efter ”THOMAS” i marketplace, och klicka sedan på **språk förstå** och följ de **skapar upplevelse**  att skapa en THOMAS prenumerationskonto. 

    ![Azure Search](./media/luis-azure-subscription/azure-search.png) 

3. Konfigurera prenumerationen med inställningar som bland annat kontonamn priser nivåer, osv. 

    ![Azure API-alternativ](./media/luis-azure-subscription/azure-api-choice.png) 

4. När du skapar THOMAS-tjänsten kan du kan visa snabbtangenterna genereras i **resurshantering -> nycklar**.  

    ![Azure-nycklar](./media/luis-azure-subscription/azure-keys.png)

    > [!Note] 
    > * Logga in på din region [THOMAS](luis-reference-regions.md) webbplats och [lägga till den nya THOMAS endpoint nyckeln](Manage-Keys.md#assign-endpoint-key). 
    > * Du behöver komma ihåg namnet på Azure-tjänsten som du skapade för att välja den på regionen [THOMAS](luis-reference-regions.md) publicera sida.  

## <a name="change-luis-pricing-tier"></a>Ändra THOMAS prisnivå

1.  I [Azure](https://portal.azure.com), hitta THOMAS-prenumerationen. Klicka på THOMAS-prenumeration.
    ![Hitta prenumerationen THOMAS](./media/luis-usage-tiers/find.png)
2.  Klicka på **prisnivå** för att visa tillgängliga prisnivåer. 
    ![Visa prisnivåerna](./media/luis-usage-tiers/subscription.png)
3.  Klicka på prisnivån och på **Välj** att spara ändringen. 
    ![Ändra din betalning THOMAS-nivå](./media/luis-usage-tiers/plans.png)
4.  När prisnivå ändringen verifierar den nya prisnivån ett popup-fönster. 
    ![Kontrollera din betalning THOMAS-nivå](./media/luis-usage-tiers/updated.png)
5. Kom ihåg att [tilldela den här slutpunkten nyckeln](manage-keys.md#assign-endpoint-key) på den **publicera** sidan och använda det i alla endpoint-frågor. 

## <a name="exceed-pricing-tier-usage"></a>Överskrida prisnivå nivå användning
Varje nivå innebär att endpoint begäranden THOMAS-konto med ett specifikt intervall. Om antalet förfrågningar är högre än den tillåtna kontots förbrukade per minut eller per månad begäranden får ett HTTP-fel för ”429: för många begäranden”.

Varje nivå kan ackumulerande förfrågningar per månad. Om förfrågningarna är högre än den tillåtna begäranden får felmeddelandet HTTP för ”403: Ej tillåtet”.  

## <a name="viewing-summary-usage"></a>Visa sammanfattning användning
Du kan visa information om THOMAS användning i Azure. Den **översikt** sidan visar senaste sammanfattningsinformation inklusive anrop och fel. Om du gör en THOMAS slutpunktsförfrågan sedan omedelbart titta på den **översiktssidan**, tillåter upp till fem minuter för användning visas.

![Visa sammanfattning användning](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Anpassa användning diagram
Mått ger en mer detaljerad vy i data.

![Standard-mått](./media/luis-usage-tiers/metrics-default.png)

Du kan konfigurera mått diagram för tidsperioden och måttypen. 

![Anpassade mått](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Totalt antal transaktioner tröskelvärde för varning
Du kan skapa en avisering om du vill veta när du har uppnått ett visst transaktion tröskelvärde, till exempel 10 000 transaktioner. 

![Standard-aviseringar](./media/luis-usage-tiers/alert-default.png)

Lägg till ett mått varning för den **totalt samtal** mått för en viss tidsperiod. Lägg till e-postadresserna för alla personer som ska ta emot aviseringen. Lägg till webhooks för alla system som ska ta emot aviseringen. Du kan också köra en logikapp när en avisering utlöses. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder [versioner](luis-how-to-manage-versions.md) att hantera ändringar i appen THOMAS.
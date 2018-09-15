---
title: Slutpunkten prenumerationshantering i LUIS
titleSuffix: Azure Cognitive Services
description: I den här artikeln skapar du en avgiftsbelagd slutpunktsnyckeln för din LUIS-konto för att tillhandahålla obegränsad trafik till din slutpunkt efter en betalningsplan.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: dcee5ce7ce103db734cf4b63c26944945efed0ea
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630910"
---
# <a name="manage-azure-endpoint-subscription-keys"></a>Hantera Azure-slutpunkt prenumerationsnycklar

Testning och prototyper endast kan du använda den kostnadsfria nivån av (F0). Produktionssystem, använda en [betald](https://aka.ms/luis-price-tier) nivå. 

> [!NOTE]
> Använd inte den [redigering nyckeln](luis-concept-keys.md#authoring-key) för slutpunkten frågor i produktion.

<a name="create-luis-service"></a>
## <a name="create-luis-endpoint-key"></a>Skapa LUIS-slutpunktsnyckel

1. Logga in på  **[Microsoft Azure](https://ms.portal.azure.com/)**. 
2. Klicka på gröna **+** logga i det övre vänstra panelen och Sök efter ”THOMAS” i marketplace, och sedan klicka på **Language Understanding** och följ de **skapa upplevelse**  att skapa en LUIS-prenumerationskonto. 

    ![Azure Search](./media/luis-azure-subscription/azure-search.png) 

3. Konfigurera prenumerationen med inställningar som bland annat kontonamn, priser nivåer, osv. 

    ![Azure API-alternativ](./media/luis-azure-subscription/azure-api-choice.png) 

4. När du har skapat LUIS-tjänsten, du kan visa åtkomstnycklar som genererats i **resurshantering -> nycklar**.  

    ![Azure-nycklar](./media/luis-azure-subscription/azure-keys.png)

    > [!Note] 
    > Logga in på din region [LUIS](luis-reference-regions.md) webbplats och [och tilldela nya LUIS slutpunktsnyckeln](luis-how-to-manage-keys.md#assign-endpoint-key). Du behöver namnet på LUIS-prenumeration från steg 3.

## <a name="change-luis-pricing-tier"></a>Ändra LUIS-prisnivå

1.  I [Azure](https://portal.azure.com), hitta LUIS-prenumerationen. Klicka på LUIS-prenumeration.
    ![Hitta din LUIS-prenumeration](./media/luis-usage-tiers/find.png)
2.  Klicka på **prisnivå** om du vill se alla tillgängliga prisnivåer. 
    ![Visa prisnivåer](./media/luis-usage-tiers/subscription.png)
3.  Klicka på prisnivån och på **Välj** att spara ändringen. 
    ![Ändra LUIS betalning nivå](./media/luis-usage-tiers/plans.png)
4.  När prissättningsändringen är klar, kontrollerar den nya prisnivån i ett popup-fönster. 
    ![Kontrollera din betalning LUIS-nivå](./media/luis-usage-tiers/updated.png)
5. Kom ihåg att [tilldela den här slutpunktsnyckeln](luis-how-to-manage-keys.md#assign-endpoint-key) på den **publicera** sidan och använda det på alla endpoint-frågor. 

## <a name="exceed-pricing-tier-usage"></a>Överskrida prisnivå nivå användning
Varje nivå kan endpoint förfrågningar till ditt Understanding Intelligent Service-konto med ett specifikt intervall. Om antalet begäranden som är högre än den tillåtna av ditt avgiftsbelagda konto per minut eller per månad, begäranden får ett HTTP-fel för ”429: för många förfrågningar”.

Varje nivå kan ackumulerande begäranden per månad. Om förfrågningarna är högre än den tillåtna begäranden får ett HTTP-fel för ”403: tillåts inte”.  

## <a name="viewing-summary-usage"></a>Visa sammanfattning av användning
Du kan visa information om LUIS användning i Azure. Den **översikt** visar senaste sammanfattningsinformation inklusive anrop och fel. Om du gör en LUIS-slutpunktsförfrågan, sedan omedelbart titta på den **översiktssidan**, vänta fem minuter för användningen visas.

![Visa sammanfattning av användning](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Anpassa diagram för användning
Mått ger en mer detaljerad översikt över data.

![Standard-mått](./media/luis-usage-tiers/metrics-default.png)

Du kan konfigurera din måttdiagram för lång tid och Måttyp. 

![Anpassade mått](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Totalt antal transaktioner tröskelvärde för avisering
Om du vill veta när du har nått ett visst transaktion tröskelvärde, till exempel 10 000 transaktioner, kan du skapa en avisering. 

![Standard-aviseringar](./media/luis-usage-tiers/alert-default.png)

Lägg till en måttavisering för den **Totalt antal anrop** mått för en viss tidsperiod. Lägg till e-postadresserna för alla personer som ska ta emot aviseringen. Lägg till webhooks för alla system som ska ta emot aviseringen. Du kan också köra en logikapp när aviseringen utlöses. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder [versioner](luis-how-to-manage-versions.md) att hantera ändringar i LUIS-appen.
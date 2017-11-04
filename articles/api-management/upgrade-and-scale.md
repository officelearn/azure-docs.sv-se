---
title: Uppgradera och skala en instans av Azure API Management | Microsoft Docs
description: "Det här avsnittet beskriver hur du uppgraderar och skala en Azure API Management-instans."
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 22cc917eb6f296724bf535e48b0dd6ba8927e5d3
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2017
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>Uppgradera och skala en instans för API Management 

Kunder kan skala en instans för API Management (APIM) genom att lägga till och ta bort enheter. En **enhet** består av dedikerade Azure-resurser och påverkar en viss belastningen-kapacitet uttryckt i ett antal API-anrop per månad. Faktiska genomflöde och svarstid varierar brett beroende på faktorer som antalet och antalet samtidiga anslutningar, typ och antal konfigurerade principer, begäran och svar storlekar och backend-svarstid. 

Kapacitet och priset för varje enhet beror på en **nivå** i enheten finns. Du kan välja mellan tre nivåer: **Developer**, **Standard**, **Premium**. Om du måste öka kapaciteten för en tjänst i en nivå, bör du lägga till en enhet. Om den nivå som för närvarande är markerad i din instans av APIM inte tillåter att lägga till flera enheter, måste du uppgradera till en högre nivå. 

Priset för varje enhet, möjligheten att lägga till/ta bort enheter, oavsett om du har vissa funktioner (till exempel flera regioner distribution) beror på den nivå som du har valt för din APIM-instans. Den [prisinformationen](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artikel, förklarar vilka pris per enhet och funktioner som du får i varje nivå. 

>[!NOTE]
>Den [prisinformationen](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artikeln visar Ungefärligt antal enhet kapacitet i varje nivå. Om du vill få mer exakta siffror som du behöver titta på en realistisk scenario för dina API: er. Se avsnittet ”så att planera kapaciteten” som följer.

## <a name="prerequisites"></a>Krav

Om du vill utföra stegen som beskrivs i den här artikeln, måste du ha:

+ En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ En APIM-instans. Mer information finns i [skapa en instans av Azure API Management](get-started-create-service-instance.md).

## <a name="how-to-plan-for-capacity"></a>Hur du planerar för kapacitet?

Om du vill ta reda på om du har tillräckligt med enheter att hantera trafik på Testa på arbetsbelastningar som du förväntar dig. 

Som nämnts ovan, antal begäranden per andra APIM kan processen beror på många faktorer. Till exempel anslutning mönstret, storleken på begäran och svar, principer som är konfigurerade på varje API, antalet klienter som skickar begäranden.

Använd **mått** (använder Azure-Monitor funktioner) att förstå hur mycket kapacitet används vid en given tidpunkt.

### <a name="use-the-azure-portal-to-examine-metrics"></a>Använda Azure portal för att undersöka mätvärden 

1. Navigera till din APIM instans i den [Azure-portalen](https://portal.azure.com/).
2. Välj **mått**.
3. Välj **kapacitet** mått från **tillgängliga mått**. 

    Måttet kapacitet ger dig en uppfattning om hur mycket kapacitet som används i din klient. Du kan testa genom att lägga till mer och mer att läsa in och se vad som är Välj-belastningen. Du kan ange en mått avisering så att du vet när något som inte förväntas sker. Till exempel din APIM instans ansluter sig kapacitet för över 5 minuter. 

    >[!TIP]
    > Du kan konfigurera aviseringen för att meddela dig när din tjänst slut på kapacitet eller se det som anropar en logikapp skalar automatiskt genom att lägga till en enhet...

## <a name="upgrade-and-scale"></a>Uppgradering och skala 

Som tidigare nämnts kan du välja mellan tre nivåer: **Developer**, **Standard**, **Premium**. Den **Developer** nivå som ska användas för att utvärdera tjänsten; ska inte användas för produktion. Den **Developer** nivå har inte SLA och du kan skala det här skiktet (Lägg till/ta bort enheter). 

**Standard** och **Premium** produktion nivåer har SLA och kan skalas. Den **Standard** skiktet kan skalas upp till fyra enheter. Du kan lägga till valfritt antal enheter på den **Premium** nivå. 

Den **Premium** nivån kan du distribuera en enda instans för API management i valfritt antal önskade Azure-regioner. När du först skapar en API Management-tjänsten för instansen innehåller bara en enhet och finns i en enda Azure-region. Den inledande regionen utses den **primära** region. Ytterligare regioner kan enkelt lägga. När du lägger till ett område, kan du ange hur många enheter som du vill allokera. Du kan till exempel har en enhet den **primära** region och fem enheter i en annan region. Du kan anpassa trafiken som du har i varje region. Mer information finns i [distribuera ett Azure API Management service-instans till Azure-regioner](api-management-howto-deploy-multi-region.md).

Du kan uppgradera och nedgradera till och från alla skikt. Observera att nedgradera kan ta bort vissa funktioner, till exempel Vnet eller flera regioner distributionen när Nedgradera till Standard från Premium-nivån.

>[!NOTE]
>Uppgradering eller skala processen kan ta 15 till 30 minuter att tillämpa. Du får meddelanden när du är klar.

### <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Använda Azure portal för att uppgradera och skala

1. Navigera till din APIM instans i den [Azure-portalen](https://portal.azure.com/).
2. Välj **skala och prissättning**.
3. Välj önskad nivå.
4. Ange antalet **enheter** du vill lägga till. Du kan använda skjutreglaget, eller så kan du ange hur många enheter.<br/>
    Om du väljer den **Premium** nivån, måste du först välja en region.
5. Tryck på **spara**

## <a name="next-steps"></a>Nästa steg

[Distribuera ett Azure API Management service-instans till Azure-regioner](api-management-howto-deploy-multi-region.md)


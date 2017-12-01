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
ms.openlocfilehash: e92c1a44b49c64308438184ab8185a90766c5bcf
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>Uppgradera och skala en instans för API Management 

Kunder kan skala en instans för API Management (APIM) genom att lägga till och ta bort enheter. En **enhet** består av dedikerade Azure-resurser och påverkar en viss belastningen-kapacitet uttryckt i ett antal API-anrop per månad. Det här numret representerar inte en gräns för anrop, men i stället maximalt dataflöde värdet för grov kapacitetsplanering. Faktiska genomflöde och svarstid varierar brett beroende på faktorer som antalet och antalet samtidiga anslutningar, typ och antal konfigurerade principer, förfrågan och svar storlekar och serverdelen svarstid.

Kapacitet och priset för varje enhet beror på den **nivå** i enheten finns. Du kan välja mellan tre nivåer: **Developer**, **Standard**, **Premium**. Om du måste öka kapaciteten för en tjänst i en nivå, bör du lägga till en enhet. Om den nivå som för närvarande är markerad i din instans av APIM inte tillåter att lägga till flera enheter, måste du uppgradera till en högre nivå. 

Priset på varje enhet och tillgängliga funktioner (till exempel flera regioner distribution) är beroende av den nivå som du har valt för din APIM-instans. Den [prisinformationen](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artikel, förklarar pris per enhet och funktioner som du får i varje nivå. 

>[!NOTE]
>Den [prisinformationen](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artikeln visar Ungefärligt antal enhet kapacitet i varje nivå. Om du vill få mer exakta siffror som du behöver titta på en realistisk scenario för dina API: er. Se avsnittet ”så att planera kapaciteten” som följer.

## <a name="prerequisites"></a>Krav

Om du vill utföra stegen som beskrivs i den här artikeln, måste du ha:

+ En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ En APIM-instans. Mer information finns i [skapa en instans av Azure API Management](get-started-create-service-instance.md).

## <a name="how-to-plan-for-capacity"></a>Hur du planerar för kapacitet?

Om du vill ta reda på om du har tillräckligt med enheter att hantera trafik på Testa på arbetsbelastningar som du förväntar dig. 

Som nämnts ovan är beror antalet begäranden per sekund som kan bearbeta en APIM enhet på många faktorer. Till exempel anslutning mönstret, storleken på begäran och svar, principer som är konfigurerade på varje API, antalet klienter som skickar begäranden.

Använd **mått** (använder Azure-Monitor funktioner) att förstå hur mycket kapacitet används vid en given tidpunkt.

### <a name="use-the-azure-portal-to-examine-metrics"></a>Använda Azure portal för att undersöka mätvärden 

1. Navigera till din APIM instans i den [Azure-portalen](https://portal.azure.com/).
2. Välj **mått**.
3. Välj **kapacitet** mått från **tillgängliga mått**. 

    Kapacitet måttet ger dig en uppfattning om hur mycket av det tillgängliga beräkningskapacitet som används i din klient. Dess värde är härledd från de beräkningsresurser som används av din klient, till exempel minne, CPU och kölängder i nätverket. Det är inte ett direkt mått av antalet begäranden som bearbetas. Du kan testa genom att öka belastningen på din klient och övervaka vilka värdet kapacitet mått som motsvarar din belastning. Du kan ange en mått avisering så att du vet när något oväntat händer. Till exempel har APIM-instansen överskridit sin förväntad maximal kapacitet i över 10 minuter.

    >[!TIP]
    > Du kan konfigurera aviseringar så att du vet när tjänsten har brist på kapacitet eller som anropar en logikapp som skalar automatiskt genom att lägga till en enhet.

## <a name="upgrade-and-scale"></a>Uppgradering och skala 

Som tidigare nämnts kan du välja mellan tre nivåer: **Developer**, **Standard**, och **Premium**. Den **Developer** nivå som ska användas för att utvärdera tjänsten; ska inte användas för produktion. Den **Developer** nivå har inte SLA och du kan skala det här skiktet (Lägg till/ta bort enheter). 

**Standard** och **Premium** är produktion nivåer som har SLA och kan skalas. Den **Standard** skiktet kan skalas upp till fyra enheter. Du kan lägga till valfritt antal enheter på den **Premium** nivå. 

Den **Premium** nivån kan du distribuera en enda instans för API management i valfritt antal önskade Azure-regioner. När du först skapar en API Management-tjänsten för instansen innehåller bara en enhet och finns i en enda Azure-region. Den inledande regionen utses den **primära** region. Ytterligare regioner kan enkelt lägga. När du lägger till ett område, kan du ange hur många enheter som du vill allokera. Du kan till exempel har en enhet den **primära** region och fem enheter i en annan region. Du kan anpassa antalet enheter på trafik som du har i varje region. Mer information finns i [distribuera ett Azure API Management service-instans till Azure-regioner](api-management-howto-deploy-multi-region.md).

Du kan uppgradera och nedgradera till och från alla skikt. Observera att uppgradera eller nedgradera kan ta bort vissa funktioner – till exempel Vnet eller flera regioner distributionen när Nedgradera till Standard från Premium-nivån.

>[!NOTE]
>Uppgradering eller skala processen kan ta 15 till 45 minuter att tillämpa. Du får meddelanden när du är klar.

### <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Använda Azure portal för att uppgradera och skala

1. Navigera till din APIM instans i den [Azure-portalen](https://portal.azure.com/).
2. Välj **skala och prissättning**.
3. Välj önskad nivå.
4. Ange antalet **enheter** du vill lägga till. Du kan använda skjutreglaget, eller så kan du ange hur många enheter.<br/>
    Om du väljer den **Premium** nivån, måste du först välja en region.
5. Tryck på **spara**

## <a name="next-steps"></a>Nästa steg

[Distribuera ett Azure API Management service-instans till Azure-regioner](api-management-howto-deploy-multi-region.md)


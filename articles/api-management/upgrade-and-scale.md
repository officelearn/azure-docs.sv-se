---
title: Uppgradera och skala en instans av Azure API Management | Microsoft Docs
description: Det här avsnittet beskriver hur du uppgraderar och skala en Azure API Management-instans.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.openlocfilehash: ca32c72b1582b2a09f9f1754ad778cf1b682a1c2
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293320"
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>Uppgradera och skala en instans för API Management  

Kunder kan skala en instans för API Management (APIM) genom att lägga till och ta bort enheter. En **enhet** består av dedikerade Azure-resurser och påverkar en viss belastningen-kapacitet uttryckt i ett antal API-anrop per månad. Det här numret representerar inte en gräns för anrop, men i stället maximalt dataflöde värdet för grov kapacitetsplanering. Faktiska genomflöde och svarstid varierar brett beroende på faktorer som antalet och antalet samtidiga anslutningar, typ och antal konfigurerade principer, förfrågan och svar storlekar och serverdelen svarstid.

Kapacitet och priset för varje enhet beror på den **nivå** i enheten finns. Du kan välja mellan fyra nivåer: **Developer**, **grundläggande**, **Standard**, **Premium**. Om du måste öka kapaciteten för en tjänst i en nivå, bör du lägga till en enhet. Om den nivå som för närvarande är markerad i din instans av APIM inte tillåter att lägga till flera enheter, måste du uppgradera till en högre nivå.

Priset på varje enhet och tillgängliga funktioner (till exempel flera regioner distribution) är beroende av den nivå som du har valt för din APIM-instans. Den [prisinformationen](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artikel, förklarar pris per enhet och funktioner som du får i varje nivå. 

>[!NOTE]
>Den [prisinformationen](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artikeln visar Ungefärligt antal enhet kapacitet i varje nivå. Om du vill få mer exakta siffror som du behöver titta på en realistisk scenario för dina API: er. Finns det [kapacitet för en instans av Azure API Management](api-management-capacity.md) artikel.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa stegen från den här artikeln, måste du:

+ Ha en aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Har en APIM-instans. Mer information finns i [skapa en instans av Azure API Management](get-started-create-service-instance.md).

+ Förstå begreppet [kapacitet för en Azure API Management-instans] (api-hantering-capacity.md).

## <a name="upgrade-and-scale"></a>Uppgradera och skala  

Du kan välja mellan fyra nivåer: **Developer**, **grundläggande**, **Standard** och **Premium**. Den **Developer** nivå som ska användas för att utvärdera tjänsten; ska inte användas för produktion. Den **Developer** nivå har inte SLA och du kan skala det här skiktet (Lägg till/ta bort enheter). 

**Grundläggande**, **Standard** och **Premium** är produktion nivåer som har SLA och kan skalas. Den **grundläggande** nivå är billigaste nivån som har SLA och skalade upp till 2-enheter kan vara **Standard** skiktet kan skalas upp till fyra enheter. Du kan lägga till valfritt antal enheter på den **Premium** nivå.

Den **Premium** nivån kan du distribuera en enda instans för API management i valfritt antal önskade Azure-regioner. När du först skapar en API Management-tjänsten för instansen innehåller bara en enhet och finns i en enda Azure-region. Den inledande regionen utses den **primära** region. Ytterligare regioner kan enkelt lägga. När du lägger till ett område, kan du ange hur många enheter som du vill allokera. Du kan till exempel har en enhet den **primära** region och fem enheter i en annan region. Du kan anpassa antalet enheter på trafik som du har i varje region. Mer information finns i [distribuera ett Azure API Management service-instans till Azure-regioner](api-management-howto-deploy-multi-region.md).

Du kan uppgradera och nedgradera till och från alla skikt. Observera att uppgradera eller nedgradera kan ta bort vissa funktioner – till exempel Vnet eller flera regioner distributionen när Nedgradera till Standard eller grundläggande från Premium-nivån.

>[!NOTE]
>Uppgradering eller skala processen kan ta 15 till 45 minuter att tillämpa. Du får meddelanden när du är klar.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Använda Azure portal för att uppgradera och skala

![Skala APIM i Azure-portalen](./media/upgrade-and-scale/portal-scale.png)

1. Navigera till din APIM instans i den [Azure-portalen](https://portal.azure.com/).
2. Välj **skala och prissättning** på menyn.
3. Välj önskad nivå.
4. Ange antalet **enheter** du vill lägga till. Du kan använda skjutreglaget, eller så kan du ange hur många enheter.  
    Om du väljer den **Premium** nivån, måste du först välja en region.
5. Tryck på **spara**

## <a name="next-steps"></a>Nästa steg

[Distribuera ett Azure API Management service-instans till Azure-regioner](api-management-howto-deploy-multi-region.md)
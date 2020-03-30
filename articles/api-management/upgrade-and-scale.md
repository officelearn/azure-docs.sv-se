---
title: Uppgradera och skala en Azure API Management-instans | Microsoft-dokument
description: I det här avsnittet beskrivs hur du uppgraderar och skalar en Azure API Management-instans.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2018
ms.author: apimpm
ms.openlocfilehash: 64649c86dbd3c3469247308bfc4dd0ed12e06949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70018243"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Uppgradera och skala en Azure API Management-instans  

Kunder kan skala en APIM-instans (Azure API Management) genom att lägga till och ta bort enheter. En **enhet** består av dedikerade Azure-resurser och har en viss bärande kapacitet uttryckt som ett antal API-anrop per månad. Det här numret representerar inte en samtalsgräns, utan snarare ett maximalt dataflödesvärde för grov kapacitetsplanering. Det faktiska dataflödet och svarstiden varierar i stort sett beroende på faktorer som antal och hastighet för samtidiga anslutningar, typ och antal konfigurerade principer, begäran och svarsstorlekar samt svarstid för serverda.

Kapacitet och pris för varje enhet beror på i **vilken nivå** enheten finns. Du kan välja mellan fyra nivåer: **Utvecklare**, **Basic**, **Standard**, **Premium**. Om du behöver öka kapaciteten för en tjänst inom en nivå bör du lägga till en enhet. Om den nivå som för närvarande är markerad i APIM-instansen inte tillåter att du lägger till fler enheter måste du uppgradera till en högre nivå.

Priset för varje enhet och tillgängliga funktioner (till exempel distribution med flera regioner) beror på vilken nivå du valde för APIM-instansen. Prisinformationsartikeln förklarar priset per enhet och funktioner du får på varje nivå. [pricing details](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 

>[!NOTE]
>Prisinformationsartikeln visar ungefärliga antal enhetskapacitet på varje nivå. [pricing details](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) För att få mer exakta siffror måste du titta på ett realistiskt scenario för dina API:er. Se kapaciteten för [en Azure API Management-instansartikel.](api-management-capacity.md)

## <a name="prerequisites"></a>Krav

Om du vill följa stegen i den här artikeln måste du:

+ Har en aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Ha en APIM-instans. Mer information finns i [Skapa en Azure API Management-instans](get-started-create-service-instance.md).

+ Förstå begreppet [kapacitet för en Azure API Management-instans](api-management-capacity.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Uppgradera och skala  

Du kan välja mellan fyra nivåer: **Utvecklare,** **Basic**, **Standard** och **Premium**. **Utvecklarnivån** ska användas för att utvärdera tjänsten. Det bör inte användas för produktion. **Utvecklarnivån** har inte SLA och du kan inte skala den här nivån (lägg till/ta bort enheter). 

**Basic,** **Standard** och **Premium** är produktionsnivåer som har SLA och kan skalas. **Basic-nivån** är den billigaste nivån som har SLA och den kan skalas upp till 2 enheter, **standardnivån** kan skalas till upp till fyra enheter. Du kan lägga till valfritt **Premium** antal enheter på Premium-nivån.

**Premium-nivån** kan du distribuera en enda Azure API Management-instans över valfritt antal önskade Azure-regioner. När du först skapar en Azure API Management-tjänst innehåller instansen bara en enhet och finns i en enda Azure-region. Den ursprungliga regionen betecknas som den **primära** regionen. Ytterligare regioner kan enkelt läggas till. När du lägger till en region anger du hur många enheter du vill allokera. Du kan till exempel ha en enhet i den **primära** regionen och fem enheter i någon annan region. Du kan anpassa antalet enheter till den trafik du har i varje region. Mer information finns i Så här distribuerar du [en Azure API Management-tjänstinstans till flera Azure-regioner](api-management-howto-deploy-multi-region.md).

Du kan uppgradera och nedgradera till och från valfri nivå. Observera att uppgradering eller nedgradering kan ta bort vissa funktioner, till exempel VNETs eller distribution med flera regioner, när du nedgraderar till Standard eller Basic från Premium-nivån.

> [!NOTE]
> Uppgraderings- eller skalningsprocessen kan ta från 15 till 45 minuter att tillämpa. Du får ett meddelande när det är klart.

> [!NOTE]
> API Management-tjänsten i **förbrukningsnivån** skalar automatiskt baserat på trafiken.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Använda Azure-portalen för att uppgradera och skala

![Skala APIM i Azure-portalen](./media/upgrade-and-scale/portal-scale.png)

1. Navigera till APIM-instansen i [Azure-portalen](https://portal.azure.com/).
2. Välj **Skala och pris** på menyn.
3. Välj önskad nivå.
4. Ange hur många **enheter** du vill lägga till. Du kan antingen använda skjutreglaget eller skriva antalet enheter.  
    Om du väljer **Premium-nivån** måste du först välja en region.
5. Tryck på **Spara**.

## <a name="downtime-during-scaling-up-and-down"></a>Stilleståndstid under upp- och nedst nedning
Om du skalar från eller till utvecklarnivån kommer det att finnas driftstopp. Annars finns det inga driftstopp. 


## <a name="next-steps"></a>Nästa steg

- [Så distribuerar du en Azure API Management-tjänstinstans till flera Azure-regioner](api-management-howto-deploy-multi-region.md)
- [Så här skalar du automatiskt en Azure API Management-tjänstinstans](api-management-howto-autoscale.md)

---
title: Uppgradera och skala en Azure API Management-instans | Microsoft Docs
description: Det här avsnittet beskriver hur du uppgraderar och skalar en Azure API Management-instans.
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
ms.openlocfilehash: 3f92eac79e9524aa6ded9f898d582ce46c99bf97
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363300"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Uppgradera och skala en Azure API Management-instans  

Kunder kan skala en Azure API Management (APIM)-instans genom att lägga till eller ta bort enheter. En **enhet** består av dedikerade Azure-resurser och har vissa load-betydelse kapacitet uttrycks som ett antal API-anrop per månad. Det här talet motsvarar inte en gräns för anrop, men i stället ett maximalt dataflöde värde för ungefärlig kapacitetsplanering. Faktiska dataflödet och svarstiderna för varierar brett beroende på faktorer som frekvensen och antalet samtidiga anslutningar, typ och antal konfigurerade principer, begäranden och svar storlekar och serverdelslatens.

Kapacitet och priset för varje enhet beror på den **nivå** i som enheten existerar. Du kan välja mellan fyra nivåer: **Developer**, **grundläggande**, **Standard**, **Premium**. Om du vill öka kapaciteten för en tjänst inom en nivå, bör du lägga till en enhet. Om den nivå som för närvarande är markerad i APIM-instansen inte tillåter att lägga till fler enheter, måste du uppgradera till en högre nivå.

Priset för varje enhet och tillgängliga funktioner (till exempel distribution i flera regioner) är beroende av den nivå som du valde för APIM-instansen. Den [prisinformation](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artikel, förklarar pris per enhet och funktioner som du får i varje nivå. 

>[!NOTE]
>Den [prisinformation](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artikeln visar Ungefärligt antal enhet kapacitet i varje nivå. Om du vill ha mer exakta siffror som du behöver titta på en realistiskt scenario för dina API: er. Se den [kapacitet för en Azure API Management-instans](api-management-capacity.md) artikeln.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa stegen från den här artikeln, måste du:

+ Ha en aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Ha en APIM-instansen. Mer information finns i [skapa en Azure API Management-instans](get-started-create-service-instance.md).

+ Förstå begreppet [kapaciteten för en Azure API Management-instans] (api-hantering-capacity.md).

## <a name="upgrade-and-scale"></a>Uppgradera och skala  

Du kan välja mellan fyra nivåer: **Developer**, **grundläggande**, **Standard** och **Premium**. Den **Developer** nivå som ska användas för att utvärdera tjänsten; ska inte användas för produktion. Den **Developer** nivån saknar serviceavtal och du inte skala den här nivån (Lägg till/ta bort enheter). 

**Grundläggande**, **Standard** och **Premium** är produktion nivåer som har SLA och kan skalas. Den **grundläggande** nivån är den billigaste nivån som har SLA och det kan vara skalats upp till 2 enheter **Standard** nivå kan skalas till upp till fyra enheter. Du kan lägga till valfritt antal enheter för att den **Premium** nivå.

Den **Premium** nivå kan du distribuera en enda Azure API Management-instans över valfritt antal önskade Azure-regioner. När du först skapa en Azure API Management-tjänsten instansen innehåller endast en enhet och finns i en enda Azure-region. Den första regionen är utsedd till den **primära** region. Enkelt du kan lägga ytterligare regioner. När du lägger till en region, kan du ange hur många enheter som ska tilldelas. Du kan till exempel ha en enhet den **primära** region och fem enheter i några andra regioner. Du kan anpassa antalet enheter på trafik som du har i varje region. Mer information finns i [hur du distribuerar en Azure API Management-tjänstinstans till flera Azure-regioner](api-management-howto-deploy-multi-region.md).

Du kan uppgradera och nedgradera till och från valfri nivå. Observera att uppgradera eller nedgradera kan ta bort vissa funktioner – till exempel virtuella nätverk eller distribution i flera regioner, när du nedgraderar till Standard eller Basic från Premium-nivån.

>[!NOTE]
>Uppgradering eller skala processen kan ta mellan 15 och 45 minuter innan tillämpas. Du får meddelanden när det är klart.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Använd Azure-portalen för att uppgradera och skala

![Skala APIM i Azure-portalen](./media/upgrade-and-scale/portal-scale.png)

1. Navigera till APIM-instansen i den [Azure-portalen](https://portal.azure.com/).
2. Välj **skalning och priser** på menyn.
3. Välj önskad nivå.
4. Ange antalet **enheter** du vill lägga till. Du kan använda skjutreglaget, eller så kan du ange hur många enheter.  
    Om du väljer den **Premium** nivå, måste du först välja en region.
5. Tryck på **Spara**.

## <a name="next-steps"></a>Nästa steg

- [Hur du distribuerar en Azure API Management-tjänstinstans till flera Azure-regioner](api-management-howto-deploy-multi-region.md)
- [Hur du automatiskt skalar en Azure API Management-tjänstinstans](api-management-howto-autoscale.md)

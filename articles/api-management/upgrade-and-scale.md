---
title: Uppgradera och skala en Azure API Management-instans | Microsoft Docs
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
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018243"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Uppgradera och skala en Azure API Management-instans  

Kunder kan skala en Azure API Management-instans (APIM) genom att lägga till och ta bort enheter. En **enhet** består av dedikerade Azure-resurser och har en viss belastnings bär ande kapacitet uttryckt som ett antal API-anrop per månad. Det här talet representerar inte någon anrops gräns, utan i stället ett maximalt data flödes värde för att tillåta grov kapacitets planering. Det faktiska data flödet och svars tiden varierar i stort sett beroende på faktorer som antalet samtidiga anslutningar, typ och antal konfigurerade principer, begär Anden och svars tider samt Server dels svars tid.

Kapaciteten och priset för varje enhet beror på på vilken **nivå** enheten finns. Du kan välja mellan fyra nivåer: **Developer**, **Basic**, **standard**, **Premium**. Om du behöver öka kapaciteten för en tjänst inom en nivå bör du lägga till en enhet. Om den valda nivån i APIM-instansen inte tillåter att fler enheter läggs till, måste du uppgradera till en nivå på högre nivå.

Priset för varje enhet och de tillgängliga funktionerna (till exempel distribution i flera regioner) beror på vilken nivå du väljer för din APIM-instans. I artikeln [pris information](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) beskrivs priset per enhet och de funktioner som du får på varje nivå. 

>[!NOTE]
>I artikeln [pris information](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) visas ungefärligt antal enhets kapaciteter på varje nivå. För att få mer exakta siffror måste du titta på ett realistiskt scenario för dina API: er. Se [kapaciteten för en artikel i Azure API Management](api-management-capacity.md) -instansen.

## <a name="prerequisites"></a>Förutsättningar

För att följa stegen i den här artikeln måste du:

+ Ha en aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Ha en APIM-instans. Mer information finns i [skapa en Azure API Management-instans](get-started-create-service-instance.md).

+ Förstå konceptet med [kapacitet för en Azure API Management-instans](api-management-capacity.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Uppgradera och skala  

Du kan välja mellan fyra nivåer: **Developer**, **Basic**, **standard** och **Premium**. **Developer** -nivån ska användas för att utvärdera tjänsten. den bör inte användas för produktion. **Developer** -nivån har inget SLA och du kan inte skala den här nivån (Lägg till/ta bort enheter). 

**Basic**, **standard** och **Premium** är produktions nivåer som har service avtal och kan skalas. **Basic** -nivån är billigaste-nivån som har service avtal och kan skalas upp till två enheter. **standard** nivån kan skalas upp till fyra enheter. Du kan lägga till valfritt antal enheter på **Premium** -nivån.

På **Premium** -nivån kan du distribuera en enda Azure API Management-instans över valfritt antal Azure-regioner. När du skapar en Azure API Management-tjänst första gången innehåller instansen bara en enhet och finns i en enda Azure-region. Den första regionen anges som den **primära** regionen. Du kan enkelt lägga till ytterligare regioner. När du lägger till en region anger du antalet enheter som du vill tilldela. Du kan till exempel ha en enhet i den **primära** regionen och fem enheter i en annan region. Du kan skräddarsy antalet enheter till den trafik du har i varje region. Mer information finns i [distribuera en Azure API Management-tjänstinstans till flera Azure-regioner](api-management-howto-deploy-multi-region.md).

Du kan uppgradera och nedgradera till och från valfri nivå. Observera att uppgradering eller nedgradering kan ta bort vissa funktioner, till exempel virtuella nätverk eller distribution i flera regioner, vid nedgradering till standard eller Basic från Premium-nivån.

> [!NOTE]
> Uppgradering eller skala processen kan ta mellan 15 och 45 minuter innan tillämpas. Du får ett meddelande när det är färdigt.

> [!NOTE]
> API Management tjänst i **förbruknings** nivån skalas automatiskt baserat på trafiken.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Använd Azure Portal för att uppgradera och skala

![Skala APIM i Azure Portal](./media/upgrade-and-scale/portal-scale.png)

1. Navigera till din APIM-instans i [Azure Portal](https://portal.azure.com/).
2. Välj **skala och priser** på menyn.
3. Välj önskad nivå.
4. Ange antalet **enheter** som du vill lägga till. Du kan antingen använda skjutreglaget eller ange antalet enheter.  
    Om du väljer **Premium** nivån måste du först välja en region.
5. Tryck på **Spara**.

## <a name="downtime-during-scaling-up-and-down"></a>Drift stopp vid skalning upp och ned
Om du skalar från eller till Developer-nivån kommer det att vara stillestånds tid. Annars finns det ingen stillestånds tid. 


## <a name="next-steps"></a>Nästa steg

- [Så distribuerar du en Azure API Management-tjänstinstans till flera Azure-regioner](api-management-howto-deploy-multi-region.md)
- [Skala en Azure API Management-tjänstinstans automatiskt](api-management-howto-autoscale.md)

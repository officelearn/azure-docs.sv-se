---
title: Azure Functions Premium-prenumeration (förhandsversion) | Microsoft Docs
description: Information och konfigurationsalternativ (VNet, Nej kallstart, obegränsade körningstid) för Azure Functions-Premium plan.
services: functions
author: jeffhollan
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: jehollan
ms.openlocfilehash: d327146c4a1fa61e55bb904308038c1ce717123d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031248"
---
# <a name="azure-functions-premium-plan-preview"></a>Azure Functions Premium-prenumeration (förhandsversion)

Azure Functions Premium-planen är ett värdalternativ för funktionsappar. Premiumprenumerationen innehåller funktioner som VNet-anslutning, inga kallstart och premium-maskinvara.  Flera funktionsappar kan distribueras till samma premiumplan och planen kan du konfigurera beräkning instansstorlek, Basplan storlek och maximalt plan storlek.  En jämförelse av Premium-prenumerationen och andra plan och som är värd för typer i [skala och värdalternativ](functions-scale.md).

> [!NOTE]
> Förhandsversionen av Premium-plan stöder för närvarande funktioner som körs i .NET, noden eller Java via Windows-infrastrukturen.

## <a name="create-a-premium-plan"></a>Skapa en premiumplan

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Du kan också skapa en premiumplan från Azure CLI

```azurecli-interactive
az functionapp plan create -g <resource-group> -n <plan-name> -l <region> --number-of-workers 1 --sku EP1
```

## <a name="features"></a>Funktioner

Följande funktioner är tillgängliga för funktionsappar som distribueras till en premiumplan.

### <a name="pre-warmed-instances"></a>Före uppvärmning instanser

Om inga händelser och körningar genomförs idag i förbrukningsplanen kan din app skala till noll instanser. När nya händelser finns, måste en ny instans anpassas med din app som körs på den.  Specialiserar sig nya instanser kan ta lite tid beroende på vilken app.  Den här ytterligare fördröjning vid första anropet kallas ofta appen startar.

Du kan ha en app i förväg värmas på ett angivet antal instanser, upp till storleken på din minsta plan i Premium-plan.  Före uppvärmning instanser kan du skala före en app innan hög belastning. När appen skalas ut, skalas den först i förväg uppvärmning instanser. Ytterligare instanser fortsätta att buffra ut och varma omedelbart som förberedelse inför nästa skalningsåtgärden. Genom att ha en buffert med förväg uppvärmning instanser undviker effektivt kallstart svarstider.  Före uppvärmning instanser är en funktion i Premium-plan och du behöver minst en instans som körs och tillgänglig hela tiden planen för är aktiv.

Du kan konfigurera antalet förväg uppvärmning instanser i Azure portal genom att välja **skala ut** i den **plattformsfunktioner** fliken.

![Inställningar för elastisk skalning](./media/functions-premium-plan/scale-out.png)

Du kan också konfigurera förväg uppvärmning instanser för en app med Azure CLI

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Privata nätverksanslutningar

Azure Functions som distribuerats till en premiumplan drar nytta av [nya VNet-integrering för web apps](../app-service/web-sites-integrate-with-vnet.md#new-vnet-integration).  När du konfigurerade din app kan kommunicera med resurser i ditt virtuella nätverk eller skyddad via tjänstslutpunkter.  IP-begränsningar finns även på appen för att begränsa inkommande trafik.

När du tilldelar ett undernät till funktionsappen i en premiumplan, behöver du ett undernät med tillräckligt med IP-adresser för varje potentiell instans. Även om det maximala antalet instanser kan variera under förhandsversionen, kräver vi ett IP-Adressblock med minst 100 tillgängliga adresser.

Mer information finns i [integrera din funktionsapp med ett virtuellt nätverk](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Snabb elastisk skalning

Ytterligare instanser läggs automatiskt för din app med samma snabb skalning logik som förbrukningsplanen.  Mer information om hur skalning fungerar finns i [fungera skalning och värdtjänster](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="unbounded-run-duration"></a>Ramavgränsare körningens varaktighet

Azure Functions i en användningsplan är begränsad till 10 minuter för körning av en enda.  I Premium-planen, varaktighet för körning som standard 30 minuter att förhindra skenande körningar. Du kan dock [modifiera host.json](./functions-host-json.md#functiontimeout) att göra det här obundna för Premium-plan-appar.

I förhandsversion, varaktigheten garanteras inte de senaste 12 minuter och har bästa möjliga chans som körs efter 30 minuter om din app inte skalas utöver minsta worker antalet.

## <a name="plan-and-sku-settings"></a>Planerings- och SKU-inställningar

När du skapar planen kan du konfigurera två inställningar: det minsta antalet instanser (eller plan storlek) och den största burst-gränsen.  Minsta instanser för en premiumplan 1 och högsta burst under förhandsversionen är 20.  Minsta instanser är reserverad och alltid körs.

> [!IMPORTANT]
> Du debiteras för varje instans som tilldelats i det lägsta instansantalet oavsett om funktioner som körs eller inte.

Om din app kräver instanser efter storleken på din plan, kan den fortsätta att skala ut tills antalet instanser når maximal burst-gränsen.  Du debiteras för instanser efter storleken på din plan endast när de är aktiv och hyrd till dig.  Vi gör en yttersta för att skala upp appen ut till dess definierade maxgränsen medan de lägsta planinstanser garanterat för din app.

Du kan konfigurera plan storlek och maximala storlekar i Azure-portalen som valts i **skala ut** alternativ i planen eller en funktionsapp som distribuerats till den här planen (under **plattformsfunktioner**).

Du kan också öka gränsen för högsta burst från Azure CLI:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Tillgänglig instans SKU: er

När du skapar våra skala din plan kan välja du mellan tre instansstorlekar.  Du kommer att debiteras för det totala antalet kärnor och minne som förbrukas per sekund.  Din app kan automatiskt skala ut till flera instanser efter behov.  

|SKU|Kärnor|Minne|Storage|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7GB|250 GB|
|EP3|4|14GB|250 GB|

## <a name="regions"></a>Regioner

Nedan visas regionerna som stöds för tillfället för den offentliga förhandsversionen.

|Region|
|--|
|Östra Australien|
|Sydöstra Australien|
|Centrala Kanada|
|Centrala USA|
|Östasien|
|USA, östra 2|
|Frankrike, centrala|
|Västra Japan|
|Sydkorea, centrala|
|Norra Europa|
|Södra centrala USA|
|Södra Indien|
|Sydostasien|
|Storbritannien, västra|
|Västra Europa|
|Indien, västra|
|Västra USA|

## <a name="known-issues"></a>Kända problem

Du kan spåra statusen för kända problem för den [offentlig förhandsversion på GitHub](https://github.com/Azure/Azure-Functions/wiki/Premium-plan-known-issues).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förstå Azure Functions skalar och värdalternativ](functions-scale.md)

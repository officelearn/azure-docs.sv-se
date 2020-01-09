---
title: Azure Functions Premium-prenumerationsavtal
description: Information och konfigurations alternativ (VNet, ingen kall start, obegränsad körnings tid) för Azure Functions Premium-planen.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: 5f6825243b7e410b49b54d04a028b5d71610ea68
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561962"
---
# <a name="azure-functions-premium-plan"></a>Azure Functions Premium-prenumerationsavtal

Azure Functions Premium-planen (kallas ibland elastisk Premium-plan) är ett värd alternativ för Function-appar. Premium-prenumerationen innehåller funktioner som VNet-anslutning, ingen kall start och förstklassig maskin vara.  Flera Function-appar kan distribueras till samma Premium plan och med planen kan du konfigurera storlek på beräknings instanser, bas Plans storlek och maximal schema storlek.  En jämförelse av Premium-planen och andra plan-och värd typer finns i [funktions skala och värd alternativ](functions-scale.md).

## <a name="create-a-premium-plan"></a>Skapa en Premium-plan

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Du kan också skapa en Premium-plan med hjälp av [AZ functionapp plan Create](/cli/azure/functionapp/plan#az-functionapp-plan-create) i Azure CLI. I följande exempel skapas en nivå plan för _elastisk Premium 1_ :

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

I det här exemplet ersätter du `<RESOURCE_GROUP>` med din resurs grupp och `<PLAN_NAME>` med ett namn för din plan som är unik i resurs gruppen. Ange ett [`<REGION>`som stöds ](#regions). Om du vill skapa en Premium-plan som stöder Linux inkluderar du alternativet `--is-linux`.

När planen har skapats kan du använda [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) för att skapa din Function-app. I portalen skapas både planen och appen på samma tidpunkt. Ett exempel på ett fullständigt Azure CLI-skript finns i [skapa en Function-app i en Premium-plan](scripts/functions-cli-create-premium-plan.md).

## <a name="features"></a>Funktioner

Följande funktioner är tillgängliga för Function-appar som distribueras till en Premium-plan.

### <a name="pre-warmed-instances"></a>Förvärmade instanser

Om inga händelser och körningar inträffar idag i förbruknings planen kan din app skalas ned till noll instanser. När nya händelser kommer in måste en ny instans vara anpassad med din app som körs på den.  Det kan ta lite tid att utföra särskilda nya instanser beroende på appen.  Den ytterligare svars tiden för det första anropet kallas ofta app kall start.

I Premium-planen kan du ha din app förvärmad på ett angivet antal instanser, upp till din minsta schema storlek.  Förvärmade instanser gör det också möjligt för dig att förskala en app innan hög belastning. När appen skalas ut skalas den först till de förvärmade instanserna. Ytterligare instanser fortsätter att buffras och värmas omedelbart i förberedelser inför nästa skalnings åtgärd. Genom att ha en buffert för förvärmade instanser kan du effektivt förhindra kall start fördröjning.  Förvärmade instanser är en funktion i Premium-planen och du måste behålla minst en instans som körs och som är tillgänglig hela tiden när planen är aktiv.

Du kan konfigurera antalet förvärmade instanser i Azure Portal genom att välja **Funktionsapp**, gå till fliken **plattforms funktioner** och välja alternativen för **skala ut** . I redigerings fönstret för Function-appen är de förvärmade instanserna speciella för den appen, men minimi-och Max instanserna gäller hela planen.

![Inställningar för elastisk skalning](./media/functions-premium-plan/scale-out.png)

Du kan också konfigurera förvärmade instanser för en app med Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Anslutning till privat nätverk

Azure Functions som distribueras till en Premium-plan drar nytta av [ny VNet-integrering för webbappar](../app-service/web-sites-integrate-with-vnet.md).  När appen har kon figurer ATS kan den kommunicera med resurser i ditt VNet eller skyddas via tjänst slut punkter.  IP-begränsningar är också tillgängliga i appen för att begränsa inkommande trafik.

När du tilldelar ett undernät till din Function-app i en Premium-plan behöver du ett undernät med tillräckligt med IP-adresser för varje möjlig instans. Vi kräver ett IP-block med minst 100 tillgängliga adresser.

Mer information finns i [integrera din Function-app med ett VNet](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Snabb elastisk skalning

Ytterligare beräknings instanser läggs automatiskt till för din app med samma snabbt skalnings logik som förbruknings planen.  Mer information om hur skalning fungerar finns i [funktions skala och värd](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="longer-run-duration"></a>Varaktighet för längre körning

Azure Functions i en förbruknings plan är begränsad till 10 minuter för en enda körning.  I Premium-planen är varaktigheten för körningen standardvärdet 30 minuter för att förhindra överkörningar. Du kan dock [ändra Host. JSON-konfigurationen](./functions-host-json.md#functiontimeout) för att göra 60 minuter för program för Premium-plan.

## <a name="plan-and-sku-settings"></a>Planera och SKU-inställningar

När du skapar planen konfigurerar du två inställningar: det minsta antalet instanser (eller plan storlek) och den maximala burst-gränsen.  Minimi instanserna är reserverade och körs alltid.

> [!IMPORTANT]
> Du debiteras för varje instans som allokeras i minsta instans antal oavsett om funktionerna körs eller inte.

Om din app kräver instanser som ligger utanför din plan storlek, kan den fortsätta att skala ut tills antalet instanser träffar den maximala burst-gränsen.  Du debiteras för instanser utöver din plan storlek bara när de är igång och hyr till dig.  Vi kommer att få bästa möjliga prestanda när du skalar din app till den definierade Max gränsen, medan de minsta plan instanserna är garanterade för din app.

Du kan konfigurera plan storlek och Max belopp i Azure Portal genom att välja alternativen för **skala ut** i planen eller en Function-app som distribueras till den planen (under **plattforms funktioner**).

Du kan också öka den maximala burst-gränsen från Azure CLI:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Tillgängliga instanser SKU: er

När du skapar eller skalar planen kan du välja mellan tre instans storlekar.  Du debiteras för det totala antalet kärnor och använt minne per sekund.  Din app kan automatiskt skala ut till flera instanser efter behov.  

|SKU|Kärnor|Minne|Lagring|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7GB|250 GB|
|EP3|4|14 GB|250 GB|

## <a name="regions"></a>Regioner

Nedan finns de regioner som stöds för varje operativ system.

|Region| Windows | Linux |
|--| -- | -- |
|Australien, centrala| ✔<sup>1</sup> | |
|Australien, centrala 2| ✔<sup>1</sup> | |
|Australien, östra| ✔ | |
|Australien, sydöstra | ✔ | ✔<sup>1</sup> |
|Brasilien, södra| ✔<sup>2</sup> |  |
|Kanada, centrala| ✔ |  |
|USA, centrala| ✔ |  |
|Asien, östra| ✔ |  |
|USA, östra | ✔ | ✔<sup>1</sup> |
|USA, östra 2| ✔ |  |
|Frankrike, centrala| ✔ |  |
|Tyskland, västra centrala| ✔ | |
|Japan, östra| ✔ | ✔<sup>1</sup> |
|Japan, västra| ✔ | |
|Sydkorea, centrala| ✔ |  |
|USA, norra centrala| ✔ |  |
|Europa, norra| ✔ | ✔<sup>1</sup> |
|USA, södra centrala| ✔ | ✔<sup>1</sup> |
|Indien, södra | ✔ | |
|Asien, sydöstra| ✔ | ✔<sup>1</sup> |
|Storbritannien, södra| ✔ | |
|Storbritannien, västra| ✔ |  |
|Europa, västra| ✔ | ✔<sup>1</sup> |
|Indien, västra| ✔ |  |
|USA, västra| ✔ | ✔<sup>1</sup> |
|USA, västra 2| ✔ |  |

<sup>1</sup> Högsta skala begränsad till 20 instanser.  
<sup>2</sup> Högsta skala begränsad till 60 instanser.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förstå Azure Functions skalnings-och värd alternativ](functions-scale.md)

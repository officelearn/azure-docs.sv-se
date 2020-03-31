---
title: Azure Functions Premium-abonnemang
description: Information och konfigurationsalternativ (VNet, ingen kallstart, obegränsad körningstid) för Azure Functions Premium-planen.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: dd7f6d0760f2b848435e7c77657e261517d29dd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276912"
---
# <a name="azure-functions-premium-plan"></a>Azure Functions Premium-abonnemang

Azure Functions Premium-planen (kallas ibland Elastic Premium-abonnemang) är ett värdalternativ för funktionsappar. Premium-planen innehåller funktioner som VNet-anslutning, ingen kallstart och förstklassig maskinvara.  Flera funktionsappar kan distribueras till samma Premium-abonnemang och med planen kan du konfigurera beräkningsinstansstorlek, basplansstorlek och maximal planstorlek.  En jämförelse av Premium-planen och andra plan- och värdtyper finns i [alternativ för funktionsskala och hosting.](functions-scale.md)

## <a name="create-a-premium-plan"></a>Skapa en Premium-plan

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Du kan också skapa en Premium-plan med [az functionapp plan skapa](/cli/azure/functionapp/plan#az-functionapp-plan-create) i Azure CLI. I följande exempel skapas en _elastisk Premium 1-plan:_

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

Ersätt i det `<RESOURCE_GROUP>` här exemplet `<PLAN_NAME>` med resursgruppen och med ett namn för din plan som är unik i resursgruppen. Ange en [stöds `<REGION>` ](https://azure.microsoft.com/global-infrastructure/services/?products=functions). Om du vill skapa en Premium-plan som stöder Linux inkluderar du `--is-linux` alternativet.

Med planen skapad kan du använda [az functionapp skapa](/cli/azure/functionapp#az-functionapp-create) för att skapa din funktionsapp. I portalen skapas både planen och appen samtidigt. Ett exempel på ett fullständigt Azure CLI-skript finns i [Skapa en funktionsapp i en Premium-plan](scripts/functions-cli-create-premium-plan.md).

## <a name="features"></a>Funktioner

Följande funktioner är tillgängliga för att fungera appar som distribueras till en Premium-plan.

### <a name="pre-warmed-instances"></a>Förvärmda instanser

Om inga händelser och körningar inträffar i dag i förbrukningsplanen kan appen skalas till noll-instanser. När nya händelser kommer in måste en ny instans vara specialiserad med din app som körs på den.  Det kan ta lite tid att specialisera nya instanser beroende på appen.  Den här ytterligare svarstiden för det första samtalet kallas ofta app kallstart.

I Premium-planen kan du få appen förvärmd på ett visst antal instanser, upp till din minsta planstorlek.  Förvärmda instanser låter dig också förska skala en app före hög belastning. När appen skalas ut skalas den först in i de förvärmda instanserna. Ytterligare instanser fortsätter att buffra ut och värma omedelbart som förberedelse för nästa skalningsåtgärd. Genom att ha en buffert av förvärmda instanser kan du effektivt undvika kallstartsdytringar.  Förvärmda instanser är en funktion i Premium-planen och du måste hålla minst en instans igång och tillgänglig hela tiden planen är aktiv.

Du kan konfigurera antalet förvärmda instanser i Azure-portalen genom att välja din **funktionsapp,** gå till fliken **Plattformsfunktioner** och välja alternativ **för utskalning.** I funktionsappredigeringsfönstret är förvärmda instanser specifika för den appen, men de lägsta och högsta instanserna gäller för hela planen.

![Inställningar för elastisk skala](./media/functions-premium-plan/scale-out.png)

Du kan också konfigurera förvärmda instanser för en app med Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Privat nätverksanslutning

Azure Functions som distribueras till en Premium-plan drar nytta av [ny VNet-integrering för webbappar](../app-service/web-sites-integrate-with-vnet.md).  När den är konfigurerad kan appen kommunicera med resurser i ditt virtuella nätverk eller skyddas via tjänstslutpunkter.  IP-begränsningar är också tillgängliga i appen för att begränsa inkommande trafik.

När du tilldelar ett undernät till din funktionsapp i en Premium-plan behöver du ett undernät med tillräckligt många IP-adresser för varje potentiell instans. Vi behöver ett IP-block med minst 100 tillgängliga adresser.

Mer information finns i [integrera din funktionsapp med ett virtuella nätverk](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Snabb elastisk skala

Ytterligare beräkningsinstanser läggs automatiskt till för din app med samma snabba skalningslogik som förbrukningsplanen.  Mer information om hur skalning fungerar finns i [Funktionsskala och värdskap](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="longer-run-duration"></a>Längre körningstid

Azure Functions i en förbrukningsplan är begränsade till 10 minuter för en enda körning.  I Premium-planen är körningstiden som standard 30 minuter för att förhindra skenande körningar. Du kan dock [ändra host.json-konfigurationen](./functions-host-json.md#functiontimeout) så att den inte är obegränsad för Premium-planappar (garanterad 60 minuter).

## <a name="plan-and-sku-settings"></a>Planera och SKU-inställningar

När du skapar planen konfigurerar du två inställningar: det minsta antalet instanser (eller planstorlek) och den maximala burst-gränsen.  Minsta instanser är reserverade och körs alltid.

> [!IMPORTANT]
> Du debiteras för varje instans som allokeras i minsta antal instans oavsett om funktioner körs eller inte.

Om din app kräver instanser som ligger utanför planens storlek kan den fortsätta att skala ut tills antalet instanser når den maximala burst-gränsen.  Du faktureras för instanser utanför din planstorlek endast medan de körs och hyrs ut till dig.  Vi gör vårt bästa för att skala ut din app till den definierade maxgränsen, medan minimiplaninstanserna garanteras för din app.

Du kan konfigurera planens storlek och maximum i Azure-portalen genom att välja alternativ **för utskalning** i planen eller en funktionsapp som distribueras till den planen (under **Plattformsfunktioner**).

Du kan också öka den maximala burst-gränsen från Azure CLI:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Tillgängliga instanssskuser

När du skapar eller skalar planen kan du välja mellan tre instansstorlekar.  Du debiteras för det totala antalet kärnor och minne som förbrukas per sekund.  Appen kan automatiskt skalas ut till flera instanser efter behov.  

|SKU|Kärnor|Minne|Lagring|
|--|--|--|--|
|Ep1 (1)|1|3.5GB|250 GB|
|Ep2 (1999|2|7 GB|250 GB|
|Ep3 (på andra sätt)|4|14 GB|250 GB|

### <a name="memory-utilization-considerations"></a>Hänsyn till minnesanvändning
Att köra på en dator med mer minne innebär inte alltid att funktionsappen kommer att använda allt tillgängligt minne.

En JavaScript-funktionsapp begränsas till exempel av standardminnesgränsen i Node.js. Om du vill öka den här `languageWorkers:node:arguments` gränsen för `--max-old-space-size=<max memory in MB>`fast minne lägger du till appinställningen med värdet .

## <a name="region-max-scale-out"></a>Region Max skala ut

Nedan visas de värden som för närvarande stöds maximal utskalning för en enda plan i varje region och OS-konfiguration. För att begära en höjning vänligen öppna en supportbiljett.

Se den fullständiga regionala tillgängligheten av Funktioner här: [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|Region| Windows | Linux |
|--| -- | -- |
|Australien, centrala| 20 | Inte tillgänglig |
|Australien, centrala 2| 20 | Inte tillgänglig |
|Australien, östra| 100 | 20 |
|Australien, sydöstra | 100 | 20 |
|Brasilien, södra| 60 | 20 |
|Kanada, centrala| 100 | 20 |
|USA, centrala| 100 | 20 |
|Asien, östra| 100 | 20 |
|USA, östra | 100 | 20 |
|USA, östra 2| 100 | 20 |
|Frankrike, centrala| 100 | 20 |
|Tyskland Västra Central| 100 | Inte tillgänglig |
|Japan, östra| 100 | 20 |
|Japan, västra| 100 | 20 |
|Sydkorea, centrala| 100 | 20 |
|USA, norra centrala| 100 | 20 |
|Europa, norra| 100 | 20 |
|Norge Öst| 20 | 20 |
|USA, södra centrala| 100 | 20 |
|Indien, södra | 100 | Inte tillgänglig |
|Sydostasien| 100 | 20 |
|Storbritannien, södra| 100 | 20 |
|Storbritannien, västra| 100 | 20 |
|Europa, västra| 100 | 20 |
|Indien, västra| 100 | 20 |
|USA, västra centrala| 20 | 20 |
|USA, västra| 100 | 20 |
|USA, västra 2| 100 | 20 |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förstå azure functions skalnings- och värdalternativ](functions-scale.md)

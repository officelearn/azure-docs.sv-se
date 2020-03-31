---
title: Konfigurera funktionsappinställningar i Azure
description: Lär dig hur du konfigurerar Azure-funktionsappinställningar.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 08/14/2019
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 662a04dbcc39f3fa95b0098eb8fe556b18b3495b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276951"
---
# <a name="manage-your-function-app"></a>Hantera din funktionsapp 

I Azure Functions tillhandahåller en funktionsapp körningskontexten för dina enskilda funktioner. Funktionsappfunktioner gäller för alla funktioner som finns i en viss funktionsapp. Alla funktioner i en funktionsapp måste vara av samma [språk](supported-languages.md). 

Enskilda funktioner i en funktionsapp distribueras tillsammans och skalas tillsammans. Alla funktioner i samma funktionsapp delar resurser per instans när funktionsappen skalas. 

Anslutningssträngar, miljövariabler och andra programinställningar definieras separat för varje funktionsapp. Alla data som måste delas mellan funktionsappar ska lagras externt i ett lagrat sparat.

I den här artikeln beskrivs hur du konfigurerar och hanterar funktionsappar. 

> [!TIP]  
> Många konfigurationsalternativ kan också hanteras med hjälp av [Azure CLI]. 

## <a name="get-started-in-the-azure-portal"></a>Kom igång i Azure Portal

Börja med att gå till [Azure-portalen] och logga in på ditt Azure-konto. I sökfältet längst upp i portalen skriver du namnet på din funktionsapp. Välj den sedan i listan. När du har valt funktionsappen visas följande sida:

![Översikt över funktionsappar i Azure-portalen](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

Du kan navigera till allt du behöver för att hantera din funktionsapp från översiktssidan, särskilt **[programinställningarna](#settings)** och **[plattformsfunktionerna](#platform-features)**.

## <a name="application-settings"></a><a name="settings"></a>Programinställningar

På fliken **Programinställningar** finns inställningar som används av funktionsappen. Dessa inställningar lagras krypterade och du måste välja **Visa värden** för att se värdena i portalen. Du kan också komma åt programinställningar med hjälp av Azure CLI.

### <a name="portal"></a>Portalen

Om du vill lägga till en inställning i portalen väljer du **Ny programinställning** och lägger till det nya nyckelvärdesparet.

![Funktionsappinställningar i Azure-portalen.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Azure CLI

Kommandot [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) returnerar de befintliga programinställningarna, som i följande exempel:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

Kommandot [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) lägger till eller uppdaterar en programinställning. I följande exempel skapas en `CUSTOM_FUNCTION_APP_SETTING` inställning med `12345`en namngiven nyckel och värdet :


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>Använda programinställningar

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

När du utvecklar en funktionsapp lokalt måste du underhålla lokala kopior av dessa värden i projektfilen local.settings.json. Mer information finns i [Filen Lokala inställningar](functions-run-local.md#local-settings-file).

## <a name="platform-features"></a>Plattformsfunktioner

![Fliken Funktionsappplattformsfunktioner.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Funktionsappar körs i och underhålls av Azure App Service-plattformen. Dina funktionsappar har därför åtkomst till de flesta av funktionerna i Azures grundläggande webbhotellsplattform. Fliken **Plattformsfunktioner** är där du kommer åt de många funktionerna i App Service-plattformen som du kan använda i dina funktionsappar. 

> [!NOTE]
> Alla App Service-funktioner är inte tillgängliga när en funktionsapp körs på förbrukningsvärdplanen.

Resten av den här artikeln fokuserar på följande App Service-funktioner i Azure-portalen som är användbara för Funktioner:

+ [App Service-redigerare](#editor)
+ [Konsol](#console)
+ [Avancerade verktyg (Kudu)](#kudu)
+ [Distributionsalternativ](#deployment)
+ [CORS](#cors)
+ [Autentisering](#auth)

Mer information om hur du arbetar med apptjänstinställningar finns i [Konfigurera Azure App Service Settings](../app-service/configure-common.md).

### <a name="app-service-editor"></a><a name="editor"></a>Redigeraren för apptjänst

![App Service-redigeraren](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

App Service-redigeraren är en avancerad in-portal-redigerare som du kan använda för att ändra JSON-konfigurationsfiler och kodfiler. Om du väljer det här alternativet startas en separat webbläsarflik med en grundläggande redigerare. På så sätt kan du integrera med Git-databasen, köra och felsöka kod och ändra funktionsappinställningar. Den här redigeraren ger en förbättrad utvecklingsmiljö för dina funktioner jämfört med den inbyggda funktionsredigeraren.  

Vi rekommenderar att du överväger att utveckla dina funktioner på din lokala dator. När du utvecklar lokalt och publicerar till Azure är dina projektfiler skrivskyddade i portalen. Mer information finns i [Kod och testa Azure-funktioner lokalt](functions-develop-local.md).

### <a name="console"></a><a name="console"></a>Konsol

![Funktion app konsol](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

Portalkonsolen är ett idealiskt utvecklarverktyg när du föredrar att interagera med din funktionsapp från kommandoraden. Vanliga kommandon är katalog och filskapande och navigering, samt kör batchfiler och skript. 

När du utvecklar lokalt rekommenderar vi att du använder [Azure Functions Core Tools](functions-run-local.md) och Azure [CLI].

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Avancerade verktyg (Kudu)

![Konfigurera Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

De avancerade verktygen för App Service (kallas även Kudu) ger tillgång till avancerade administrativa funktioner i din funktionsapp. Från Kudu hanterar du systeminformation, appinställningar, miljövariabler, platstillägg, HTTP-huvuden och servervariabler. Du kan också starta **Kudu** genom att bläddra till SCM-slutpunkten för din funktionsapp, till exempel`https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment-center"></a><a name="deployment"></a>Distributionscenter

När du använder en källkontrolllösning för att utveckla och underhålla din funktionskod kan du med Deployment Center skapa och distribuera från källkontrollen. Ditt projekt skapas och distribueras till Azure när du gör uppdateringar. Mer information finns [i Distributionstekniker i Azure Functions](functions-deployment-technologies.md).

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Cross-origin resource sharing (CORS)

För att förhindra körning av skadlig kod på klienten blockerar moderna webbläsare begäranden från webbprogram till resurser som körs i en separat domän. [Med direktresursdelning (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) `Access-Control-Allow-Origin` kan en rubrik deklarera vilket ursprung som får anropa slutpunkter i funktionsappen.

#### <a name="portal"></a>Portalen

När du **konfigurerar** listan Tillåtet ursprung för `Access-Control-Allow-Origin` funktionsappen läggs huvudet automatiskt till i alla svar från HTTP-slutpunkter i funktionsappen. 

![Konfigurera cors-lista för funktionsappen](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

När jokertecknet`*`( ) används ignoreras alla andra domäner. 

Använd [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) kommandot för att lägga till en domän i listan över tillåtna ursprung. I följande exempel läggs contoso.com domänen till:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Använd [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) kommandot för att lista de aktuella tillåtna ursprungen.

### <a name="authentication"></a><a name="auth"></a>Autentisering

![Konfigurera autentisering för en funktionsapp](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

När funktioner använder en HTTP-utlösare kan du kräva att anrop först autentiseras. App Service stöder Azure Active Directory-autentisering och inloggning med sociala leverantörer, till exempel Facebook, Microsoft och Twitter. Mer information om hur du konfigurerar specifika autentiseringsleverantörer finns i [översikt över Azure App Service-autentisering](../app-service/overview-authentication-authorization.md). 


## <a name="next-steps"></a>Nästa steg

+ [Konfigurera inställningar för Azure App-tjänst](../app-service/configure-common.md)
+ [Löpande distribution för Azure Functions](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure-portal]: https://portal.azure.com

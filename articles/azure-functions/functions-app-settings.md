---
title: Referens för app-inställningar för Azure Functions
description: Referens dokumentation för Azure Functions app-inställningar eller miljövariabler.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: glenga
ms.openlocfilehash: 896179a393b870390991a8e9942f6e7287ec5c90
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063310"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referens för app-inställningar för Azure Functions

App-inställningar i en Function-app innehåller globala konfigurations alternativ som påverkar alla funktioner för den Function-appen. När du kör lokalt öppnas de här inställningarna som lokala [miljövariabler](functions-run-local.md#local-settings-file). Den här artikeln innehåller appinställningar som är tillgängliga i Function Apps.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Det finns andra globala konfigurations alternativ i [Host. JSON](functions-host-json.md) -filen och i den [lokala. Settings. JSON](functions-run-local.md#local-settings-file) -filen.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Application Insights Instrumentation-nyckeln om du använder Application Insights. Se [övervaka Azure Functions](functions-monitoring.md).

|Nyckel|Exempelvärde|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

I version 2. x av Functions-körningen konfigurerar du appens beteende baserat på körnings miljön. Det här värdet [läses vid initiering](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Du kan ange `AZURE_FUNCTIONS_ENVIRONMENT` till ett värde, men [tre värden](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) stöds: [utveckling](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [mellanlagring](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)och [produktion](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). När `AZURE_FUNCTIONS_ENVIRONMENT` inte är inställt, är standardvärdet `Development` på en lokal miljö och `Production` på Azure. Den här inställningen ska användas i stället för `ASPNETCORE_ENVIRONMENT` för att ställa in körnings miljön. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Valfri anslutnings sträng för lagrings konto för att lagra loggar och visa dem på fliken **övervakning** i portalen. Lagrings kontot måste vara ett allmänt syfte som stöder blobbar, köer och tabeller. Se krav för [lagrings konto](functions-infrastructure-as-code.md#storage-account) och [lagrings konto](functions-create-function-app-portal.md#storage-account-requirements).

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = [namn]; AccountKey = [nyckel]|

> [!TIP]
> För prestanda och erfarenhet rekommenderar vi att du använder APPINSIGHTS_INSTRUMENTATIONKEY och App Insights för övervakning i stället för AzureWebJobsDashboard

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` innebär att du inaktiverar standard sidan för landning som visas för rot-URL: en för en Function-app. Standardvärdet är `false`.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsDisableHomepage|sant|

När den här appens inställning utelämnas eller anges till `false`, visas en sida som liknar följande exempel som svar på URL: en `<functionappname>.azurewebsites.net`.

![Funktion för programmets landnings sida](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` innebär att versions läget används vid kompilering av .NET-kod; `false` innebär att använda fel söknings läge. Standardvärdet är `true`.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|sant|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

En kommaavgränsad lista över beta funktioner som ska aktive ras. Beta funktioner som aktive ras av de här flaggorna är inte produktion klara, men kan aktive ras för experimentell användning innan de går live.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Anger lagrings platsen eller providern som ska användas för nyckel lagring. För närvarande är de databaser som stöds Blob Storage ("BLOB") och det lokala fil systemet ("filer"). Standardvärdet är BLOB i version 2 och fil system i version 1.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsSecretStorageType|Filer|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Azure Functions runtime använder den här anslutnings strängen för lagrings kontot för alla funktioner utom för HTTP-utlösta funktioner. Lagrings kontot måste vara ett allmänt syfte som stöder blobbar, köer och tabeller. Se krav för [lagrings konto](functions-infrastructure-as-code.md#storage-account) och [lagrings konto](functions-create-function-app-portal.md#storage-account-requirements).

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [namn]; AccountKey = [nyckel]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Sökväg till den kompilator som används för TypeScript. Gör att du kan åsidosätta standardvärdet om du behöver.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTION\_APP\_redigera\_läge

Avgör om redigering i Azure Portal är aktive rad. Giltiga värden är "readwrite" och "ReadOnly".

|Nyckel|Exempelvärde|
|---|------------|
|FUNCTION\_APP\_redigera\_läge|ReadOnly|

## <a name="functions_extension_version"></a>FUNKTIONER\_tillägg\_VERSION

Den version av Functions runtime som ska användas i den här Function-appen. Ett tilde med huvud version innebär att använda den senaste versionen av den högre versionen (till exempel "~ 2"). När nya versioner av samma huvud version är tillgängliga installeras de automatiskt i Function-appen. Använd det fullständiga versions numret (till exempel "2.0.12345") för att fästa appen till en angiven version. Standardvärdet är "~ 2". Ett värde på `~1` att PIN-appen till version 1. x av körnings miljön.

|Nyckel|Exempelvärde|
|---|------------|
|FUNKTIONER\_tillägg\_VERSION|~ 2|

## <a name="functions_worker_process_count"></a>FUNKTIONER\_arbets\_PROCESS\_antal

Anger det maximala antalet språk arbets processer, med standardvärdet `1`. Det högsta tillåtna värdet är `10`. Funktions anrop distribueras jämnt mellan olika språk arbets processer. Språk arbets processer har skapats var 10: e sekund tills antalet funktioner\_arbets\_PROCESS\_antalet har nåtts. Användning av flera språk arbets processer är inte samma sak som [skalning](functions-scale.md). Överväg att använda den här inställningen när din arbets belastning har en blandning av processor gränser och I/O-kopplade anrop. Den här inställningen gäller för alla non-.NET-språk.

|Nyckel|Exempelvärde|
|---|------------|
|FUNKTIONER\_arbets\_PROCESS\_antal|2|


## <a name="functions_worker_runtime"></a>FUNCTIONs\_WORKer\_RUNTIME

Språk arbets körningen som ska läsas in i Function-appen.  Detta motsvarar det språk som används i ditt program (till exempel "dotNet"). För funktioner på flera språk måste du publicera dem till flera appar, var och en med motsvarande arbetares körnings värde.  Giltiga värden är `dotnet` (C#/F#), `node` (JavaScript/typescript), `java` (Java), `powershell` (PowerShell) och `python` (python).

|Nyckel|Exempelvärde|
|---|------------|
|FUNCTIONs\_WORKer\_RUNTIME|dotNet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Endast för förbruknings & Premium-planer. Anslutnings sträng för lagrings kontot där programmets kod och konfiguration lagras. Se [skapa en Function-app](functions-infrastructure-as-code.md#create-a-function-app).

|Nyckel|Exempelvärde|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [namn]; AccountKey = [nyckel]|

## <a name="website_contentshare"></a>WEBBPLATS\_CONTENTSHARE

Endast för förbruknings & Premium-planer. Fil Sök vägen till programmets kod och konfiguration. Används med WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Standard är en unik sträng som börjar med namnet på Function-appen. Se [skapa en Function-app](functions-infrastructure-as-code.md#create-a-function-app).

|Nyckel|Exempelvärde|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBBPLATS\_MAX\_dynamisk\_program\_skala ut\_ut

Det maximala antalet instanser som Function-appen kan skala ut till. Standardvärdet är ingen gräns.

> [!NOTE]
> Den här inställningen är en förhands gransknings funktion – och är bara tillförlitlig om värdet < = 5

|Nyckel|Exempelvärde|
|---|------------|
|WEBBPLATS\_MAX\_dynamisk\_program\_skala ut\_ut|5|

## <a name="website_node_default_version"></a>WEBBPLATS\_nod\_DEFAULT_VERSION

_Endast Windows._  
Anger den version av Node. js som ska användas när du kör din Function-app i Windows. Du bör använda tilde (~) om du vill att körnings miljön ska använda den senaste tillgängliga versionen av mål versionen. Om till exempel är inställt på `~10`används den senaste versionen av Node. js 10. När en högre version är riktad mot ett tilde behöver du inte uppdatera den lägre versionen manuellt. 

|Nyckel|Exempelvärde|
|---|------------|
|WEBBPLATS\_nod\_DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>WEBBPLATS\_köra\_från\_-paket

Gör att din Function-app kan köras från en monterad paket fil.

|Nyckel|Exempelvärde|
|---|------------|
|WEBBPLATS\_köra\_från\_-paket|1|

Giltiga värden är antingen en URL som matchar platsen för en distributions paket fil eller `1`. När det är inställt på `1`måste paketet finnas i mappen `d:\home\data\SitePackages`. När du använder zip-distribution med den här inställningen överförs paketet automatiskt till den här platsen. I för hands versionen hette den här inställningen `WEBSITE_RUN_FROM_ZIP`. Mer information finns i [köra funktioner från en paket fil](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Som standard använder proxyservrar en genväg för att skicka API-anrop från proxyservrar direkt till funktioner i samma Funktionsapp istället för att skapa en ny HTTP-begäran. Med den här inställningen kan du inaktivera beteendet.

|Nyckel|Värde|Beskrivning|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|sant|Anrop med en server dels-URL som pekar på en funktion i den lokala Funktionsapp kommer inte längre att skickas direkt till funktionen och kommer i stället att dirigeras tillbaka till HTTP-frontend för Funktionsapp|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Detta är standardvärdet. Anrop med en server dels-URL som pekar på en funktion i den lokala Funktionsapp vidarebefordras direkt till den funktionen|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Den här inställningen styr om% 2F avkodas som snedstreck i väg parametrar när de infogas i Server dels-URL: en. 

|Nyckel|Värde|Beskrivning|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|sant|Väg parametrar med kodade snedstreck kommer att avkodas. `example.com/api%2ftest` kommer att bli `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Detta är standard beteendet. Alla väg parametrar kommer att skickas längs oförändrade|

### <a name="example"></a>Exempel

Här är ett exempel på proxy. json i en Function-app på URL-myfunction.com

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```
|URL-avkodning|Indata|Resultat|
|-|-|-|
|sant|myfunction.com/test%2fapi|example.com/test/api
|false|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Nästa steg

[Lär dig hur du uppdaterar appinställningar](functions-how-to-use-azure-function-app-settings.md#settings)

[Se globala inställningar i Host. JSON-filen](functions-host-json.md)

[Se andra app-inställningar för App Service appar](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

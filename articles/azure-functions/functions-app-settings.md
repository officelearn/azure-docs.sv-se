---
title: Referens för appinställningar för Azure Functions
description: Referens dokumentation för Azure Functions app-inställningar eller miljövariabler.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 6f42c411263575040d4392b85542920e8f2463d4
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690752"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referens för appinställningar för Azure Functions

App-inställningar i en Function-app innehåller globala konfigurations alternativ som påverkar alla funktioner för den Function-appen. När du kör lokalt öppnas de här inställningarna som lokala [miljövariabler](functions-run-local.md#local-settings-file). Den här artikeln innehåller appinställningar som är tillgängliga i Function Apps.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Det finns andra globala konfigurations alternativ i [Host. JSON](functions-host-json.md) -filen och i den [lokala. Settings. JSON](functions-run-local.md#local-settings-file) -filen.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Instrumentation-nyckeln för Application Insights. Använd bara en av `APPINSIGHTS_INSTRUMENTATIONKEY` eller `APPLICATIONINSIGHTS_CONNECTIONSTRING`. Mer information finns i [övervaka Azure Functions](functions-monitoring.md). 

|Tangent|Exempelvärde|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connectionstring"></a>APPLICATIONINSIGHTS_CONNECTIONSTRING

Anslutnings strängen för Application Insights. Använd `APPLICATIONINSIGHTS_CONNECTIONSTRING` i stället `APPINSIGHTS_INSTRUMENTATIONKEY` för när din funktions app kräver tillagda anpassningar som stöds med hjälp av anslutnings strängen. Mer information finns i [anslutnings strängar](../azure-monitor/app/sdk-connection-string.md). 

|Tangent|Exempelvärde|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTIONSTRING|InstrumentationKey = [key]; IngestionEndpoint = [URL]; LiveEndpoint = [URL]; ProfilerEndpoint = [URL]; SnapshotEndpoint = [URL];|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

I version 2. x och senare versioner av Functions-körningen konfigurerar du appens beteende baserat på körnings miljön. Det här värdet [läses vid initiering](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Du kan ange `AZURE_FUNCTIONS_ENVIRONMENT` valfritt värde, men [tre värden](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) stöds: [utveckling](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [mellanlagring](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)och [produktion](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). När `AZURE_FUNCTIONS_ENVIRONMENT` inte har angetts används standardvärdet `Development` i en lokal miljö `Production` och på Azure. Den här inställningen ska användas i stället `ASPNETCORE_ENVIRONMENT` för att ställa in körnings miljön. 

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

I version 2. x och senare versioner av Functions-körningen kan program inställningarna åsidosätta [Host. JSON](functions-host-json.md) -inställningar i den aktuella miljön. Dessa åsidosättningar uttrycks som program inställningar med `AzureFunctionsJobHost__path__to__setting`namnet. Mer information finns i [Åsidosätt Host. JSON-värden](functions-host-json.md#override-hostjson-values).

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Valfri anslutnings sträng för lagrings konto för att lagra loggar och visa dem på fliken **övervakning** i portalen. Den här inställningen är endast giltig för appar som är riktade till version 1. x i Azure Functions Runtime. Lagrings kontot måste vara ett allmänt syfte som stöder blobbar, köer och tabeller. Mer information finns i [krav för lagrings konton](storage-considerations.md#storage-account-requirements).

|Tangent|Exempelvärde|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName =<name>; AccountKey =<key>|

> [!NOTE]
> För bättre prestanda och erfarenhet använder kör som version 2. x och senare versioner APPINSIGHTS_INSTRUMENTATIONKEY och App Insights för övervakning i stället för `AzureWebJobsDashboard`.

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true`innebär att inaktivera standard sidan för landning som visas för rot-URL: en för en Function-app. Standardvärdet är `false`.

|Tangent|Exempelvärde|
|---|------------|
|AzureWebJobsDisableHomepage|true|

När den här appens inställning utelämnas eller `false`anges till visas en sida som liknar följande exempel som svar på URL: `<functionappname>.azurewebsites.net`en.

![Funktion för programmets landnings sida](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true`innebär användning av versions läge när .NET-kod kompileras. `false` betyder Använd fel söknings läge. Standardvärdet är `true`.

|Tangent|Exempelvärde|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

En kommaavgränsad lista över beta funktioner som ska aktive ras. Beta funktioner som aktive ras av de här flaggorna är inte produktion klara, men kan aktive ras för experimentell användning innan de går live.

|Tangent|Exempelvärde|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Anger lagrings platsen eller providern som ska användas för nyckel lagring. För närvarande är de databaser som stöds Blob Storage ("BLOB") och det lokala fil systemet ("filer"). Standardvärdet är BLOB i version 2 och fil system i version 1.

|Tangent|Exempelvärde|
|---|------------|
|AzureWebJobsSecretStorageType|Filer|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Azure Functions runtime använder den här anslutnings strängen för lagrings kontot för alla funktioner utom för HTTP-utlösta funktioner. Lagrings kontot måste vara ett allmänt syfte som stöder blobbar, köer och tabeller. Se krav för [lagrings konto](functions-infrastructure-as-code.md#storage-account) och [lagrings konto](storage-considerations.md#storage-account-requirements).

|Tangent|Exempelvärde|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [namn]; AccountKey = [nyckel]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Sökväg till den kompilator som används för TypeScript. Gör att du kan åsidosätta standardvärdet om du behöver.

|Tangent|Exempelvärde|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>redigerings\_\_läge\_för Function-appen

Avgör om redigering i Azure Portal är aktive rad. Giltiga värden är "readwrite" och "ReadOnly".

|Tangent|Exempelvärde|
|---|------------|
|redigerings\_\_läge\_för Function-appen|ReadOnly|

## <a name="functions_extension_version"></a>FUNKTIONS\_tilläggs\_version

Den version av Functions runtime som ska användas i den här Function-appen. Ett tilde med huvud version innebär att använda den senaste versionen av den högre versionen (till exempel "~ 2"). När nya versioner av samma huvud version är tillgängliga installeras de automatiskt i Function-appen. Använd det fullständiga versions numret (till exempel "2.0.12345") för att fästa appen till en angiven version. Standardvärdet är "~ 2". Ett värde för `~1` PIN-modulen till version 1. x av körnings miljön.

|Tangent|Exempelvärde|
|---|------------|
|FUNKTIONS\_tilläggs\_version|~ 2|

## <a name="functions_v2_compatibility_mode"></a>\_Functions\_v2\_-kompatibilitetsläge

Den här inställningen gör att din Function-app kan köras i version 2. x-kompatibelt läge på version 3. x-körningsmiljön. Använd endast den här inställningen om du stöter på problem när [du uppgraderar din Function-app från version 2. x till 3. x i körnings miljön](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Den här inställningen är endast avsedd som en kortsiktig lösning medan du uppdaterar appen så att den körs korrekt på version 3. x. Den här inställningen stöds så länge [2. x-körningen stöds](functions-versions.md). Om du stöter på problem som förhindrar att din app körs på version 3. x utan att använda den här inställningen kan du [rapportera problemet](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Kräver att [tilläggs\_\_versionen av funktioner](functions-app-settings.md#functions_extension_version) anges till `~3`.

|Tangent|Exempelvärde|
|---|------------|
|\_Functions\_v2\_-kompatibilitetsläge|true|

## <a name="functions_worker_process_count"></a>FUNKTIONER\_arbets\_process\_antal

Anger det maximala antalet språk arbets processer, med standardvärdet `1`. Det högsta tillåtna värdet är `10`. Funktions anrop distribueras jämnt mellan olika språk arbets processer. Språk arbets processer har skapats var 10: e sekund tills antalet arbets\_\_\_processer som har angetts av funktioner har nåtts. Användning av flera språk arbets processer är inte samma sak som [skalning](functions-scale.md). Överväg att använda den här inställningen när din arbets belastning har en blandning av processor gränser och I/O-kopplade anrop. Den här inställningen gäller för alla non-.NET-språk.

|Tangent|Exempelvärde|
|---|------------|
|FUNKTIONER\_arbets\_process\_antal|2|


## <a name="functions_worker_runtime"></a>FUNKTIONER\_Worker\_runtime

Språk arbets körningen som ska läsas in i Function-appen.  Detta motsvarar det språk som används i ditt program (till exempel "dotNet"). För funktioner på flera språk måste du publicera dem till flera appar, var och en med motsvarande arbetares körnings värde.  Giltiga värden är `dotnet` (C#/f #), `node` (Java Script/typescript) `java` , (Java) `powershell` , (PowerShell) och `python` (python).

|Tangent|Exempelvärde|
|---|------------|
|FUNKTIONER\_Worker\_runtime|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Endast för förbruknings & Premium-planer. Anslutnings sträng för lagrings kontot där programmets kod och konfiguration lagras. Se [skapa en Function-app](functions-infrastructure-as-code.md#create-a-function-app).

|Tangent|Exempelvärde|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [namn]; AccountKey = [nyckel]|

## <a name="website_contentshare"></a>WEBBPLATS\_CONTENTSHARE

Endast för förbruknings & Premium-planer. Fil Sök vägen till programmets kod och konfiguration. Används med WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Standard är en unik sträng som börjar med namnet på Function-appen. Se [skapa en Function-app](functions-infrastructure-as-code.md#create-a-function-app).

|Tangent|Exempelvärde|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBBPLATS\_högsta\_dynamiska\_program\_skala\_ut

Det maximala antalet instanser som Function-appen kan skala ut till. Standardvärdet är ingen gräns.

> [!NOTE]
> Den här inställningen är en förhands gransknings funktion – och är bara tillförlitlig om värdet <= 5

|Tangent|Exempelvärde|
|---|------------|
|WEBBPLATS\_högsta\_dynamiska\_program\_skala\_ut|5|

## <a name="website_node_default_version"></a>noden\_\_webbplats DEFAULT_VERSION

_Endast Windows._  
Anger den version av Node. js som ska användas när du kör din Function-app i Windows. Du bör använda tilde (~) om du vill att körnings miljön ska använda den senaste tillgängliga versionen av mål versionen. Om till `~10`exempel är inställt på, används den senaste versionen av Node. js 10. När en högre version är riktad mot ett tilde behöver du inte uppdatera den lägre versionen manuellt. 

|Tangent|Exempelvärde|
|---|------------|
|noden\_\_webbplats DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>WEBBPLATS\_som\_körs\_från paket

Gör att din Function-app kan köras från en monterad paket fil.

|Tangent|Exempelvärde|
|---|------------|
|WEBBPLATS\_som\_körs\_från paket|1|

Giltiga värden är antingen en URL som matchar platsen för en distributions paket fil eller `1`. När det är `1`inställt på måste paketet finnas `d:\home\data\SitePackages` i mappen. När du använder zip-distribution med den här inställningen överförs paketet automatiskt till den här platsen. I för hands versionen hette den här `WEBSITE_RUN_FROM_ZIP`inställningen. Mer information finns i [köra funktioner från en paket fil](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Som standard använder proxyservrar en genväg för att skicka API-anrop från proxyservrar direkt till funktioner i samma Funktionsapp istället för att skapa en ny HTTP-begäran. Med den här inställningen kan du inaktivera beteendet.

|Tangent|Värde|Beskrivning|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Anrop med en server dels-URL som pekar på en funktion i den lokala Funktionsapp kommer inte längre att skickas direkt till funktionen och kommer i stället att dirigeras tillbaka till HTTP-frontend för Funktionsapp|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|falskt|Detta är standardvärdet. Anrop med en server dels-URL som pekar på en funktion i den lokala Funktionsapp vidarebefordras direkt till den funktionen|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Den här inställningen styr om% 2F avkodas som snedstreck i väg parametrar när de infogas i Server dels-URL: en. 

|Tangent|Värde|Beskrivning|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Väg parametrar med kodade snedstreck kommer att avkodas. `example.com/api%2ftest`blir`example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|falskt|Det här är standardbeteendet. Alla väg parametrar kommer att skickas längs oförändrade|

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
|true|myfunction.com/test%2fapi|example.com/test/api
|falskt|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Nästa steg

[Lär dig hur du uppdaterar appinställningar](functions-how-to-use-azure-function-app-settings.md#settings)

[Se globala inställningar i Host. JSON-filen](functions-host-json.md)

[Se andra app-inställningar för App Service appar](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

---
title: Referens för appinställningar för Azure Functions
description: Referensdokumentation för appinställningar för Azure Functions eller miljövariabler.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 4de6f093e43bbb8b3e258c3dd2a71f728beb7287
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769548"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referens för appinställningar för Azure Functions

Appinställningar i en funktionsapp innehåller globala konfigurationsalternativ som påverkar alla funktioner för den funktionsappen. När du kör lokalt öppnas de här inställningarna som lokala [miljövariabler](functions-run-local.md#local-settings-file). Den här artikeln innehåller appinställningar som är tillgängliga i funktionsappar.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Det finns andra globala konfigurationsalternativ i den [host.json](functions-host-json.md) fil och i den [local.settings.json](functions-run-local.md#local-settings-file) fil.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Klicka i instrumenteringsnyckeln Application Insights om du använder Application Insights. Se [övervaka Azure Functions](functions-monitoring.md).

|Nyckel|Exempelvärde|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032 skall-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

I version 2. x och senare versioner av Functions-körningen konfigurerar du appens beteende baserat på körnings miljön. Det här värdet [läses vid initiering](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Du kan ange `AZURE_FUNCTIONS_ENVIRONMENT` till ett värde, men [tre värden](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) stöds: [utveckling](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [mellanlagring](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)och [produktion](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). När `AZURE_FUNCTIONS_ENVIRONMENT` inte är inställt, är standardvärdet `Development` på en lokal miljö och `Production` på Azure. Den här inställningen ska användas i stället för `ASPNETCORE_ENVIRONMENT` för att ställa in körnings miljön. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Valfritt lagringskontots anslutningssträng för att lagra loggar och visa dem i den **övervakaren** i portalen. Lagringskontot måste vara ett allmänt som har stöd för blobar, köer och tabeller. Se [lagringskonto](functions-infrastructure-as-code.md#storage-account) och [krav för Lagringskonto](functions-create-function-app-portal.md#storage-account-requirements).

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [key]|

> [!TIP]
> För prestanda och upplevelse rekommenderar vi att du använder APPINSIGHTS_INSTRUMENTATIONKEY och App Insights för övervakning i stället för AzureWebJobsDashboard

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` innebär att inaktiverar landningssida som visas för rot-URL för en funktionsapp. Standardvärdet är `false`.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsDisableHomepage|sant|

När den här appinställningen är utelämnas eller värdet `false`, en sida som liknar följande exempel visas som svar på URL: en `<functionappname>.azurewebsites.net`.

![Funktionen applandningssida](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` innebär att använda Produktionsläge vid kompilering .NET-kod. `false` innebär att använda felsökningsläge. Standardvärdet är `true`.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|sant|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

En kommaavgränsad lista över beta vilka funktioner du vill aktivera. Beta-funktioner som aktiveras med dessa flaggor är inte klara för produktion, men kan aktiveras för experiment innan du publicerar.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsFeatureFlags|feature1 feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Anger lagringsplatsen eller provider som ska användas för lagring av nycklar. För närvarande stöds databaser är blob-lagring (”Blob”) och det lokala filsystemet (”filer”). Standardvärdet är blob i version 2 och filsystemet i version 1.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsSecretStorageType|Filer|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Azure Functions-runtime använder den här anslutningssträng för lagringskonto för alla funktioner utom HTTP-utlöst funktion. Lagringskontot måste vara ett allmänt som har stöd för blobar, köer och tabeller. Se [lagringskonto](functions-infrastructure-as-code.md#storage-account) och [krav för Lagringskonto](functions-create-function-app-portal.md#storage-account-requirements).

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [key]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Sökväg till kompilatorn används för TypeScript. Kan du åsidosätta standardinställningen om du behöver.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobs_TypeScriptPath|%Home%\typescript|

## <a name="function_app_edit_mode"></a>FUNKTIONEN\_APP\_REDIGERA\_LÄGE

Avgör om redigering i Azure Portal är aktive rad. Giltiga värden är ”readwrite” och ”skrivskyddad”.

|Nyckel|Exempelvärde|
|---|------------|
|FUNKTIONEN\_APP\_REDIGERA\_LÄGE|skrivskyddad|

## <a name="functions_extension_version"></a>FUNCTIONS\_TILLÄGGET\_VERSION

Versionen av funktionskörningen ska användas i den här funktionsappen. Tecknet tilde med huvudversion innebär att du använder den senaste versionen av den huvudsakliga versionen (till exempel ”~ 2”). När nya versioner för samma huvudversion är tillgängliga, installeras de automatiskt i funktionsappen. Om du vill fästa app på en specifik version, använder du det fullständiga versionsnumret (till exempel ”2.0.12345”). Standardvärdet är ”~ 2”. Värdet `~1` PIN-koder, din app till version 1.x av körningen.

|Nyckel|Exempelvärde|
|---|------------|
|FUNCTIONS\_TILLÄGGET\_VERSION|~ 2|

## <a name="functions_v2_compatibility_mode"></a>FUNCTIONs\_v2\_kompatibilitet\_läge

Den här inställningen gör att din Function-app kan köras i version 2. x-kompatibelt läge på version 3. x-körningsmiljön. Använd endast den här inställningen om du stöter på problem när [du uppgraderar din Function-app från version 2. x till 3. x i körnings miljön](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Den här inställningen är endast avsedd som en kortsiktig lösning medan du uppdaterar appen så att den körs korrekt på version 3. x. Den här inställningen stöds så länge [2. x-körningen stöds](functions-versions.md). Om du stöter på problem som förhindrar att din app körs på version 3. x utan att använda den här inställningen kan du [rapportera problemet](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Kräver att [funktioner\_tillägg\_version](functions-app-settings.md#functions_extension_version) anges till `~3`.

|Nyckel|Exempelvärde|
|---|------------|
|FUNCTIONs\_v2\_kompatibilitet\_läge|sant|

## <a name="functions_worker_process_count"></a>FUNKTIONER\_arbets\_PROCESS\_antal

Anger det maximala antalet språk arbets processer, med standardvärdet `1`. Det högsta tillåtna värdet är `10`. Funktions anrop distribueras jämnt mellan olika språk arbets processer. Språk arbets processer har skapats var 10: e sekund tills antalet funktioner\_arbets\_PROCESS\_antalet har nåtts. Användning av flera språk arbets processer är inte samma sak som [skalning](functions-scale.md). Överväg att använda den här inställningen när din arbets belastning har en blandning av processor gränser och I/O-kopplade anrop. Den här inställningen gäller för alla non-.NET-språk.

|Nyckel|Exempelvärde|
|---|------------|
|FUNKTIONER\_arbets\_PROCESS\_antal|2|


## <a name="functions_worker_runtime"></a>FUNCTIONS\_WORKER\_RUNTIME

Den worker CLR att läsa in i funktionsappen.  Detta motsvarar det språk som används i ditt program (till exempel ”dotnet”). För funktioner på flera språk behöver du publicera dem till flera appar, var och en med ett motsvarande värde för worker-runtime.  Giltiga värden är `dotnet` (C#/F#), `node` (JavaScript/typescript), `java` (Java), `powershell` (PowerShell) och `python` (python).

|Nyckel|Exempelvärde|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|DotNet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Endast för förbruknings & Premium-planer. Anslutningssträng för lagringskonto där funktionskod och konfiguration lagras. Se [skapa en funktionsapp](functions-infrastructure-as-code.md#create-a-function-app).

|Nyckel|Exempelvärde|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [key]|

## <a name="website_contentshare"></a>WEBBPLATSEN\_CONTENTSHARE

Endast för förbruknings & Premium-planer. Sökvägen till funktionskod och konfiguration. Använda med WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Standardvärdet är en unik sträng som börjar med funktionsappens namn. Se [skapa en funktionsapp](functions-infrastructure-as-code.md#create-a-function-app).

|Nyckel|Exempelvärde|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBBPLATSEN\_MAX\_DYNAMISK\_PROGRAMMET\_SKALA\_UT

Det maximala antalet instanser som funktionsappen skalas ut. Standard finns ingen gräns.

> [!NOTE]
> Den här inställningen är en förhandsversion funktion – och endast tillförlitliga om ett värde < = 5

|Nyckel|Exempelvärde|
|---|------------|
|WEBBPLATSEN\_MAX\_DYNAMISK\_PROGRAMMET\_SKALA\_UT|5|

## <a name="website_node_default_version"></a>WEBBPLATSEN\_NODEN\_DEFAULT_VERSION

_Endast Windows._  
Anger den version av Node. js som ska användas när du kör din Function-app i Windows. Du bör använda tilde (~) om du vill att körnings miljön ska använda den senaste tillgängliga versionen av mål versionen. Om till exempel är inställt på `~10`används den senaste versionen av Node. js 10. När en högre version är riktad mot ett tilde behöver du inte uppdatera den lägre versionen manuellt. 

|Nyckel|Exempelvärde|
|---|------------|
|WEBBPLATSEN\_NODEN\_DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>WEBBPLATSEN\_KÖR\_FROM\_PAKET

Gör att funktionsappen ska köras från en monterad paketfil.

|Nyckel|Exempelvärde|
|---|------------|
|WEBBPLATSEN\_KÖR\_FROM\_PAKET|1|

Giltiga värden är antingen en URL som motsvarar platsen för en paketfil för distribution eller `1`. När värdet `1`, paketet måste finnas i den `d:\home\data\SitePackages` mapp. När du använder zip-distribution med den här inställningen överförs automatiskt paketet till den här platsen. I förhandsversion, kallades den här inställningen `WEBSITE_RUN_FROM_ZIP`. Mer information finns i [kör dina funktioner från en paketfil](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Functions proxies kommer som standard använder en genväg för att skicka API-anrop från proxyservrar direkt till funktioner i samma Funktionsapp i stället för att skapa en ny HTTP-begäran. Den här inställningen kan du inaktivera som standard.

|Nyckel|Värde|Beskrivning|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|sant|Anrop med en server dels-URL som pekar på en funktion i den lokala Funktionsapp kommer inte längre att skickas direkt till funktionen och kommer i stället att dirigeras tillbaka till HTTP-frontend för Funktionsapp|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Detta är standardvärdet. Anrop med en server dels-URL som pekar på en funktion i den lokala Funktionsapp vidarebefordras direkt till den funktionen|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Den här inställningen styr om % 2F avkodas som snedstreck i vägen parametrar när de infogas i backend-URL. 

|Nyckel|Värde|Beskrivning|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|sant|Parametrar för vägen med kodade snedstreck har dem avkodas. `example.com/api%2ftest` blir `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Detta är standardbeteendet. Alla vägar som kommer att överföras parametrar har inte ändrats|

### <a name="example"></a>Exempel

Här är ett exempel proxies.json i en funktionsapp i URL-myfunction.com

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
|sant|myFunction.com/test%2fapi|example.com/test/API
|false|myFunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Nästa steg

[Lär dig hur du uppdaterar appen inställningar](functions-how-to-use-azure-function-app-settings.md#settings)

[Se globala inställningar i filen host.json](functions-host-json.md)

[Se andra appinställningar för App Service-appar](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

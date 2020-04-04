---
title: Referens för appinställningar för Azure Functions
description: Referensdokumentation för appinställningarna eller miljövariablerna för Azure Functions.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: e2d168d8828d17e13f875e3b2555c7db0d4ba32d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656787"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referens för appinställningar för Azure Functions

Appinställningar i en funktionsapp innehåller globala konfigurationsalternativ som påverkar alla funktioner för funktionsappen. När du kör lokalt används dessa inställningar som lokala [miljövariabler](functions-run-local.md#local-settings-file). I den här artikeln visas de appinställningar som är tillgängliga i funktionsappar.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Det finns andra globala konfigurationsalternativ i [filen host.json](functions-host-json.md) och i filen [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Instrumenteringsnyckeln för Application Insights. Använd endast `APPINSIGHTS_INSTRUMENTATIONKEY` en `APPLICATIONINSIGHTS_CONNECTIONSTRING`av eller . Mer information finns i [Övervaka Azure-funktioner](functions-monitoring.md). 

|Nyckel|Exempelvärde|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connectionstring"></a>APPLICATIONINSIGHTS_CONNECTIONSTRING

Anslutningssträngen för Application Insights. Använd `APPLICATIONINSIGHTS_CONNECTIONSTRING` i `APPINSIGHTS_INSTRUMENTATIONKEY` stället för när funktionsappen kräver de tillagda anpassningar som stöds med hjälp av anslutningssträngen. Mer information finns i [Anslutningssträngar](../azure-monitor/app/sdk-connection-string.md). 

|Nyckel|Exempelvärde|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTIONSTRING|InstrumentationKey=[key];IngestionEndpoint=[url]; LiveEndpoint=[url]; ProfilerEndpoint=[url]; SnapshotEndpoint=[url];|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

I version 2.x och senare versioner av funktionskörningen konfigurerar appbeteende baserat på körningsmiljön. Det här värdet [läss under initieringen](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Du kan `AZURE_FUNCTIONS_ENVIRONMENT` ange vilket värde som helst, men [tre värden](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) stöds: [Utveckling,](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development) [Mellanlagring](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)och [Produktion](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). När `AZURE_FUNCTIONS_ENVIRONMENT` den inte är inställd, `Development` är det `Production` standard på en lokal miljö och på Azure. Den här inställningen bör `ASPNETCORE_ENVIRONMENT` användas i stället för för att ställa in körningsmiljön. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Valfri anslutningssträng för lagring av lagringskonto för lagring av loggar och visning av dem på fliken **Bildskärm** i portalen. Den här inställningen gäller endast för appar som är inriktade på version 1.x av Azure Functions-körningen. Lagringskontot måste vara ett allmänt syfte som stöder blobbar, köer och tabeller. Mer information finns i [Krav på lagringskonto](storage-considerations.md#storage-account-requirements).

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https; Kontonamn=<name>; AccountKey=<key>|

> [!NOTE]
> För bättre prestanda och upplevelse använder runtime version 2.x och senare versioner APPINSIGHTS_INSTRUMENTATIONKEY `AzureWebJobsDashboard`och App Insights för övervakning i stället för .

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHemsida

`true`innebär att inaktivera standardmålsidan som visas för rot-URL:en för en funktionsapp. Standardvärdet är `false`.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsDisableHemsida|true|

När den här appinställningen utelämnas eller ställs in på `false`visas en sida `<functionappname>.azurewebsites.net`som liknar följande exempel som svar på webbadressen .

![Målsida för funktionsapp](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true`avser användning Avgivningsläge vid kompilering av .NET-kod. `false` innebär att använda felsökningsläge. Standardvärdet är `true`.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

En kommaavgränsad lista över betafunktioner som ska aktiveras. Betafunktioner som aktiveras av dessa flaggor är inte produktionsklara, men kan aktiveras för experimentell användning innan de publiceras.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsFeatureFlags|funktion1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Anger den databas eller leverantör som ska användas för nyckellagring. För närvarande är de databaser som stöds bloblagring ("Blob") och det lokala filsystemet ("Filer"). Standard är blob i version 2 och filsystem i version 1.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsSecretStorageType|Filer|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Azure Functions-körningen använder den här anslutningssträngen för lagringskonto för alla funktioner utom HTTP-utlösta funktioner. Lagringskontot måste vara ett allmänt syfte som stöder blobbar, köer och tabeller. Se Krav [på lagringskonto](functions-infrastructure-as-code.md#storage-account) [och lagringskonto](storage-considerations.md#storage-account-requirements).

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https; AccountName=[namn]; AccountKey=[nyckel]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Sökväg till kompilatorn som används för TypeScript. Gör att du kan åsidosätta standardinställningen om du behöver.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>\_REDIGERINGSLÄGE\_FÖR FUNKTIONSAPP\_

Dikterar om redigering i Azure-portalen är aktiverat. Giltiga värden är "readwrite" och "readonly".

|Nyckel|Exempelvärde|
|---|------------|
|\_REDIGERINGSLÄGE\_FÖR FUNKTIONSAPP\_|ReadOnly|

## <a name="functions_extension_version"></a>TILLÄGGSVERSION\_\_AV FUNKTIONER

Den version av funktionskörningen som ska användas i den här funktionsappen. En tilde med huvudversion innebär att använda den senaste versionen av huvudversionen (till exempel "~2"). När nya versioner för samma huvudversion är tillgängliga installeras de automatiskt i funktionsappen. Om du vill fästa appen på en viss version använder du det fullständiga versionsnumret (till exempel "2.0.12345"). Standard är "~2". Värdet på `~1` fäster appen på version 1.x av körningen.

|Nyckel|Exempelvärde|
|---|------------|
|TILLÄGGSVERSION\_\_AV FUNKTIONER|~2|

## <a name="functions_v2_compatibility_mode"></a>KOMPATIBILITETSLÄGE\_\_FÖR FUNKTIONER V2\_

Med den här inställningen kan funktionsappen köras i ett version 2.x-kompatibelt läge på version 3.x-körningen. Använd den här inställningen endast om du stöter på problem när [du uppgraderar funktionsappen från version 2.x till 3.x av körningen](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Den här inställningen är endast avsedd som en kortsiktig lösning medan du uppdaterar appen så att den körs korrekt i version 3.x. Den här inställningen stöds så länge [2.x-körningen stöds](functions-versions.md). Om du stöter på problem som hindrar din app från att köras på version 3.x utan att använda den här inställningen kan du [rapportera problemet](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Kräver att [FUNKTIONSFÖRLÄNGNINGSVERSION\_\_](functions-app-settings.md#functions_extension_version) ställs in på . `~3`

|Nyckel|Exempelvärde|
|---|------------|
|KOMPATIBILITETSLÄGE\_\_FÖR FUNKTIONER V2\_|true|

## <a name="functions_worker_process_count"></a>ANTAL\_\_FUNKTIONER\_FÖR ARBETSPROCESS

Anger det maximala antalet språkarbetsprocesser, med `1`ett standardvärde på . Det högsta tillåtna värdet är `10`. Funktionsanrop fördelas jämnt mellan språkarbetarprocesser. Språkarbetsprocesser skapas var 10:e sekund\_\_tills antalet som anges av FUNCTIONS WORKER PROCESS\_COUNT har uppnåtts. Att använda flera språkarbetsprocesser är inte samma sak som [skalning](functions-scale.md). Överväg att använda den här inställningen när din arbetsbelastning har en blandning av CPU-bundna och I/O-bundna anrop. Den här inställningen gäller för alla non-.NET språk.

|Nyckel|Exempelvärde|
|---|------------|
|ANTAL\_\_FUNKTIONER\_FÖR ARBETSPROCESS|2|


## <a name="functions_worker_runtime"></a>FUNKTIONER\_\_ARBETARE RUNTIME

Språkarbetarens körning som ska läsas in i funktionsappen.  Detta motsvarar det språk som används i ditt program (till exempel "dotnet"). För funktioner på flera språk måste du publicera dem på flera appar, var och en med motsvarande arbetskörningsvärde.  Giltiga värden `dotnet` `node` är (C#/F#), `java` (JavaScript/TypeScript), (Java), `python` `powershell` (PowerShell) och (Python).

|Nyckel|Exempelvärde|
|---|------------|
|FUNKTIONER\_\_ARBETARE RUNTIME|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Endast för & Premium-abonnemang. Anslutningssträng för lagringskonto där funktionsappkoden och konfigurationen lagras. Se [Skapa en funktionsapp](functions-infrastructure-as-code.md#create-a-function-app).

|Nyckel|Exempelvärde|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https; AccountName=[namn]; AccountKey=[nyckel]|

## <a name="website_contentshare"></a>WEBBPLATS\_INNEHÅLLSDELNING

Endast för & Premium-abonnemang. Filsökvägen till funktionsappkoden och konfigurationen. Används med WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Standard är en unik sträng som börjar med funktionsappnamnet. Se [Skapa en funktionsapp](functions-infrastructure-as-code.md#create-a-function-app).

|Nyckel|Exempelvärde|
|---|------------|
|WEBSITE_CONTENTSHARE|funktionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBBPLATS\_\_MAX\_\_DYNAMISK\_APPLIKATION SKALA UT

Det maximala antalet instanser som funktionsappen kan skala ut till. Standard är ingen gräns.

> [!NOTE]
> Den här inställningen är en förhandsgranskningsfunktion - och endast tillförlitlig om den är inställd på ett värde <= 5

|Nyckel|Exempelvärde|
|---|------------|
|WEBBPLATS\_\_MAX\_\_DYNAMISK\_APPLIKATION SKALA UT|5|

## <a name="website_node_default_version"></a>WEBBPLATSNOD\_\_DEFAULT_VERSION

_Endast Windows._  
Anger vilken version av Nod.js som ska användas när du kör funktionsappen i Windows. Du bör använda en tilde (~) för att få körningen att använda den senaste tillgängliga versionen av den riktade huvudversionen. När du till `~10`exempel är inställd på används den senaste versionen av Node.js 10. När en huvudversion är riktad med en tilde behöver du inte uppdatera delversionen manuellt. 

|Nyckel|Exempelvärde|
|---|------------|
|WEBBPLATSNOD\_\_DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>WEBBPLATS\_\_KÖRA\_FRÅN PAKETET

Gör att funktionsappen kan köras från en monterad paketfil.

|Nyckel|Exempelvärde|
|---|------------|
|WEBBPLATS\_\_KÖRA\_FRÅN PAKETET|1|

Giltiga värden är antingen en URL som matchas till `1`platsen för en distributionspaketfil eller . När det `1`är inställt på `d:\home\data\SitePackages` måste paketet finnas i mappen. När du använder zip-distribution med den här inställningen överförs paketet automatiskt till den här platsen. I förhandsgranskningen fick `WEBSITE_RUN_FROM_ZIP`den här inställningen namnet . Mer information finns i [Kör dina funktioner från en paketfil](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Som standard Fungerar proxyservrar kommer att använda en genväg för att skicka API-anrop från proxyservrar direkt till funktioner i samma funktion App, snarare än att skapa en ny HTTP-begäran. Med den här inställningen kan du inaktivera det beteendet.

|Nyckel|Värde|Beskrivning|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Samtal med en serverdels-URL som pekar på en funktion i den lokala funktionsappen skickas inte längre direkt till funktionen och dirigeras i stället tillbaka till HTTP-klientdelen för funktionsappen|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Detta är standardvärdet. Samtal med en backend-URL som pekar på en funktion i den lokala funktionsappen vidarebefordras direkt till den funktionen|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Den här inställningen styr om %2F avkodas som snedstreck i flödesparametrar när de infogas i backend-URL:en. 

|Nyckel|Värde|Beskrivning|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Vägparametrar med kodade snedstreck kommer att få dem avkodade. `example.com/api%2ftest`kommer att bli`example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Det här är standardbeteendet. Alla ruttparametrar kommer att skickas längs oförändrade|

### <a name="example"></a>Exempel

Här är ett exempel proxies.json i en funktionsapp på URL-myfunction.com

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
|false|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Nästa steg

[Läs om hur du uppdaterar appinställningar](functions-how-to-use-azure-function-app-settings.md#settings)

[Se globala inställningar i filen host.json](functions-host-json.md)

[Visa andra appinställningar för App Service-appar](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

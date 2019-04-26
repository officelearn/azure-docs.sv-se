---
title: Referens för appinställningar för Azure Functions
description: Referensdokumentation för appinställningar för Azure Functions eller miljövariabler.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: glenga
ms.openlocfilehash: 573e6ea857ef17a6160f7e03f52f9c64f903530a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60347520"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referens för appinställningar för Azure Functions

Appinställningar i en funktionsapp innehåller globala konfigurationsalternativ som påverkar alla funktioner för den funktionsappen. När du kör lokalt, de här inställningarna används som lokala [miljövariabler](functions-run-local.md#local-settings-file). Den här artikeln innehåller appinställningar som är tillgängliga i funktionsappar.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Det finns andra globala konfigurationsalternativ i den [host.json](functions-host-json.md) fil och i den [local.settings.json](functions-run-local.md#local-settings-file) fil.

## <a name="appinsightsinstrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Klicka i instrumenteringsnyckeln Application Insights om du använder Application Insights. Se [övervaka Azure Functions](functions-monitoring.md).

|Nyckel|Exempelvärde|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032 skall-64f83bb83bb|

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
|AzureWebJobsDisableHomepage|true|

När den här appinställningen är utelämnas eller värdet `false`, en sida som liknar följande exempel visas som svar på URL: en `<functionappname>.azurewebsites.net`.

![Funktionen applandningssida](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` innebär att använda Produktionsläge vid kompilering .NET-kod. `false` innebär att använda felsökningsläge. Standardvärdet är `true`.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

En kommaavgränsad lista över beta vilka funktioner du vill aktivera. Beta-funktioner som aktiveras med dessa flaggor är inte klara för produktion, men kan aktiveras för experiment innan du publicerar.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsFeatureFlags|feature1 feature2|

## <a name="azurewebjobsscriptroot"></a>AzureWebJobsScriptRoot

Sökvägen till rotkatalogen där det *host.json* fil- och funktionen mappar finns. I en funktionsapp är standardvärdet `%HOME%\site\wwwroot`.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsScriptRoot|%Home%\site\wwwroot|

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

## <a name="azurewebjobstypescriptpath"></a>AzureWebJobs_TypeScriptPath

Sökväg till kompilatorn används för TypeScript. Kan du åsidosätta standardinställningen om du behöver.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobs_TypeScriptPath|%Home%\typescript|

## <a name="functionappeditmode"></a>FUNKTIONEN\_APP\_REDIGERA\_LÄGE

Avgör om redigering i Azure-portalen är aktiverad. Giltiga värden är ”readwrite” och ”skrivskyddad”.

|Nyckel|Exempelvärde|
|---|------------|
|FUNKTIONEN\_APP\_REDIGERA\_LÄGE|skrivskyddad|

## <a name="functionsextensionversion"></a>FUNCTIONS\_TILLÄGGET\_VERSION

Versionen av funktionskörningen ska användas i den här funktionsappen. Tecknet tilde med huvudversion innebär att du använder den senaste versionen av den huvudsakliga versionen (till exempel ”~ 2”). När nya versioner för samma huvudversion är tillgängliga, installeras de automatiskt i funktionsappen. Om du vill fästa app på en specifik version, använder du det fullständiga versionsnumret (till exempel ”2.0.12345”). Standardvärdet är ”~ 2”. Värdet `~1` PIN-koder, din app till version 1.x av körningen.

|Nyckel|Exempelvärde|
|---|------------|
|FUNCTIONS\_TILLÄGGET\_VERSION|~ 2|

## <a name="functionsworkerruntime"></a>FUNCTIONS\_WORKER\_RUNTIME

Den worker CLR att läsa in i funktionsappen.  Detta motsvarar det språk som används i ditt program (till exempel ”dotnet”). För funktioner på flera språk behöver du publicera dem till flera appar, var och en med ett motsvarande värde för worker-runtime.  Giltiga värden är `dotnet` (C#/F#), `node` (JavaScript/TypeScript) `java` (Java) och `python` (Python).

|Nyckel|Exempelvärde|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|DotNet|

## <a name="websitecontentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

För förbrukningsplaner. Anslutningssträng för lagringskonto där funktionskod och konfiguration lagras. Se [skapa en funktionsapp](functions-infrastructure-as-code.md#create-a-function-app).

|Nyckel|Exempelvärde|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [key]|

## <a name="websitecontentshare"></a>WEBBPLATSEN\_CONTENTSHARE

För förbrukningsplaner. Sökvägen till funktionskod och konfiguration. Använda med WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Standardvärdet är en unik sträng som börjar med funktionsappens namn. Se [skapa en funktionsapp](functions-infrastructure-as-code.md#create-a-function-app).

|Nyckel|Exempelvärde|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="websitemaxdynamicapplicationscaleout"></a>WEBBPLATSEN\_MAX\_DYNAMISK\_PROGRAMMET\_SKALA\_UT

Det maximala antalet instanser som funktionsappen skalas ut. Standard finns ingen gräns.

> [!NOTE]
> Den här inställningen är en förhandsversion funktion – och endast tillförlitliga om ett värde < = 5

|Nyckel|Exempelvärde|
|---|------------|
|WEBBPLATSEN\_MAX\_DYNAMISK\_PROGRAMMET\_SKALA\_UT|5|

## <a name="websitenodedefaultversion"></a>WEBBPLATSEN\_NODEN\_DEFAULT_VERSION

Standardvärdet är ”8.11.1”.

|Nyckel|Exempelvärde|
|---|------------|
|WEBBPLATSEN\_NODEN\_DEFAULT_VERSION|8.11.1|

## <a name="websiterunfrompackage"></a>WEBBPLATSEN\_KÖR\_FROM\_PAKET

Gör att funktionsappen ska köras från en monterad paketfil.

|Nyckel|Exempelvärde|
|---|------------|
|WEBBPLATSEN\_KÖR\_FROM\_PAKET|1|

Giltiga värden är antingen en URL som motsvarar platsen för en paketfil för distribution eller `1`. När värdet `1`, paketet måste finnas i den `d:\home\data\SitePackages` mapp. När du använder zip-distribution med den här inställningen överförs automatiskt paketet till den här platsen. I förhandsversion, kallades den här inställningen `WEBSITE_RUN_FROM_ZIP`. Mer information finns i [kör dina funktioner från en paketfil](run-functions-from-deployment-package.md).

## <a name="azurefunctionproxydisablelocalcall"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Functions proxies kommer som standard använder en genväg för att skicka API-anrop från proxyservrar direkt till funktioner i samma Funktionsapp i stället för att skapa en ny HTTP-begäran. Den här inställningen kan du inaktivera som standard.

|Nyckel|Värde|Beskrivning|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Anrop med en backend-url som pekar på en funktion i lokalt Funktionsappen inte längre kommer att skickas direkt till funktionen och i stället att skickas tillbaka till HTTP-klientdel för Funktionsappen|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Detta är standardvärdet. Anrop med en backend-url som pekar på en funktion i lokalt Funktionsapp vidarebefordras direkt till funktionen|


## <a name="azurefunctionproxybackendurldecodeslashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Den här inställningen styr om % 2F avkodas som snedstreck i vägen parametrar när de infogas i backend-URL. 

|Nyckel|Värde|Beskrivning|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Parametrar för vägen med kodade snedstreck har dem avkodas. `example.com/api%2ftest` blir `example.com/api/test`|
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
|true|myFunction.com/test%2fapi|example.com/test/API
|false|myFunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Nästa steg

[Lär dig hur du uppdaterar appen inställningar](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)

[Se globala inställningar i filen host.json](functions-host-json.md)

[Se andra appinställningar för App Service-appar](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

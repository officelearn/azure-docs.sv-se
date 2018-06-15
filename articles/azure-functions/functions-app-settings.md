---
title: Referens för inställningar för Azure Functions
description: I referensdokumentationen för Azure Functions app-inställningar eller miljövariabler.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/26/2017
ms.author: tdykstra
ms.openlocfilehash: bd5603b8f0e15eeae9dd3799d4e10952e115680f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194275"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referens för inställningar för Azure Functions

App-inställningar i en funktionsapp innehålla globala konfigurationsalternativ som påverkar alla funktioner för funktionen appen. När du kör lokalt, är dessa inställningar i miljövariabler. Den här artikeln innehåller appinställningar som är tillgängliga i funktionen appar.

Det finns andra globala konfigurationsalternativ som finns i den [host.json](functions-host-json.md) fil och i den [local.settings.json](functions-run-local.md#local-settings-file) fil.

## <a name="appinsightsinstrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Application Insights instrumentation nyckel om du använder Application Insights. Se [övervaka Azure Functions](functions-monitoring.md).

|Nyckel|Exempelvärde|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9 af77-484b-9032 skall-64f83bb83bb|

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Valfri konto anslutningssträngen för lagring av loggar och visa dem i den **övervakaren** i portalen. Lagringskontot måste vara en generell som stöder blobbar, köer och tabeller. Se [lagringskonto](functions-infrastructure-as-code.md#storage-account) och [konto lagringskraven](functions-create-function-app-portal.md#storage-account-requirements).

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [nyckel]|

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` innebär att inaktivera standard landningssida som visas för rot-URL för en funktionsapp. Standardvärdet är `false`.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsDisableHomepage|true|

När den här appinställningen utelämnas eller värdet `false`, en sida som liknar följande exempel visas som svar på URL: en `<functionappname>.azurewebsites.net`.

![Funktionen Landningssida för app](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` sätt använder versionsläge vid kompilering av .NET-kod. `false` sätt använder felsökningsläge. Standardvärdet är `true`.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

En kommaavgränsad lista över beta-funktionerna för att möjliggöra. Beta-funktioner som aktiveras av dessa flaggor är inte redo produktion, men kan aktiveras för experiment innan du aktiverar.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsFeatureFlags|feature1 feature2|

## <a name="azurewebjobsscriptroot"></a>AzureWebJobsScriptRoot

Sökvägen till rotkatalogen där det *host.json* fil- och funktionen mappar finns. I en funktionsapp är standardvärdet `%HOME%\site\wwwroot`.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsScriptRoot|%Home%\site\wwwroot|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Anger den databas eller en provider som ska användas för lagring av nycklar. Databaserna som stöds för närvarande blob (”Blob”) och filsystem (”inaktiverad”). Standardvärdet är filsystem (”inaktiverad”).

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsSecretStorageType|inaktiverad|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Azure Functions-runtime använder den här anslutningssträngen för storage-konto för alla funktioner utom HTTP utlöses funktioner. Lagringskontot måste vara en generell som stöder blobbar, köer och tabeller. Se [lagringskonto](functions-infrastructure-as-code.md#storage-account) och [konto lagringskraven](functions-create-function-app-portal.md#storage-account-requirements).

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [nyckel]|

## <a name="azurewebjobstypescriptpath"></a>AzureWebJobs_TypeScriptPath

Sökvägen till kompileraren för maskin. Kan du åsidosätta standardinställningen om du behöver.

|Nyckel|Exempelvärde|
|---|------------|
|AzureWebJobs_TypeScriptPath|%Home%\typescript|

## <a name="functionappeditmode"></a>FUNKTIONEN\_APP\_REDIGERA\_LÄGE

Giltiga värden är ”readwrite” och ”readonly”.

|Nyckel|Exempelvärde|
|---|------------|
|FUNKTIONEN\_APP\_REDIGERA\_LÄGE|skrivskyddad|

## <a name="functionsextensionversion"></a>FUNKTIONER\_TILLÄGGET\_VERSION

Versionen av Azure Functions-runtime ska användas i den här funktionen appen. Tilde med huvudversionen innebär att du använder den senaste versionen av den högre versionen (till exempel ”~ 1). När nya versioner för samma huvudversion, installeras de automatiskt i appen funktion. Använd det fullständiga versionsnumret (till exempel ”1.0.12345”) för att fästa appen till en specifik version. Standardvärdet är ”~ 1”.

|Nyckel|Exempelvärde|
|---|------------|
|FUNKTIONER\_TILLÄGGET\_VERSION|~1|

## <a name="websitecontentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

För förbrukning-planer. Anslutningssträngen för lagringskontot där appen Funktionskoden och konfiguration lagras. Se [skapa en funktionsapp](functions-infrastructure-as-code.md#create-a-function-app).

|Nyckel|Exempelvärde|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [nyckel]|

## <a name="websitecontentshare"></a>WEBSITE_CONTENTSHARE

För förbrukning-planer. Filsökvägen till app Funktionskoden och konfiguration. Används med WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Standardvärdet är en unik sträng som börjar med funktionsnamnet app. Se [skapa en funktionsapp](functions-infrastructure-as-code.md#create-a-function-app).

|Nyckel|Exempelvärde|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="websitemaxdynamicapplicationscaleout"></a>WEBBPLATSEN\_MAX\_DYNAMISKA\_PROGRAMMET\_SKALA\_UT

Maximalt antal instanser som appen funktion kan skalas ut. Standard finns ingen gräns.

> [!NOTE]
> Den här inställningen är för en förhandsvisningsfunktion.

|Nyckel|Exempelvärde|
|---|------------|
|WEBBPLATSEN\_MAX\_DYNAMISKA\_PROGRAMMET\_SKALA\_UT|10|

## <a name="websitenodedefaultversion"></a>WEBBPLATSEN\_NOD\_DEFAULT_VERSION

Standardvärdet är ”6.5.0”.

|Nyckel|Exempelvärde|
|---|------------|
|WEBBPLATSEN\_NOD\_DEFAULT_VERSION|6.5.0|

## <a name="next-steps"></a>Nästa steg

[Lär dig hur du uppdatera app-inställningar](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)

[Globala inställningar i filen host.json](functions-host-json.md)

[Finns andra appinställningar för Apptjänst-appar](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

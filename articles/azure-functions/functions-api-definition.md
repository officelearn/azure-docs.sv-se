---
title: OpenAPI-metadata i Azure Functions | Microsoft Docs
description: Översikt över OpenAPI-stöd i Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: 68077e576fe3451627d8a5c8e1ff1b26d06d96b7
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091857"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Stöd för OpenAPI 2.0-metadata i Azure Functions (förhandsversion)
OpenAPI 2.0 (tidigare Swagger) metadata stöds i Azure Functions är en förhandsgranskningsfunktion som du kan använda för att skriva en definition för OpenAPI 2.0 i en funktionsapp. Du kan sedan ha filen med hjälp av funktionsappen.

[OpenAPI-metadata](http://swagger.io/) gör en funktion som är värd för en REST-API för att användas av en stor mängd annan programvara. Den här programvaran innehåller Microsoft-erbjudanden som PowerApps och [API Apps-funktionen i Azure App Service](../app-service/app-service-web-overview.md), utvecklingsverktyg från tredje part som [Postman](https://www.getpostman.com/docs/importing_swagger), och [många andra paket](http://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Vi rekommenderar att du börjar med den [komma igång-självstudiekurs](./functions-api-definition-getting-started.md) och sedan tillbaka till det här dokumentet för mer information om specifika funktioner.

## <a name="enable"></a>Aktivera stöd för OpenAPI-definition
Du kan konfigurera alla OpenAPI-inställningar på den **API-Definition** sidan i din funktionsapp **plattformsfunktioner**.

> [!NOTE]
> Funktionen API-definition-funktionen stöds inte för beta-runtime för närvarande.

Om du vill aktivera generering av en värdbaserad OpenAPI-definition och en definition av Snabbstart, ange **API-definitionskälla** till **funktion (förhandsversion)**. **Externa URL: en** tillåter funktionen att använda en OpenAPI-definition som har är värdbaserad någon annanstans.

## <a name="generate-definition"></a>Generera en Swagger-stommen från din funktion metadata
Med hjälp av en mall kan du börja skriva din första OpenAPI-definition. Funktionen definition mallen skapar en null-optimerade OpenAPI-definitionen genom att använda alla metadata i filen function.json för var och en av dina funktioner för HTTP-utlösare. Du måste fylla i mer information om ditt API från den [OpenAPI-specifikation](http://swagger.io/specification/), till exempel mallar för begäranden och svar.

Stegvisa instruktioner finns i den [komma igång-självstudiekurs](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Tillgängliga mallar

|Namn| Beskrivning |
|:-----|:-----|
|Definitionen|En OpenAPI-definition med den maximala mängden information som kan härledas från befintliga metadata för den funktionen.|

### <a name="quickstart-details"></a>Inkluderade metadata i definitionen

I följande tabell representerar inställningar för Azure portal och motsvarande data i function.json som det är kopplat till den genererade Swagger-stommen.

|Swagger.JSON|Portalens användargränssnitt|Function.JSON|
|:----|:-----|:-----|
|[Värd](http://swagger.io/specification/#fixed-fields-15)|**Funktionen appinställningar** > **App Service-inställningarna** > **översikt** > **URL**|*Finns inte*
|[Sökvägar](http://swagger.io/specification/#paths-object-29)|**Integrera** > **valda HTTP-metoder**|Bindningar: väg
|[Sökväg till objekt](http://swagger.io/specification/#path-item-object-32)|**Integrera** > **flödesmallen**|Bindningar: metoder
|[Säkerhet](http://swagger.io/specification/#security-scheme-object-112)|**nycklar**|*Finns inte*|
|operationID*|**Väg + tillåtna verb**|Väg + tillåtna verb|

\*Åtgärds-ID krävs endast för att integrera med PowerApps och Flow.
> [!NOTE]
> X-ms-summary-tillägget tillhandahåller ett visningsnamn i Logikappar, PowerApps och Flow.
>
> Mer information finns i [anpassa Swagger-definitionen för PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="CICD"></a>Använda CI/CD för att ange en API-definition

 Du måste aktivera API-definition som är värd i portalen innan du aktiverar källkontroll att ändra API-definitionen från källkontroll. Följ dessa anvisningar:

1. Bläddra till **API-Definition (förhandsversion)** i din funktionsappinställningarna.
  1. Ange **API-definitionskälla** till **funktionen**.
  1. Klicka på **generera API-definitionsmall** och sedan **spara** att skapa en definition för mallen för att ändra senare.
  1. Observera att din URL för API-definition och nyckel.
1. [Konfigurera kontinuerlig integrering/kontinuerlig distribution (CI/CD)](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements).
2. Ändra swagger.json i källkontrollen på \site\wwwroot\.azurefunctions\swagger\swagger.json.

Nu kan ändringar av swagger.json i databasen finns på din funktionsapp på API: et definition URL och nyckel som du antecknade i steg 1.c.

## <a name="next-steps"></a>Nästa steg
* [Komma igång-självstudiekurs](functions-api-definition-getting-started.md). Prova vår genomgång om du vill se en OpenAPI-definition i praktiken.
* [Azure Functions GitHub-lagringsplats](https://github.com/Azure/Azure-Functions/). Kolla in funktioner databasen för att ge oss feedback om förhandsversionen av API-definition support. Se ett GitHub-ärende för allt du vill se uppdaterade.
* [Azure Functions-utvecklare](functions-reference.md). Läs mer om kodning av funktioner och definiering av utlösare och bindningar.

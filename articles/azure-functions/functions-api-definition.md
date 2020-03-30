---
title: OpenAPI-metadata i Azure Functions
description: Översikt över OpenAPI-stöd i Azure Functions
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: a80079574dc29c54de89f5275c65637b205742d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74227404"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Support för OpenAPI 2.0-metadata i Azure Functions (förhandsversion)
OpenAPI 2.0 (tidigare Swagger) metadata stöd i Azure Functions är en förhandsgranskningsfunktion som du kan använda för att skriva en OpenAPI 2.0 definition i en funktionsapp. Du kan sedan vara värd för filen med hjälp av funktionsappen.

> [!IMPORTANT]
> Förhandsgranskningsfunktionen OpenAPI är i dag endast tillgänglig i 1.x-körningen. Information om hur du skapar en 1.x-funktionsapp [hittar du här](./functions-versions.md#creating-1x-apps).

[OpenAPI-metadata](https://swagger.io/) gör att en funktion som är värd för ett REST API kan förbrukas av en mängd andra program. Den här programvaran innehåller Microsoft-erbjudanden som PowerApps och [API Apps-funktionen i Azure App Service](../app-service/overview.md), utvecklarverktyg från tredje part som [Postman](https://www.getpostman.com/docs/importing_swagger)och [många fler paket](https://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Vi rekommenderar att du börjar med [självstudien](./functions-api-definition-getting-started.md) för att komma igång och sedan återgå till det här dokumentet för att lära dig mer om specifika funktioner.

## <a name="enable-openapi-definition-support"></a><a name="enable"></a>Aktivera OpenAPI-definitionsstöd
Du kan konfigurera alla OpenAPI-inställningar på **API Definition-sidan** i funktionsappens **plattformsfunktioner**.

> [!NOTE]
> Funktion API definition funktionen stöds inte för beta runtime för närvarande.

Om du vill aktivera generering av en värdad OpenAPI-definition och en snabbstartsdefinition anger du **API-definitionskällan** till **Funktion (förhandsversion).** **Med extern URL** kan funktionen använda en OpenAPI-definition som finns någon annanstans.

## <a name="generate-a-swagger-skeleton-from-your-functions-metadata"></a><a name="generate-definition"></a>Generera ett Swagger-skelett från funktionens metadata
En mall kan hjälpa dig att börja skriva din första OpenAPI-definition. Definitionsmallfunktionen skapar en gles OpenAPI-definition med hjälp av alla metadata i filen function.json för var och en av http-utlösarfunktionerna. Du måste fylla i mer information om ditt API från [OpenAPI-specifikationen,](https://swagger.io/specification/)till exempel mallar för begäran och svar.

Steg-för-steg-instruktioner finns i [självstudiekursen komma igång](./functions-api-definition-getting-started.md).

### <a name="available-templates"></a><a name="templates"></a>Tillgängliga mallar

|Namn| Beskrivning |
|:-----|:-----|
|Genererad definition|En OpenAPI-definition med den maximala mängden information som kan härledas från funktionens befintliga metadata.|

### <a name="included-metadata-in-the-generated-definition"></a><a name="quickstart-details"></a>Inkluderade metadata i den genererade definitionen

Följande tabell representerar Azure-portalinställningarna och motsvarande data i function.json när den mappas till det genererade Swagger-skelettet.

|Swagger.json (swagger.json)|Portal-användargränssnittet|Funktion.json|
|:----|:-----|:-----|
|[Värd](https://swagger.io/specification/#fixed-fields-15)|**Url** > **till översikt för** funktionsappinställningar**i** > **App Service** > |*Inte närvarande*
|[Sökvägar](https://swagger.io/specification/#paths-object-29)|**Integrera** > **valda HTTP-metoder**|Bindningar: Rutt
|[Sökvägsobjekt](https://swagger.io/specification/#path-item-object-32)|**Mall för integrera** > **rutt**|Bindningar: Metoder
|[Säkerhet](https://swagger.io/specification/#security-scheme-object-112)|**Nycklar**|*Inte närvarande*|
|operationID*|**Rutt + Tillåtna verb**|Rutt + tillåtna verb|

\*Åtgärds-ID krävs endast för integrering med PowerApps och Flow.
> [!NOTE]
> Tillägget x-ms-sammanfattning innehåller ett visningsnamn i Logic Apps, PowerApps och Flow.
>
> Mer information finns i [Anpassa swagger-definitionen för PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="use-cicd-to-set-an-api-definition"></a><a name="CICD"></a>Använda CI/CD för att ange en API-definition

 Du måste aktivera API-definitionshosting i portalen innan du aktiverar källkontroll för att ändra API-definitionen från källkontrollen. Följ dessa instruktioner:

1. Bläddra till **API Definition (förhandsgranskning)** i funktionsappinställningarna.
   1. Ange **API-definitionskälla** till **Funktion**.
   1. Klicka på **Generera API-definitionsmall** och sedan **Spara** om du vill skapa en malldefinition för att ändra senare.
   1. Observera url och nyckel för API-definition.
1. [Konfigurera kontinuerlig integrering/kontinuerlig distribution (CI/CD)](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#requirements-for-continuous-deployment).
2. Ändra swagger.json i källkontroll på\.\site\wwwroot azurefunctions\swagger\swagger.json.

Nu är ändringar i swagger.json i din databas värd din funktion app på API definition URL och nyckel som du noterade i steg 1.c.

## <a name="next-steps"></a>Nästa steg
* [Komma igång handledning](functions-api-definition-getting-started.md). Prova vår genomgång för att se en OpenAPI-definition i praktiken.
* [Azure Functions GitHub-databas](https://github.com/Azure/Azure-Functions/). Kolla in functions-arkivet för att ge oss feedback på förhandsversionen av API-definitionens stöd. Gör ett GitHub-problem för allt du vill se uppdaterat.
* [Referens för Azure Functions-utvecklare](functions-reference.md). Lär dig mer om kodningsfunktioner och definiera triggers och bindningar.

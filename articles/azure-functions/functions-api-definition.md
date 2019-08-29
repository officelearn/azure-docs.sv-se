---
title: OpenAPI metadata i Azure Functions | Microsoft Docs
description: Översikt över OpenAPI-stöd i Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: 00dedd069914384a1883a54bfb433857286fb49c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097580"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Stöd för OpenAPI 2,0-metadata i Azure Functions (för hands version)
OpenAPI 2,0 (tidigare Swagger) stöd för metadata i Azure Functions är en förhands gransknings funktion som du kan använda för att skriva en OpenAPI 2,0-definition i en Function-app. Du kan sedan vara värd för filen med hjälp av Function-appen.

> [!IMPORTANT]
> Förhandsgranskningsfunktionen OpenAPI är i dag endast tillgänglig i 1.x-körningen. Information om hur du skapar en 1.x-funktionsapp [hittar du här](./functions-versions.md#creating-1x-apps).

Med [openapi metadata](https://swagger.io/) kan en funktion som är värd för en REST API användas av en mängd olika program varor. I den här program varan ingår Microsoft-erbjudanden som PowerApps och den [API Apps funktionen i Azure App Service](../app-service/overview.md), verktyg från [](https://www.getpostman.com/docs/importing_swagger)tredje parts utvecklare som Postman och [många fler paket](https://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Vi rekommenderar att du börjar med [kursen komma igång](./functions-api-definition-getting-started.md) och sedan återgår till det här dokumentet för att lära dig mer om de olika funktionerna.

## <a name="enable"></a>Aktivera stöd för OpenAPI-definition
Du kan konfigurera alla OpenAPI-inställningar på sidan **API-definition** i funktions programmets plattforms **funktioner**.

> [!NOTE]
> Funktions-API definitions funktionen stöds inte för närvarande för beta körning.

Om du vill aktivera genereringen av en definition för värdbaserade OpenAPI och en snabb starts definition anger du **API-definition** till **funktion (för hands version)** . **Extern URL** gör att din funktion kan använda en openapi-definition som är värdbaserad någon annan stans.

## <a name="generate-definition"></a>Generera en Swagger-Skeleton från din funktions metadata
En mall kan hjälpa dig att börja skriva din första OpenAPI-definition. Med funktionen definitions mal len skapar du en OpenAPI-definition med hjälp av alla metadata i function. JSON-filen för var och en av dina HTTP-utlösare. Du måste fylla i mer information om ditt API från [openapi](https://swagger.io/specification/)-specifikationen, till exempel mallar för begäran och svar.

Stegvisa instruktioner finns i [komma igång-kursen](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Tillgängliga mallar

|Name| Beskrivning |
|:-----|:-----|
|Definition som skapats|En OpenAPI-definition med den maximala mängd information som kan härledas från funktionens befintliga metadata.|

### <a name="quickstart-details"></a>Inkluderade metadata i den genererade definitionen

Följande tabell visar Azure Portal inställningar och motsvarande data i function. JSON som den är mappad till den genererade Swagger-Skeleton.

|Swagger.json|Portal gränssnitt|Function. JSON|
|:----|:-----|:-----|
|[Värd](https://swagger.io/specification/#fixed-fields-15)|**Funktions program inställningar** > **App Service inställningar** > översikt**URL** > |*Finns inte*
|[Mappar](https://swagger.io/specification/#paths-object-29)|Integrera > **valda http-metoder**|Bindningar Routa
|[Sök vägs objekt](https://swagger.io/specification/#path-item-object-32)|Integrera > **Route-mall**|Bindningar Metoder
|[Säkerhet](https://swagger.io/specification/#security-scheme-object-112)|**Nyckel**|*Finns inte*|
|operationID*|**Route + tillåtna verb**|Route + tillåtna verb|

\*Åtgärds-ID krävs endast för integrering med PowerApps och Flow.
> [!NOTE]
> Tillägget x-MS-Summary ger ett visnings namn i Logic Apps, PowerApps och Flow.
>
> Mer information finns i [Anpassa din Swagger-definition för PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="CICD"></a>Använd CI/CD för att ange en API-definition

 Du måste aktivera API-definitionens värd i portalen innan du aktiverar käll kontroll för att ändra din API-definition från käll kontroll. Följ dessa anvisningar:

1. Bläddra till **API-definition (för hands version)** i inställningar för Function-appen.
   1. Ange **källa för API-definition** till **funktion**.
   1. Klicka på **skapa definitions definitions mall** och **Spara** för att skapa en mall definition för ändring senare.
   1. Notera API-definitionens URL och nyckel.
1. [Konfigurera kontinuerlig integrering/kontinuerlig distribution (CI/CD)](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#requirements-for-continuous-deployment).
2. Ändra Swagger. json i käll kontroll på \site\wwwroot\.azurefunctions\swagger\swagger.JSON.

Nu finns ändringar i Swagger. json i din lagrings plats för din Function-app på API-definitionens URL och nyckel som du antecknade i steg 1. c.

## <a name="next-steps"></a>Nästa steg
* [Själv studie kurs om att komma igång](functions-api-definition-getting-started.md). Prova vår genom gång för att se en OpenAPI-definition i praktiken.
* [Azure Functions GitHub](https://github.com/Azure/Azure-Functions/)-lagringsplatsen. Kolla in Functions-lagringsplatsen för att ge oss feedback om stöd för för hands versionen av API-definition. Gör ett GitHub-ärende för allt du vill se uppdaterat.
* [Referens för Azure Functions-utvecklare](functions-reference.md). Lär dig mer om att koda funktioner och definiera utlösare och bindningar.

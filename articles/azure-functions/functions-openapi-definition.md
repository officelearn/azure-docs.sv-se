---
title: Skapa en OpenAPI-definition för en funktion med Azure API Management
description: Skapa en OpenAPI-definition som gör det möjligt för andra appar och tjänster att anropa din funktion i Azure.
services: functions
keywords: OpenAPI, Swagger, cloud apps, cloud services,
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: glenga
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 255a7c9d0b9da15176fca90c6934a84fa0f863ed
ms.sourcegitcommit: 1d257ad14ab837dd13145a6908bc0ed7af7f50a2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65501861"
---
# <a name="create-an-openapi-definition-for-a-function-with-azure-api-management"></a>Skapa en OpenAPI-definition för en funktion med Azure API Management

REST API: er beskrivs ofta med hjälp av en OpenAPI-definition. Den här definitionen innehåller information om vilka åtgärder som är tillgängliga i API:t och hur data om förfrågningar och svar för API:t ska vara strukturerade.

I den här självstudien skapar du en funktion som avgör om en nödreparation på en vindturbin är kostnadseffektiv. Därefter kan du skapa en OpenAPI-definition för funktion appen med [Azure API Management](../api-management/api-management-key-concepts.md) så att funktionen kan anropas från andra appar och tjänster.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * skapar en funktion i Azure
> * Generera en OpenAPI-definition med hjälp av Azure API Management
> * testar definitionen genom att anropa funktionen.

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner. En funktionsapp kan du gruppera funktioner som en logisk enhet för enklare hantering, distribution, skalning och dela resurser.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>Skapa funktionen

Den här självstudien används en HTTP-utlöst funktion som tar två parametrar:

* Beräknad tid för att göra en turbinen reparera i timmar.
* Kapaciteten för turbinen i kilowatt. 

Funktionen beräknar sedan hur mycket en reparation skulle kosta och hur mycket intäkter turbinen kan generera under en 24-timmarsperiod. ATT skapa HTTP funktion i som utlöses av den [Azure-portalen](https://portal.azure.com).

1. Expandera funktionsappen och välj knappen **+** bredvid **Funktioner**. Välj **i portalen** > **fortsätta**.

1. Välj **fler mallar...** och välj sedan **Slutför och visa mallar**

1. Välj HTTP-utlösare, skriver du in `TurbineRepair` för funktionen **namn**, Välj `Function` för  **[autentiseringsnivå](functions-bindings-http-webhook.md#http-auth)**, och välj sedan  **Skapa**.  

    ![Skapa HTTP-funktion för OpenAPI](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Ersätt innehållet i run.csx C# skriptfil med följande kod och välj sedan **spara**:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    Den här funktionskoden returnerar meddelandet `Yes` eller `No` som indikation på om en nödreparation är kostnadseffektiv, intäktsmöjligheten som turbinen representerar och kostnaden för att reparera turbinen.

1. Testa funktionen genom att klicka på **Test** längst till höger för att expandera fliken Test. Ange följande värde för **Brödtext i förfrågan** och klicka sedan på **Kör**.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![Testa funktionen i Azure Portal](media/functions-openapi-definition/test-function.png)

    Följande värde returneras i brödtexten i svaret.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Nu har du en funktion som avgör om en nödreparation är kostnadseffektiv. Nu ska skapa du en OpenAPI-definition för funktionsappen.

## <a name="generate-the-openapi-definition"></a>Generera OpenAPI-definitionen

Nu är du redo att generera OpenAPI-definitionen.

1. Välj funktionsappen sedan i **plattformsfunktioner**, Välj **API Management** och välj **Skapa nytt** under **API Management**.

    ![Välj API Management i plattformsfunktioner](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Använd API Management-inställningar som anges i tabellen nedanför bilden.

    ![Skapa ny API Management-tjänsten](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Namn** | Globalt unikt namn | Ett namn genereras baserat på namnet på din funktionsapp. |
    | **Prenumeration** | Din prenumeration | Den prenumeration under vilken den här nya resursen skapas. |  
    | **[Resursgrupp](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Samma resurs som din funktionsapp, som ska få in åt dig. |
    | **Plats** | USA, västra | Välj USA, västra. |
    | **Organisationens namn** | Contoso | Namnet på den organisation som används på utvecklarportalen och e-postmeddelanden. |
    | **E-postadress för administratör** | din e-post | E-postmeddelande som har fått systemmeddelanden från API Management. |
    | **prisnivå** | Förbrukning (förhandsversion) | Förbrukning nivån är en förhandsversion och är inte tillgängligt i alla regioner. Fullständig prisinformation finns på [API Management-sidan med priser](https://azure.microsoft.com/pricing/details/api-management/) |

1. Välj **skapa** att skapa API Management-instans, vilket kan ta flera minuter.

1. Välj **aktivera Application Insights** för att skicka loggar till på samma plats som programmet funktion, acceptera den återstående standardvärdena och välj **länk API**.

1. Den **importera Azure Functions** öppnas med den **TurbineRepair** funktionen markerat. Välj **Välj** att fortsätta.

    ![Importera Azure Functions till API Management](media/functions-openapi-definition/import-function-openapi.png)

1. I den **skapa från Funktionsapp** , acceptera standardinställningarna och välj **skapa**

    ![Skapa från funktionsapp](media/functions-openapi-definition/create-function-openapi.png)

API: et har skapats för funktionen.

## <a name="test-the-openapi-definition"></a>Testa OpenAPI-definitionen

Innan du använder API-definition, bör du kontrollera att den fungerar.

1. På den **Test** i din funktion, väljer **POST** åtgärden

1. Ange värden för **timmar** och **kapacitet**

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Klicka på **skicka**, visa HTTP-svaret.

    ![Testa API-funktion](media/functions-openapi-definition/test-function-api-openapi.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Mer information om API Management](../api-management/api-management-key-concepts.md)

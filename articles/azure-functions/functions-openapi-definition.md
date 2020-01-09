---
title: Exponera dina funktioner med OpenAPI med hjälp av Azure API Management
description: Skapa en OpenAPI-definition som gör det möjligt för andra appar och tjänster att anropa din funktion i Azure.
ms.topic: tutorial
ms.date: 05/08/2019
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 56e2182b408b4e8b1f89eee7458a27c5dd54bb55
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562021"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>Skapa en OpenAPI-definition för ett Server lös API med hjälp av Azure API Management

REST API: er beskrivs ofta med hjälp av en OpenAPI-definition. Den här definitionen innehåller information om vilka åtgärder som är tillgängliga i API:t och hur data om förfrågningar och svar för API:t ska vara strukturerade.

I den här självstudien skapar du en funktion som avgör om en nödreparation på en vindturbin är kostnadseffektiv. Sedan skapar du en OpenAPI-definition för Function-appen med hjälp av [Azure API Management](../api-management/api-management-key-concepts.md) så att funktionen kan anropas från andra appar och tjänster.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * skapar en funktion i Azure
> * Generera en OpenAPI-definition med Azure API Management
> * testar definitionen genom att anropa funktionen.
> * Hämta OpenAPI-definitionen

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner. Med en Function-app kan du gruppera funktioner som en logisk enhet för enklare hantering, distribution, skalning och delning av resurser.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>Skapa funktionen

Den här självstudien använder en HTTP-utlöst funktion som tar två parametrar:

* Beräknad tid för att göra en turbin-reparation i timmar.
* Kapaciteten för turbinen i kilowatt. 

Funktionen beräknar sedan hur mycket en reparation skulle kosta och hur mycket intäkter turbinen kan generera under en 24-timmarsperiod. För att skapa funktionen HTTP-utlöst i [Azure Portal](https://portal.azure.com):

1. Expandera funktionsappen och välj knappen **+** bredvid **Funktioner**. Välj **i portal** > **Fortsätt**.

1. Välj **Fler mallar...** och välj sedan **Slutför och Visa mallar**

1. Välj HTTP-utlösare, skriv `TurbineRepair` som funktions **namn**, Välj `Function` för **[Autentiseringsnivå](functions-bindings-http-webhook.md#http-auth)** och välj sedan **skapa**.  

    ![Skapa HTTP-funktion för OpenAPI](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Ersätt innehållet i skript filen Run. CSX C# med följande kod och välj sedan **Spara**:

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

1. Testa funktionen genom att klicka på **test** längst till höger för att expandera fliken test. Ange följande värde för **begär ande texten**och klicka sedan på **Kör**.

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

Nu har du en funktion som avgör om en nödreparation är kostnadseffektiv. Därefter genererar du en OpenAPI-definition för Function-appen.

## <a name="generate-the-openapi-definition"></a>Generera OpenAPI-definitionen

Nu är du redo att generera OpenAPI-definitionen.

1. Välj Function-appen, Välj **API Management** i **plattforms funktioner**och välj **Skapa ny** under **API Management**.

    ![Välj API Management i plattforms funktioner](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Använd API Management inställningar som anges i tabellen nedanför bilden.

    ![Skapa ny API Management-tjänst](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Namn** | Globalt unikt namn | Ett namn genereras baserat på namnet på din Function-app. |
    | **Prenumeration** | Din prenumeration | Prenumerationen som den här nya resursen skapas under. |  
    | **[Resursgrupp](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Samma resurs som din Function-app, som ska ställas in för dig. |
    | **Plats** | USA, västra | Välj platsen för västra USA. |
    | **Organisationens namn** | Contoso | Namnet på organisationen som används i Developer-portalen och för e-postaviseringar. |
    | **E-postadress för administratör** | din e-post | E-post som tog emot system meddelanden från API Management. |
    | **prisnivå** | Konsumtion (för hands version) | Förbruknings nivån är i för hands version och är inte tillgänglig i alla regioner. Fullständig pris information finns på sidan med [API Management priser](https://azure.microsoft.com/pricing/details/api-management/) |

1. Välj **Skapa** om du vill skapa API Management-instansen, vilket kan ta flera minuter.

1. Välj **aktivera Application Insights** för att skicka loggar till samma plats som funktions programmet, Godkänn sedan de återstående standardvärdena och välj **Link API**.

1. **Import Azure Functions** öppnas med funktionen **TurbineRepair** markerad. Klicka på **Välj** för att fortsätta.

    ![Importera Azure Functions till API Management](media/functions-openapi-definition/import-function-openapi.png)

1. På sidan **skapa från Funktionsapp** godkänner du standardvärdena och väljer **skapa**

    ![Skapa från funktionsapp](media/functions-openapi-definition/create-function-openapi.png)

API: et har nu skapats för funktionen.

## <a name="test-the-api"></a>Testa API:et

Innan du använder OpenAPI-definitionen bör du kontrol lera att API: et fungerar.

1. På fliken **test** i funktionen väljer du **post** åtgärd.

1. Ange värden för **timmar** och **kapacitet**

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Klicka på **Skicka**och Visa http-svaret.

    ![API för test funktion](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="download-the-openapi-definition"></a>Hämta OpenAPI-definitionen

Om ditt API fungerar som förväntat kan du ladda ned OpenAPI-definitionen.

1. Välj **Hämta openapi-definition** överst på sidan.
   
   ![Ladda ned OpenAPI-definition](media/functions-openapi-definition/download-definition.png)

2. Öppna den nedladdade JSON-filen och granska definitionen.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

Du har använt API Management integration för att generera en OpenAPI definition av dina funktioner. Nu kan du redigera definitionen i API Management i portalen. Du kan också [läsa mer om API Management](../api-management/api-management-key-concepts.md).

> [!div class="nextstepaction"]
> [Redigera OpenAPI-definitionen i API Management](../api-management/edit-api.md)

---
title: "Skapa en OpenAPI definition för en funktion | Microsoft Docs"
description: "Skapa en definition av OpenAPI som gör det möjligt för andra appar och tjänster för att anropa en funktion i Azure."
services: functions
keywords: "OpenAPI, Swagger, molnappar, molntjänster"
documentationcenter: 
author: mgblythe
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/09/2017
ms.author: mblythe; glenga
ms.custom: mvc
ms.openlocfilehash: 1ad23a098ee0482b3c8c853ab5cee989f752a101
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="create-an-openapi-definition-for-a-function"></a>Skapa en OpenAPI definition för en funktion
REST API: er beskrivs ofta med hjälp av en OpenAPI definition (tidigare känt som en [Swagger](http://swagger.io/) filen). Den här definitionen innehåller information om vilka åtgärder är tillgängliga i en API och hur data för begäran och svar för API: et strukturerad.

I den här självstudiekursen skapar du en funktion som avgör om en reparation på en vind turbin är kostnadseffektiv. Du kan sedan skapa en OpenAPI definition för funktionen appen så att funktionen kan anropas från andra appar och tjänster.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en funktion i Azure
> * Generera en OpenAPI definition med hjälp av OpenAPI verktyg
> * Ändra definitionen för att tillhandahålla ytterligare metadata
> * Testa definitionen genom att anropa funktionen

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner. En funktion kan du gruppera funktioner som en logik enhet för enklare hantering, distribution, skala och delning av resurser. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]


## <a name="create-the-function"></a>Skapa funktionen

Den här kursen använder en HTTP-utlöses funktion som har två parametrar: beräknad tid för att göra en turbin reparera (i timmar). och kapaciteten för turbinen (i kilowatt). Funktionen beräknar sen hur mycket en reparation kostar och hur mycket intäkter turbinen kan göra under en 24-timmarsperiod.

1. Expandera funktionen appen och markera den  **+**  knappen bredvid **funktioner**. Om det är den första funktionen i din funktionsapp väljer du **Anpassad funktion**. Detta visar en fullständig uppsättning med funktionsmallar. 

    ![Sidan snabbstart för funktioner i Azure Portal](media/functions-openapi-definition/add-first-function.png)

2. Skriv i sökfältet `http` och välj sedan **C#** för HTTP-utlösaren mallen. 
 
    ![Välj HTTP-utlösare](./media/functions-openapi-definition/select-http-trigger-portal.png)

3. Typen `TurbineRepair` för funktionen **namn**, Välj `Function` för  **[autentiseringsnivå](functions-bindings-http-webhook.md#http-auth)**, och välj sedan **skapa**.  

    ![Skapa funktionen HTTP utlöses](./media/functions-openapi-definition/select-http-trigger-portal-2.png)

1. Ersätt innehållet i filen run.csx med följande kod och klicka sedan på **spara**:

    ```c#
    using System.Net;

    const double revenuePerkW = 0.12; 
    const double technicianCost = 250; 
    const double turbineCost = 100;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {   

        //Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();
        int hours = data.hours;
        int capacity = data.capacity;

        //Formulas to calculate revenue and cost
        double revenueOpportunity = capacity * revenuePerkW * 24;  
        double costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;

        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        }

        return req.CreateResponse(HttpStatusCode.OK, new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix         
        }); 
    }
    ```
    Den här Funktionskoden returnerar ett meddelande av `Yes` eller `No` att indikera om en reparation är kostnadseffektiv, samt omsättningen som representerar turbinen och kostnaden för att åtgärda turbinen. 

1. Testa funktionen genom att klicka på **testa** längst till höger att expandera fliken test. Ange följande värde för den **Begärandetext**, och klicka sedan på **kör**.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![Testa funktionen i Azure-portalen](media/functions-openapi-definition/test-function.png)

    Följande värde returneras i brödtexten i svaret.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Nu har du en funktion som bestämmer kostnadseffektivitet för reparation. Sedan skapa och ändra en OpenAPI för funktionen appen.

## <a name="generate-the-openapi-definition"></a>Generera OpenAPI definition

Nu är du redo att generera OpenAPI definition. Den här definitionen kan användas av andra Microsoft-teknik som API Apps [PowerApps](functions-powerapps-scenario.md) och [Microsoft Flow](../azure-functions/app-service-export-api-to-powerapps-and-flow.md), så väl som tredje part utvecklingsverktyg som [Postman](https://www.getpostman.com/docs/importing_swagger) och [många fler paket](http://swagger.io/tools/).

1. Välj endast det *verb* att din API stöder (i det här fallet POST). Detta gör den genererade API-definitionen tydligare.

    1. På den **integrera** för den nya funktionen i HTTP-utlösare, ändra **tillåten HTTP-metoderna** till **valt metoder**

    1. I **valda HTTP-metoderna**, avmarkera alla alternativ utom **efter**.

        ![Den valda http-metoder](media/functions-openapi-definition/selected-http-methods.png)
        
1. Klicka på din app funktionsnamn (som **funktionen demo energi**) > **plattformsfunktioner** > **API-definition**.

    ![API-definition](media/functions-openapi-definition/api-definition.png)

1. På den **API-definition** klickar du på **funktionen**.

    ![Källan för API-definition](media/functions-openapi-definition/api-definition-source.png)

    Det här steget gör det möjligt för en uppsättning OpenAPI alternativ för funktionen appen, inklusive en slutpunkt som värd för en OpenAPI-fil från din funktionsapp domän, en infogad kopia av den [OpenAPI Editor](http://editor.swagger.io), och en API-definition mallen generator.

1. Klicka på **generera API-definition mallen** > **spara**.

    ![Skapa mall för API-definition](media/functions-openapi-definition/generate-template.png)

    Azure söker igenom appen funktionen för HTTP-utlösaren funktioner och använder informationen i functions.json för att generera en OpenAPI definition. Här är definitionen som genereras:

    ```yaml
    swagger: '2.0'
    info:
    title: function-demo-energy.azurewebsites.net
    version: 1.0.0
    host: function-demo-energy.azurewebsites.net
    basePath: /
    schemes:
    - https
    - http
    paths:
    /api/TurbineRepair:
        post:
        operationId: /api/TurbineRepair/post
        produces: []
        consumes: []
        parameters: []
        description: >-
            Replace with Operation Object
            #http://swagger.io/specification/#operationObject
        responses:
            '200':
            description: Success operation
        security:
            - apikeyQuery: []
    definitions: {}
    securityDefinitions:
    apikeyQuery:
        type: apiKey
        name: code
        in: query
    ```

    Den här definitionen beskrivs som en _mallen_ eftersom den kräver mer metadata för att vara en fullständig OpenAPI definition. Du ska ändra definitionen i nästa steg.

## <a name="modify-the-openapi-definition"></a>Ändra OpenAPI-definition
Nu när du har en definition av mallen kan ändra du den för att tillhandahålla ytterligare metadata om API: er åtgärder och datastrukturer. I **API-definition**, ta bort definitionen för genererade från `post` längst ned i definitionen klistra in i innehållet nedan och klickar på **spara**.

```yaml
    post:
      operationId: CalculateCosts
      description: Determines if a technician should be sent for repair
      summary: Calculates costs
      x-ms-summary: Calculates costs
      x-ms-visibility: important
      produces:
        - application/json
      consumes:
        - application/json
      parameters:
        - name: body
          in: body
          description: Hours and capacity used to calculate costs
          x-ms-summary: Hours and capacity
          x-ms-visibility: important
          required: true
          schema:
            type: object
            properties:
              hours:
                description: The amount of effort in hours required to conduct repair
                type: number
                x-ms-summary: Hours
                x-ms-visibility: important
              capacity:
                description: The max output of a turbine in kilowatts
                type: number
                x-ms-summary: Capacity
                x-ms-visibility: important
      responses:
        200:
          description: Message with cost and revenue numbers
          x-ms-summary: Message
          schema:
           type: object
           properties:
            message:
              type: string
              description: Returns Yes or No depending on calculations
              x-ms-summary: Message 
            revenueOpportunity:
              type: string
              description: The revenue opportunity cost
              x-ms-summary: RevenueOpportunity 
            costToFix:
              type: string
              description: The cost in $ to fix the turbine
              x-ms-summary: CostToFix
      security:
        - apikeyQuery: []
definitions: {}
securityDefinitions:
  apikeyQuery:
    type: apiKey
    name: code
    in: query
```

I det här fallet kan du endast klistra in metadata har uppdaterats, men det är viktigt att förstå vilka typer av ändringar som vi gjort standardmallen:

+ Ange att API: et skapar och använder data i en JSON-format.

+ De obligatoriska parametrarna har angetts med sina namn och datatyper.

+ Anges för ett lyckat svar med sina namn och datatyper.

+ Ange egna sammanfattningar och beskrivningar för i API: et och dess åtgärder och parametrar. Detta är viktigt för personer som använder den här funktionen.

+ Lägga till x-ms-sammanfattning och x-ms-synlighet, som används i Användargränssnittet för Microsoft Flow och Logic Apps. Mer information finns i [OpenAPI tillägg för anpassade API: er i Microsoft Flow](https://preview.flow.microsoft.com/documentation/customapi-how-to-swagger/).

> [!NOTE]
> Vi kvar definitionen säkerhet med standardmetoden för autentisering av API-nyckel. Ändrar du det här avsnittet av definitionen om du använder en annan typ av autentisering.

Mer information om hur du definierar-API: et finns i [öppna API-specifikationen](https://swagger.io/specification/#operationObject).

## <a name="test-the-openapi-definition"></a>Testa OpenAPI definition
Innan du använder API-definitionen är en bra idé att testa i Användargränssnittet för Azure-funktioner.

1. På den **hantera** fliken i din funktion under **Värdnycklar**, kopiera den **standard** nyckel.

    ![Kopiera API-nyckel](media/functions-openapi-definition/copy-api-key.png)

    > [!NOTE]
    >Du använder den här nyckeln för testning och du även använda den när du anropar API: et från en app eller tjänst.

1. Gå tillbaka till API-definition: **funktionen demo energi** > **plattformsfunktioner** > **API-definition**.

1. I den högra rutan, klickar du på **ändra autentisering**, ange API-nyckeln som du kopierade och klicka på **autentisera**.

    ![Autentisera med API-nyckel](media/functions-openapi-definition/authenticate-api-key.png)

1. Bläddra nedåt och klicka **försök utföra åtgärden**.

    ![Försök utföra åtgärden](media/functions-openapi-definition/try-operation.png)

1. Ange värden för **timmar** och **kapacitet**.

    ![Ange parametrar](media/functions-openapi-definition/parameters.png)

    Observera hur Användargränssnittet använder beskrivningar av API-definition.

1. Klicka på **skicka en begäran om**, klicka på den **ganska** fliken för att se utdata.

    ![Skicka en begäran](media/functions-openapi-definition/send-request.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en funktion i Azure
> * Generera en OpenAPI definition med hjälp av OpenAPI verktyg
> * Ändra definitionen för att tillhandahålla ytterligare metadata
> * Testa definitionen genom att anropa funktionen

Gå vidare till nästa avsnitt lära dig hur du skapar en PowerApps-app som använder OpenAPI definition som du skapade.
> [!div class="nextstepaction"]
> [Anropa en funktion från PowerApps](functions-powerapps-scenario.md)

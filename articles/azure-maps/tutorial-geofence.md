---
title: 'Självstudiekurs: Skapa en geofence och spåra enheter på en karta | Microsoft Azure Maps'
description: Lär dig hur du konfigurerar en geofence- och spåra enheter i förhållande till geofence med hjälp av Microsoft Azure Maps Spatial Service.
author: philmea
ms.author: philmea
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 126829f12d71e40511c26e781cb191988c1d031e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333869"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Självstudiekurs: Konfigurera en geofence med hjälp av Azure Maps

Den här självstudien vägleder dig igenom de grundläggande stegen för att konfigurera geofence med hjälp av Azure Maps. Tänk på det här scenariot, en konstruktion Site Manager har att övervaka potentiella farliga utrustning. Chefen måste se till att utrustningen stannar i de valda övergripande byggområdena. Detta övergripande byggområde är en hård parameter. Regler kräver att utrustning håller sig inom den här parametern och överträdelser rapporteras till Operations Manager.  

Vi använder API:et för dataöverföring för att lagra en geofence och använda Api:et för Geofence för att kontrollera utrustningens plats i förhållande till geofence. Både API:et för dataöverföring och Geofence-API:et kommer från Azure Maps. Vi använder också Azure Event Grid för att strömma geofence-resultaten och ställa in ett meddelande baserat på geofence-resultaten. Mer information om Event Grid finns i [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

I den här guiden täcker vi hur du:

> [!div class="checklist"]
> * Ladda upp geofence-området i Azure Maps-datatjänsten med hjälp av API:et för datauppladdning.
> *   Konfigurera en Event Grid för att hantera geofence-händelser.
> *   Konfigurera geofence-händelsehanterare.
> *   Konfigurera aviseringar som svar på geofence-händelser med hjälp av Logic Apps.
> *   Använda Azure Maps geofence-tjänst-API:er för att spåra huruvida en byggsresurs befinner sig på byggarbetsplatsen.


## <a name="prerequisites"></a>Krav

### <a name="create-an-azure-maps-account"></a>Skapa ett Azure Maps-konto 

Följ instruktionerna i [Skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps) för att skapa en Azure Maps-kontoprenumeration med S1-prisnivå. Stegen i [hämta primärnyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account) visar hur du hämtar den primära nyckeln för ditt konto. Mer information om autentisering i Azure Maps finns [i hantera autentisering i Azure Maps](./how-to-manage-authentication.md).

## <a name="upload-geofences"></a>Ladda upp geofences

Vi antar att den huvudsakliga geofence är subsite1, som har en inställd utgångstid. Du kan skapa fler kapslade geofences efter behov. Dessa uppsättningar av staket kan användas för att spåra olika byggområden inom det övergripande byggområdet. Underwebbplats1 kan till exempel vara där arbete utförs under vecka 1 till 4 i schemat. subite2 kan vara där arbetet sker under vecka 5 till 7. Alla sådana staket kan läsas in som en enda datauppsättning i början av projektet. Dessa staket används för att spåra regler baserat på tid och rum. 

För att ladda upp geofence för byggarbetsplatsen med hjälp av Data Upload API, använder vi postman ansökan. Installera [postman ansökan](https://www.getpostman.com/) och göra ett gratis konto. 

När Postman-appen har installerats följer du dessa steg för att ladda upp byggwebbplatsens geofence med hjälp av Azure Maps, Data Upload API.

1. Öppna Postman-appen, klicka på New | Create new (Ny | Skapa ny), och välj Request (Begäran). Ange ett namn för Request (Begäran) för Upload geofence data (Ladda upp geofence-data), välj en samling eller en mapp att spara den i och klicka på Save (Spara).

    ![Ladda upp geofences med hjälp av Postman](./media/tutorial-geofence/postman-new.png)

2. Välj POST HTTP-metod på fliken Builder (Byggare) och ange följande URL för att göra en POST-begäran.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    GEOJSON-parametern i URL-sökvägen representerar dataformatet för data som laddas upp.

3. Klicka på **Params** och ange följande nyckel/värde-par som ska användas för POST-begäran-URL. Ersätt {subscription-key} med din Azure Maps-prenumerationsnyckel, även känd som primärnyckel.
   
    ![Parametrar för uppladdningsdata (geofence) i Postman](./media/tutorial-geofence/postman-key-vals.png)

4. Klicka på **Brödtext** och välj sedan råinmatningsformatet och välj JSON som indataformat i listrutan. Ange följande JSON som data som ska laddas upp:

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
              "expiredTime": "2019-01-15T00:00:00",
              "validityPeriod": [
                {
                  "startTime": "2019-01-08T01:00:00",
                  "endTime": "2019-01-08T17:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. Klicka på Send (Skicka) och granska svarsrubriken. När en lyckad begäran har slutförts innehåller **platshuvudet** status-URI:n. Status-URI:n är av följande format. Värdet uploadStatusId är inte mellan { }. Det är vanligt att använda { } för att visa värden som användaren måste ange eller värden som är olika för olika användare.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Kopiera din status-URI och lägg till prenumerationsnyckeln. Status-URI-formatet ska vara som det nedan. Observera att i formatet nedan ändrar du {subscription-key}, inkluderar inte { }, med din prenumerationsnyckel.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Om du `udId`vill hämta den öppnar du en ny flik i Postman-appen och väljer GET HTTP-metoden på fliken Builder. Gör en GET-begäran på status-URI från föregående steg. Om dataöverföringen lyckades får du udId i svarstexten. Kopiera udId för senare användning.

   ```JSON
   {
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udId}?api-version=1.0"
   }
   ```

## <a name="set-up-an-event-handler"></a>Konfigurera en händelsehanterare

I det här avsnittet skapar vi en händelsehanterare som tar emot meddelanden. Den här händelsehanteraren bör meddela Operations Manager om in- och utmatningshändelser för all utrustning.

Vi gör två [Logic Apps-tjänster](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) för att hantera in- och avslutningshändelser. När händelserna i Logic Apps-utlösaren utlöses fler händelser i följd. Tanken är att skicka aviseringar, i det här fallet e-postmeddelanden, till Operations Manager. Följande bild visar skapandet av en logikapp för geofence-inträdeshändelsen. På liknande sätt kan skapa du en till för utträdeshändelsen. Mer information finns i avsnittet om [händelsehanterare som stöds](https://docs.microsoft.com/azure/event-grid/event-handlers).

1. Skapa en logikapp i Azure-portalen. Välj Logic App på Azure Marketplace. Välj sedan knappen **Skapa.**

   ![Skapa Azure Logic Apps för att hantera geofence-händelser](./media/tutorial-geofence/logic-app.png)

2. På inställningsmenyn för Logic App navigerar du till **Logic App Designer**

3. Välj en HTTP-begärandeutlösare och välj sedan "Nytt steg". I Outlook-kopplingen väljer du "skicka ett e-postmeddelande" som en åtgärd
  
   ![Logic Apps-schema](./media/tutorial-geofence/logic-app-schema.png)

4. Fyll i fälten för att skicka ett e-postmeddelande. Lämna HTTP-URL, kommer det automatiskt att generera när du klickar på "spara"

   ![Generera en slutpunkt för Logic Apps](./media/tutorial-geofence/logic-app-endpoint.png)

5. Spara logikappen för att generera HTTP-URL-slutpunkten och kopiera HTTP-URL:en.

## <a name="create-an-azure-maps-events-subscription"></a>Skapa en prenumeration för Azure Maps-händelser

Azure Maps har stöd för tre händelsetyper. Du kan ta en titt på händelsetyper som Azure Maps stöder [här](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps). Vi behöver två olika händelseprenumerationer, en för enter-händelsen och en för avslutningshändelserna.

Följ stegen nedan för att skapa en händelseprenumeration för geofence-inträdeshändelserna. Du kan prenumerera på geofence-utträdeshändelser på ett liknande sätt.

1. Navigera till ditt Azure Maps-konto. Välj Prenumerationer på instrumentpanelen. Klicka på ditt prenumerationsnamn och välj **händelser** på inställningsmenyn.

   ![Navigera till Azure Maps-kontohändelser](./media/tutorial-geofence/events-tab.png)

2. För att skapa en händelseprenumeration väljer du Event Subscription (Händelseprenumeration) på sidan Events (Händelser).

   ![Skapa en prenumeration för Azure Maps-händelser](./media/tutorial-geofence/create-event-subscription.png)

3. Namnge händelseprenumerationen och prenumerera på händelsetypen Enter (Inträde). Välj nu Webbkrok som "Slutpunktstyp". Klicka på "Välj en slutpunkt" och kopiera http-URL-slutpunkten för Logikapp till {Endpoint}"

   ![Prenumerationsinformation för Azure Maps Events](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Använda geofence-API

Du kan använda Geofence API för att kontrollera om en **enhet**, i det här fallet utrustning, är inuti eller utanför en geofence. Låter fråga Geofence GET API mot olika platser, där en viss utrustning har flyttats med tiden. Följande figur illustrerar fem platser med fem anläggningsmaskiner. 

> [!Note]
> Scenariot och beteendet baseras på samma **enhets-ID** så att det återspeglar de fem olika platserna som i figuren nedan.

"deviceId" är ett unikt ID som du tillhandahåller för din enhet i GET-begäran, när du frågar efter dess plats. När du gör en asynkron begäran till **sökgeofence - GET API**hjälper "deviceId" till att publicera geofence-händelser för den enheten, i förhållande till den angivna geofence. I den här guiden har vi gjort asynkrona begäranden till API:et med en unik "deviceId". Begäranden i handledningen görs i kronologisk ordning, som i diagrammet. Egenskapen "isEventPublished" i svaret publiceras när en enhet går in i eller avslutar geofence. Du behöver inte registrera en enhet för att följa den här självstudien.

Låt oss titta tillbaka på diagrammet. Var och en av dessa fem platser används för utvärdering av ändringar i geofence inträdes- och utträdesstatus mot gränsen. Om det sker en tillståndsändring utlöser geofence-tjänsten en händelse som skickas till logikappen av Event Grid. Som ett resultat kommer åtgärdens chef att få motsvarande enter eller exit meddelande via ett e-postmeddelande.

![Geofence Karta i Azure Maps](./media/tutorial-geofence/geofence.png)

I Postman-appen öppnar du en ny flik i samma samling som du skapade ovan. Välj GET HTTP-metoden på fliken Builder (Byggare):

Följande är fem HTTP GET Geofencing API-begäranden, med olika platskoordinater för utrustningen. Koordinaterna är som observerats i kronologisk ordning. Varje begäran åtföljs av svarstexten.
 
1. Plats 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Geofence-fråga 1](./media/tutorial-geofence/geofence-query1.png)

   I svaret ovan innebär det negativa avståndet från huvudgelofensen att utrustningen är inne i geofence. Det positiva avståndet från underwebbplatsen innebär att utrustningen ligger utanför underwebbplatsens geofence. 

2. Plats 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Geofence-fråga 2](./media/tutorial-geofence/geofence-query2.png)

   Om man tittar på föregående JSON svar noggrant utrustningen är utanför underwebbplatsen, men det är innanför huvudstaket. Ingen händelse utlöses och ingen e-post skickas.

3. Plats 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geofence-fråga 3](./media/tutorial-geofence/geofence-query3.png)

   En tillståndsändring har inträffat och är utrustningen innanför både huvudplatsen och delplatsens geofences. Den här ändringen medför att en händelse publiceras och ett e-postmeddelande skickas till Operations Manager.

4. Plats 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Geofence-fråga 4](./media/tutorial-geofence/geofence-query4.png)

   Genom att undersöka motsvarande svar noggrant kan du se att det inte publiceras någon händelse i det här fallet trots att utrustningen har utträtt från delplatsens geofence. Om du tittar på användarens angivna tid i GET-begäran kan du se att underwebbplatsgeofence har upphört att gälla för den här gången. Utrustningen är fortfarande i huvudgelofens. Du kan även se geometri-ID för delplatsens geofence under `expiredGeofenceGeometryId` i svarstexten.


5. Plats 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geofence-fråga 5](./media/tutorial-geofence/geofence-query5.png)

   Du kan se att utrustningen har lämnat den huvudsakliga byggarbetsplatsens geofence. En händelse publiceras och ett e-postmeddelande skickas till Operations Manager.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig: hur du konfigurerar geofence genom att ladda upp den i Azure Maps and Data-tjänsten med hjälp av API:et för dataöverföring. Du har även lärt dig använda Azure Maps Event Grid för att prenumerera på och hantera geofence-händelser. 

* Se avsnittet om att [hantera innehållstyper i Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type) om du vill lära dig att använda Logic Apps till att parsa JSON för att skapa en mer komplex logik.
* Mer information om händelsehanterare i Event Grid finns i avsnittet om [händelsehanterare som stöds i Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers).

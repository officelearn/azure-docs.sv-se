---
title: Skapa en geofence med hjälp av Azure Maps | Microsoft Docs
description: Konfigurera en geofence med hjälp av Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 55dc0fa31398bcc04d9793c8cffc9258dc29e4c7
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244461"
---
# <a name="set-up-a-geofence-by-using-azure-maps"></a>Konfigurera en geofence med hjälp av Azure Maps

Den här självstudien vägleder dig igenom de grundläggande stegen för att konfigurera geofence med hjälp av Azure Maps. Det scenario som vi behandlar i den här självstudien är till för att hjälpa byggnadschefer att övervaka potentiellt farlig utrustning som flyttas utanför angivna byggområden. På byggarbetsplatser finns det dyr utrustning och förordningar som måste följas. Det kräver normalt att utrustningen förvaras på byggarbetsplatsen och inte förs bort utan tillstånd.

Vi använder uppladdnings-API:et för Azure Maps Data för att lagra en geofence och Geofence-API för Azure Maps för att kontrollera utrustningens plats relativt till geofence. Vi använder Azure Event Grid för att strömma geofence-resultatet och konfigurera ett meddelande baserat på geofence-resultatet.
Mer information om Event Grid finns i [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).
I den här självstudien lär du dig att:

> [!div class="checklist"]
* Ladda upp geofence-området i Azure Maps-datatjänsten med hjälp av API:et för datauppladdning.
*   Konfigurera en Event Grid för att hantera geofence-händelser.
*   Konfigurera geofence-händelsehanterare.
*   Konfigurera aviseringar som svar på geofence-händelser med hjälp av Logic Apps.
*   Använda Azure Maps geofence-tjänst-API:er för att spåra huruvida en byggsresurs befinner sig på byggarbetsplatsen.


## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="create-an-azure-maps-account"></a>Skapa ett Azure Maps-konto 

För att slutföra stegen i den här självstudien behöver du först läsa [hantera konton och nycklar](how-to-manage-account-keys.md) för att skapar och hantera kontoprenumerationen med prisnivån S1.

## <a name="upload-geofences"></a>Ladda upp geofences

Vi använder postman-programmet för att ladda upp geofence för byggarbetsplatsen med hjälp av API:et för datauppladdning. För den här självstudien förutsätter vi att det finns en övergripande byggarbetsplats som utgör en bestämd parameter som byggutrustningen inte ska bryta mot. Överträdelse mot den här gränsen är allvarliga och rapporteras till driftschefen. En optimal uppsättning med ytterligare gränser kan användas för att spåra olika byggområden inom den övergripande byggarbetsplatsen enligt schema. Vi kan anta att den huvudsakliga geofence har en subsite1 (delplats 1) som har en angiven förfallotid och upphör efter den tiden. Du kan skapa fler kapslade geofences efter behov. Till exempel kan subsite1 vara den plats där arbetet sker under vecka 1 till 4 på schemat och subsite2 den plats där arbetet sker under vecka 5 till 7. Alla sådana gränser kan läsas in som en enda datamängd i början av projektet och användas för att spåra regler baserat på tid och rum. Mer information om geofence-dataformat finns i [Geofence GeoJSON-data](https://docs.microsoft.com/azure/azure-maps/geofence-geojson). Mer information om att ladda upp data till Azure Maps-tjänsten finns i [dokumentationen om API:et för datauppladdning](https://docs.microsoft.com/rest/api/maps/data/uploadpreview).

Öppna Postman-appen och följa anvisningarna nedan för att ladda upp byggarbetsplatsens geofence med hjälp av API:et för datauppladdning för Azure Maps.

1. Öppna Postman-appen, klicka på New | Create new (Ny | Skapa ny), och välj Request (Begäran). Ange ett namn för Request (Begäran) för Upload geofence data (Ladda upp geofence-data), välj en samling eller en mapp att spara den i och klicka på Save (Spara).

    ![Ladda upp geofences med hjälp av Postman](./media/tutorial-geofence/postman-new.png)

2. Välj POST HTTP-metod på fliken Builder (Byggare) och ange följande URL för att göra en POST-begäran.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    GEOJSON-parametern i URL-sökvägen representerar dataformatet för data som laddas upp.

3. Klicka på **Params** och ange följande nyckel/värde-par som ska användas för POST-begäran-URL. Ersätt värdet för prenumerationsnyckel med din Azure Maps-prenumerationsnyckel.
   
    ![Key-Value-parametrar för Postman](./media/tutorial-geofence/postman-key-vals.png)

4. Klicka på **Body** (Brödtext), välj raw-indataformatet och välj JSON som indataformat i den nedrullningsbara listan. Ange följande JSON som data som ska laddas upp:

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

5. Klicka på Send (Skicka) och granska svarsrubriken. Platsrubriken innehåller URI för åtkomst eller nedladdning av data för framtida användning. Den innehåller även ett unikt `udId` för uppladdade data.

  ```HTTP
  https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
  ```

## <a name="set-up-an-event-handler"></a>Konfigurera en händelsehanterare

För att meddela driftschefen om inträdes- och utträdeshändelser bör vi skapa en händelsehanterare som tar emot meddelandena.

Vi skapar två [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps)-tjänster för att hantera inträdes- och utträdeshändelser. Vi skapar även händelseutlösare i logikapparna som utlöses av dessa händelser. Tanken är att skicka aviseringar, i det här fallet e-postmeddelanden till driftschefen vid inträde av utrustning till eller utträde från byggarbetsplatsen. Följande bild visar skapandet av en logikapp för geofence-inträdeshändelsen. På liknande sätt kan skapa du en till för utträdeshändelsen.
Mer information finns i avsnittet om [händelsehanterare som stöds](https://docs.microsoft.com/azure/event-grid/event-handlers).

1. Skapa en logikapp i Azure-portalen

  ![skapa logikappar](./media/tutorial-geofence/logic-app.png)

2. Välj en HTTP-begärandeutlösare och välj sedan ”Send an email” (Skicka ett e-postmeddelande) som en åtgärd i Outlook-anslutningsappen
  
  ![Logic Apps-schema](./media/tutorial-geofence/logic-app-schema.png)

3. Spara logikappen för att generera HTTP-URL-slutpunkten och kopiera HTTP-URL:en.

  ![Logic Apps-slutpunkt](./media/tutorial-geofence/logic-app-endpoint.png)


## <a name="create-an-azure-maps-events-subscription"></a>Skapa en prenumeration för Azure Maps-händelser

Azure Maps har stöd för tre händelsetyper. Du kan ta en titt på händelsetyper som Azure Maps stöder [här](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps). Vi skapar två olika prenumerationer, en för inträdeshändelser och en annan för utträdeshändelser.

Följ stegen nedan för att skapa en händelseprenumeration för geofence-inträdeshändelserna. Du kan prenumerera på geofence-utträdeshändelser på ett liknande sätt.

1. Gå till ditt Azure Maps-konto via [den här portallänken](https://ms.portal.azure.com/#@microsoft.onmicrosoft.com/dashboard/) och välj fliken Events (Händelser).

   ![Azure Maps-händelser](./media/tutorial-geofence/events-tab.png)

2. För att skapa en händelseprenumeration väljer du Event Subscription (Händelseprenumeration) på sidan Events (Händelser).

   ![Prenumeration för Azure Maps-händelser](./media/tutorial-geofence/create-event-subscription.png)

3. Namnge händelseprenumerationen och prenumerera på händelsetypen Enter (Inträde). Välj nu Web Hook som ”Endpoint Type” (Slutpunktstyp) och kopiera din logikapps HTTP-URL-slutpunkt till ”Endpoint” (Slutpunkt)

   ![Händelseprenumeration](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Använda geofence-API

Du kan använda geofence-API:et för att kontrollera huruvida en **device** (enhet) (utrustning som är en del av status) är innanför eller utanför en geofence. För att få en bättre förståelse för geofence använder du GET-API:et. Vi kör frågor mot den för olika platser där viss utrustning har flyttats över tid. Följande bild visar fem platser för viss byggutrustning med ett unikt **enhets-ID** enligt observation i kronologisk ordning. Var och en av dessa fem platser används för utvärdering av ändringar i geofence inträdes- och utträdesstatus mot gränsen. Om det sker en tillståndsändring utlöser geofence-tjänsten en händelse som skickas till logikappen av Event Grid. Därmed får driftschefen motsvarande inträdes- eller utträdesmeddelande via e-post.

> [!Note]
> Ovanstående scenario och beteende baseras på samma **enhets-ID** så att det speglar de fem olika platserna som på bilden nedan.

![Geofence-karta](./media/tutorial-geofence/geofence.png)

I Postman-appen öppnar du en ny flik i samma samling som du skapade ovan. Välj GET HTTP-metoden på fliken Builder (Byggare):

Följande är fem HTTP GET Geofencing API-begäranden med olika motsvarande platskoordinater för utrustningen enligt observation i kronologisk ordning. Varje begäran åtföljs av svarstexten.
 
1. Plats 1:
    
  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```
  ![Geofence-fråga 1](./media/tutorial-geofence/geofence-query1.png)

  Som du ser i frågan nedan innebär det negativa avståndet från den huvudsakliga geofence att utrustningen är innanför geofence, och det positiva avståndet från delplatsens geofence innebär att den är utanför delplatsens geofence. 

2. Plats 2: 
   
  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```
    
  ![Geofence-fråga 2](./media/tutorial-geofence/geofence-query2.png)

  Om du tittar noggrant på det föregående JSON-svaret ser du att utrustningen befinner sig utanför delplatsen men innanför den huvudsakliga gränsen. Det utlöser inte någon händelse, och ingen e-post skickas.

3. Plats 3: 
  
  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```

  ![Geofence-fråga 3](./media/tutorial-geofence/geofence-query3.png)

  En tillståndsändring har inträffat och är utrustningen innanför både huvudplatsen och delplatsens geofences. Detta publicerar en händelse och ett e-postmeddelandet skickas till driftschefen.

4. Plats 4: 

  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```
  
  ![Geofence-fråga 4](./media/tutorial-geofence/geofence-query4.png)

   Genom att undersöka motsvarande svar noggrant kan du se att det inte publiceras någon händelse i det här fallet trots att utrustningen har utträtt från delplatsens geofence. Om du tittar på användarens angivna tid i GET-begäran ser du att delplatsens geofence har upphört i förhållande till den här tiden, och utrustningen befinner sig fortfarande i den huvudsakliga geofence. Du kan även se geometri-ID för delplatsens geofence under `expiredGeofenceGeometryId` i svarstexten.


5. Plats 5:
      
  ```HTTP
  https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
  ```

  ![Geofence-fråga 5](./media/tutorial-geofence/geofence-query5.png)

  Du kan se att utrustningen har lämnat den huvudsakliga byggarbetsplatsens geofence. En händelse publiceras. Det är en allvarlig överträdelse och ett viktigt e-postmeddelande skickas till driftschefen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig att konfigurera en geofence genom att ladda upp den till Azure Maps-datatjänsten med hjälp av API:et för datauppladdning. Du har även lärt dig använda Azure Maps Event Grid för att prenumerera på och hantera geofence-händelser. 

* Se avsnittet om att [hantera innehållstyper i Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type) om du vill lära dig att använda Logic Apps till att parsa JSON för att skapa en mer komplex logik.
* Mer information om händelsehanterare i Event Grid finns i avsnittet om [händelsehanterare som stöds i Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers).

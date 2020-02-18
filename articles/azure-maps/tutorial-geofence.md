---
title: 'Självstudie: skapa en inhägnad och spåra enheter på en karta | Microsoft Azure Maps'
description: Lär dig hur du ställer in en avgränsning och spårar enheter i förhållande till den gräns som används för Microsoft Azure Maps-spatialdata.
author: farah-alyasari
ms.author: v-faalya
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e0e918a2508bb65de176b030a73598e221aff676
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2020
ms.locfileid: "77370150"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Självstudie: Konfigurera ett geografiskt avgränsnings tecken genom att använda Azure Maps

Den här självstudien vägleder dig igenom de grundläggande stegen för att konfigurera geofence med hjälp av Azure Maps. Överväg det här scenariot, en konstruktions Site Manager måste övervaka potentiell farligt utrustning. Chefen måste se till att utrustningen ligger inom de valda övergripande Bygg områdena. Detta övergripande konstruktions områden är en hård parameter. Regler som kräver att utrustningen hålls kvar i den här parametern och överträdelser rapporteras till Operations Manager.  

Vi använder API: et för data uppladdning för att lagra ett geografiskt område och använda API: et för att kontrol lera utrustnings platsen i förhållande till den här gränsen. Både data överförings-API: et och API: et för polystängsel är från Azure Maps. Vi använder också Azure Event Grid för att strömma de här resultaten och ställa in ett meddelande baserat på de gränser som uppstår. Mer information om Event Grid finns i [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

I den här självstudien lär vi dig att:

> [!div class="checklist"]
> * Ladda upp geofence-området i Azure Maps-datatjänsten med hjälp av API:et för datauppladdning.
> *   Konfigurera en Event Grid för att hantera geofence-händelser.
> *   Konfigurera geofence-händelsehanterare.
> *   Konfigurera aviseringar som svar på geofence-händelser med hjälp av Logic Apps.
> *   Använda Azure Maps geofence-tjänst-API:er för att spåra huruvida en byggsresurs befinner sig på byggarbetsplatsen.


## <a name="prerequisites"></a>Förutsättningar

### <a name="create-an-azure-maps-account"></a>Skapa ett Azure Maps-konto 

Följ instruktionerna i [skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps) om du vill skapa en Azure Maps konto prenumeration med pris nivån S1. Stegen i [Hämta primär nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account) visar hur du hämtar primär nyckeln för ditt konto. Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](./how-to-manage-authentication.md).

## <a name="upload-geofences"></a>Ladda upp geofences

Vi förutsätter att huvud gränsen är subsite1, som har en angiven förfallo tid. Du kan skapa fler kapslade geofences efter behov. Dessa uppsättningar med avgränsningar kan användas för att spåra olika konstruktions områden inom det övergripande arbets området. Subsite1 kan till exempel vara den plats där arbetet sker under vecka 1 till 4 i schemat. subsite2 kan vara där arbetet äger rum under veckan 5 till 7. Alla sådana avgränsningar kan läsas in som en enda data uppsättning i början av projektet. Dessa avgränsningar används för att spåra regler baserade på tid och utrymme. 

För att överföra den här arbets platsens gräns med hjälp av API: et för data överföring använder vi Postman-programmet. Installera [Postman-programmet](https://www.getpostman.com/) och gör ett kostnads fritt konto. 

När Postman-appen har installerats följer du de här stegen för att ladda upp arbets platsens gränser med hjälp av Azure Maps, API för data uppladdning.

1. Öppna Postman-appen, klicka på New | Create new (Ny | Skapa ny), och välj Request (Begäran). Ange ett namn för Request (Begäran) för Upload geofence data (Ladda upp geofence-data), välj en samling eller en mapp att spara den i och klicka på Save (Spara).

    ![Ladda upp geofences med hjälp av Postman](./media/tutorial-geofence/postman-new.png)

2. Välj POST HTTP-metod på fliken Builder (Byggare) och ange följande URL för att göra en POST-begäran.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    GEOJSON-parametern i URL-sökvägen representerar dataformatet för data som laddas upp.

3. Klicka på **Params** och ange följande nyckel/värde-par som ska användas för POST-begäran-URL. Ersätt {Subscription-Key} med din Azure Maps prenumerations nyckel, även kallat primär nyckel.
   
    ![Parametrar för överföring av data (avgränsning) i Postman](./media/tutorial-geofence/postman-key-vals.png)

4. Klicka på **brödtext** och välj sedan formatet RAW-indata och välj JSON som indataformat i list rutan. Ange följande JSON som data som ska laddas upp:

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

5. Klicka på Send (Skicka) och granska svarsrubriken. Vid en lyckad begäran kommer **plats** rubriken innehålla status-URI. Status-URI har följande format. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Kopiera din status-URI och Lägg till prenumerations nyckeln. Status-URI-formatet bör vara det som anges nedan. Observera att i formatet nedan skulle du ändra {Subscription-Key}, inklusive {}, med din prenumerations nyckel.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Om du vill hämta `udId`öppnar du en ny flik i Postman-appen och väljer Hämta HTTP-metod på fliken Builder. gör en GET-begäran i status-URI: n från föregående steg. Om din data uppladdning lyckades får du udId i svars texten. Kopiera udId för senare användning.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```

## <a name="set-up-an-event-handler"></a>Konfigurera en händelsehanterare

I det här avsnittet skapar vi en händelse hanterare som tar emot meddelanden. Den här händelse hanteraren bör meddela Operations Manager om att ange och avsluta händelser för all utrustning.

Vi gör två [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) -tjänster för att hantera in-och avsluts händelser. När händelserna i Logic Apps utlösare utlöses fler händelser i följd. Tanken är att skicka aviseringar, i det här fallet e-postmeddelanden, till Operations Manager. Följande bild visar skapandet av en logikapp för geofence-inträdeshändelsen. På liknande sätt kan skapa du en till för utträdeshändelsen. Mer information finns i avsnittet om [händelsehanterare som stöds](https://docs.microsoft.com/azure/event-grid/event-handlers).

1. Skapa en Logic-app i Azure Portal. Välj Logic app i Azure Marketplace. Välj sedan knappen **skapa** .

   ![Skapa Azure Logic Apps för att hantera polystängsel-händelser](./media/tutorial-geofence/logic-app.png)

2. På menyn Inställningar för Logic app går du till **Logic Apps designer**

3. Välj en utlösare för HTTP-begäran och välj sedan "nytt steg". I Outlook Connector väljer du "Skicka ett e-postmeddelande" som en åtgärd
  
   ![Logic Apps-schema](./media/tutorial-geofence/logic-app-schema.png)

4. Fyll i fälten för att skicka ett e-postmeddelande. Lämna HTTP-URL: en genereras automatiskt efter att du har klickat på Spara

   ![Generera en Logic Apps-slutpunkt](./media/tutorial-geofence/logic-app-endpoint.png)

5. Spara logikappen för att generera HTTP-URL-slutpunkten och kopiera HTTP-URL:en.

## <a name="create-an-azure-maps-events-subscription"></a>Skapa en prenumeration för Azure Maps-händelser

Azure Maps har stöd för tre händelsetyper. Du kan ta en titt på händelsetyper som Azure Maps stöder [här](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps). Vi behöver två olika händelse prenumerationer, en för händelsen ange och en för avsluts händelser.

Följ stegen nedan för att skapa en händelseprenumeration för geofence-inträdeshändelserna. Du kan prenumerera på geofence-utträdeshändelser på ett liknande sätt.

1. Navigera till ditt Azure Maps-konto. I instrument panelen väljer du prenumerationer. Klicka på ditt prenumerations namn och välj **händelser** på menyn Inställningar.

   ![Navigera till Azure Maps konto händelser](./media/tutorial-geofence/events-tab.png)

2. För att skapa en händelseprenumeration väljer du Event Subscription (Händelseprenumeration) på sidan Events (Händelser).

   ![Skapa en prenumeration för Azure Maps-händelser](./media/tutorial-geofence/create-event-subscription.png)

3. Namnge händelseprenumerationen och prenumerera på händelsetypen Enter (Inträde). Välj nu Web Hook som "slut punkts typ". Klicka på "Välj en slut punkt" och kopiera din Logic app HTTP URL-slutpunkt till {Endpoint}

   ![Prenumerations information för Azure Maps händelser](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Använda geofence-API

Du kan använda API: et för inre gränser för att kontrol lera om en **enhet**, i det här fallet utrustning, ligger innanför eller utanför en inre gräns. Tillåter frågor till API: et för att hämta API mot olika platser, där en viss utrustning har flyttats över tid. Följande bild illustrerar fem platser med fem konstruktions utrustning. 

> [!Note]
> Scenariot och beteendet baseras på samma **enhets-ID** så att det motsvarar de fem olika platserna som visas i bilden nedan.

"DeviceId" är ett unikt ID som du anger för enheten i GET-begäran när du frågar efter dess plats. När du gör en asynkron begäran till **Search-API: et för att hämta API**, hjälper "deviceId" i att publicera kors avgränsnings händelser för den enheten, i förhållande till det angivna avgränsnings värdet. I den här självstudien har vi gjort asynkrona förfrågningar till API: et med en unik "deviceId". Förfrågningarna i självstudien görs i kronologisk ordning, som i diagrammet. Egenskapen "isEventPublished" i svaret publiceras varje gång som en enhet går in eller avslutar avgränsningen. Du behöver inte registrera en enhet för att följa den här självstudien.

Gör att du ser tillbaka i diagrammet. Var och en av dessa fem platser används för utvärdering av ändringar i geofence inträdes- och utträdesstatus mot gränsen. Om det sker en tillståndsändring utlöser geofence-tjänsten en händelse som skickas till logikappen av Event Grid. Det innebär att åtgärds chefen får motsvarande RETUR-eller avslutnings meddelande via ett e-postmeddelande.

![Gräns karta i Azure Maps](./media/tutorial-geofence/geofence.png)

I Postman-appen öppnar du en ny flik i samma samling som du skapade ovan. Välj GET HTTP-metoden på fliken Builder (Byggare):

Följande är fem HTTP Hämta API-begäranden för polystaket, med olika plats koordinater för utrustningen. Koordinaterna är som observerade i kronologisk ordning. Varje begäran åtföljs av svarstexten.
 
1. Plats 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Geofence-fråga 1](./media/tutorial-geofence/geofence-query1.png)

   I svaret ovan innebär det negativa avståndet från huvud gränsen att utrustningen ligger innanför det inre avgränsnings gränsen. Det positiva avståndet från under platsens yttre gräns innebär att utrustningen ligger utanför under platsens yttre gräns. 

2. Plats 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Geofence-fråga 2](./media/tutorial-geofence/geofence-query2.png)

   Om du tittar på föregående JSON-svar noggrant är utrustningen utanför under platsen, men är inuti huvud gränsen. Ingen händelse utlöses och inget e-postmeddelande skickas.

3. Plats 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geofence-fråga 3](./media/tutorial-geofence/geofence-query3.png)

   En tillståndsändring har inträffat och är utrustningen innanför både huvudplatsen och delplatsens geofences. Den här ändringen innebär att en händelse publiceras och att ett e-postmeddelande skickas till Operations Manager.

4. Plats 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Geofence-fråga 4](./media/tutorial-geofence/geofence-query4.png)

   Genom att undersöka motsvarande svar noggrant kan du se att det inte publiceras någon händelse i det här fallet trots att utrustningen har utträtt från delplatsens geofence. Om du tittar på användarens angivna tid i GET-begäran kan du se att under platsens tids gräns har upphört att gälla för den här tiden. Utrustningen är fortfarande i huvudets inhägnad. Du kan även se geometri-ID för delplatsens geofence under `expiredGeofenceGeometryId` i svarstexten.


5. Plats 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geofence-fråga 5](./media/tutorial-geofence/geofence-query5.png)

   Du kan se att utrustningen har lämnat den huvudsakliga byggarbetsplatsens geofence. En händelse publiceras och en e-postavisering skickas till Operations Manager.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig: hur du ställer in en avgränsning genom att ladda upp det i Azure Maps-och data tjänsten med hjälp av API: et för data överföring. Du har även lärt dig använda Azure Maps Event Grid för att prenumerera på och hantera geofence-händelser. 

* Se avsnittet om att [hantera innehållstyper i Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type) om du vill lära dig att använda Logic Apps till att parsa JSON för att skapa en mer komplex logik.
* Mer information om händelsehanterare i Event Grid finns i avsnittet om [händelsehanterare som stöds i Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers).

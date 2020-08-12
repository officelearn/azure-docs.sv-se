---
title: 'Självstudie: skapa en inhägnad och spåra enheter på en Microsoft Azure karta'
description: Lär dig hur du konfigurerar ett geografiskt avgränsnings tecken. Se hur du spårar enheter i förhållande till den här gränsen genom att använda tjänsten Azure Maps spatial.
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/11/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: b88d9132ec1548c9d94fc418af35b55ac2836e96
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121246"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Självstudie: Konfigurera ett geofence med hjälp av Azure Maps

I den här självstudien får du lära dig grunderna i hur du skapar och använder Azure Mapsbaserade tjänster för att skapa och använda tjänster i följande scenarion:

*En konstruktions Site Manager måste spåra utrustning när den går in och lämnar ett byggnads områdets perimeter. När en del av utrustningen avslutar eller anger dessa perimeter skickas ett e-postmeddelande till Operations Manager.*

Azure Maps tillhandahåller ett antal tjänster för att stödja spårning av utrustning som går in och avslutar arbets ytan i scenariot ovan. I den här självstudien lär vi dig att:

> [!div class="checklist"]
> * Överför [polystaket av POLYjson-data](geofence-geojson.md) som definierar de konstruktions plats områden som vi vill övervaka. Vi använder [API: et för data överföring](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) för att överföra avgränsningar som polygon-koordinater till ditt Azure Maps-konto.
> * Konfigurera två [Logic-appar](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps) som, när de utlöses, kommer att skicka e-postaviseringar till bygg platsen Operations Manager när utrustningen går in och avslutar området för avgränsning.
> * Använd [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) om du vill prenumerera på Azure Mapsde gränser för att ange och avsluta en händelse. Vi kommer att konfigurera två webb-Hook-händelseloggar som anropar de HTTP-slutpunkter som definierats i dina två Logic Apps. Logic Apps skickar sedan lämpliga e-postaviseringar om utrustning som flyttas utanför eller genom att ange ett geografiskt avgränsnings tecken.
> * Använd [Sök funktionen för att hämta API: er](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) för att ta emot meddelanden när en del av utrustningen avslutas och anger de områden som ligger utanför gränsen.

## <a name="prerequisites"></a>Förutsättningar

1. [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel.

I den här självstudien används [Postman](https://www.postman.com/) -programmet, men du kan välja en annan API utvecklings miljö.

## <a name="upload-geofencing-geojson-data"></a>Överför polystaket, polyjson-data

I den här självstudien laddar vi upp polyinhägnaden av polyjson-data som innehåller en `FeatureCollection` . `FeatureCollection`Innehåller två gränser som definierar polygonens avgränsnings områden på bygg platsen. Den första gränsen har ingen förfallo tid eller begränsningar. Den andra kan bara frågas mot under kontors tid (9-5 P.M. PST) och kommer inte längre att vara giltigt efter den 1 januari 2022. Mer information om formatet för det här formatet finns i [polyjson-data (polystaket](geofence-geojson.md)).

>[!TIP]
>Du kan när som helst uppdatera dina avgränsnings data. Mer information om hur du uppdaterar dina data finns i [API för data överföring](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)

1. Öppna Postman-appen. Längst upp i Postman-appen väljer du **nytt**. I fönstret **Skapa nytt** väljer du **samling**.  Namnge samlingen och välj knappen **skapa** .

2. Välj **nytt** om du vill skapa en begäran. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn** för begäran. Välj den samling som du skapade i föregående steg och välj sedan **Spara**.

3. Välj metoden **post** http på fliken Builder och ange följande URL för att överföra polystaket-data till Azure Maps-tjänsten. För den här begäran och andra begär Anden som nämns i den här artikeln ersätter `{Azure-Maps-Primary-Subscription-key}` du med den primära prenumerations nyckeln.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    Den _interjson_ -parametern i URL-sökvägen representerar data formatet för de data som överförs.

4. Klicka på fliken **brödtext** . Välj **RAW**och sedan **JSON** som indataformat. Kopiera och klistra in följande interjson-data i **bröd** text området:

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
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
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

5. Klicka på knappen blå **sändning** och vänta tills begäran har bearbetats. När begäran har slutförts går du till fliken **sidhuvud** i svaret. Kopiera värdet för **plats** nyckeln, som är `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Om du vill kontrol lera status för API-anropet skapar du en **Get** http-begäran på `status URL` . Du måste lägga till din primära prenumerations nyckel till URL: en för autentisering. **Get** -begäran bör likna följande URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. När **Get** http-begäran har slutförts returneras en `resourceLocation` . `resourceLocation`Innehåller det unika `udid` för det överförda innehållet. Du måste spara detta `udid` för att fråga The get unstängsel API i det sista avsnittet i den här självstudien. Du kan också använda `resourceLocation` URL: en för att hämta metadata från den här resursen i nästa steg.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

8. Om du vill hämta metadata för innehåll skapar **du en http** -begäran på den `resourceLocation` URL som hämtades i steg 7. Se till att lägga till din primära prenumerations nyckel till URL: en för autentisering. **Get** -begäran bör likna följande URL:

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. När **Get** http-begäran har slutförts innehåller svars texten som `udid` anges i i `resourceLocation` steg 7, platsen för att komma åt/Ladda ned innehållet i framtiden och några andra metadata om innehållet som skapat/uppdaterat datum, storlek och så vidare. Ett exempel på det övergripande svaret är:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "7/15/2020 6:11:43 PM +00:00",
        "updated": "7/15/2020 6:11:45 PM +00:00",
        "sizeInBytes": 1962,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-logic-app-workflows"></a>Skapa Logic app-arbetsflöden

I det här avsnittet ska vi skapa två [Logic app](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps) -slutpunkter som utlöser ett e-postmeddelande. Vi visar dig hur du skapar den första utlösaren som kommer att skicka e-postmeddelanden när dess slut punkt anropas.

1. Logga in på [Azure-portalen](https://portal.azure.com)

2. Klicka på **Skapa en resurs** längst upp till vänster i [Azure Portal](https://portal.azure.com).

3. Skriv **Logic app**i rutan *Sök på Marketplace* .

4. Välj **Logic app**i *resultaten*. Klicka på knappen **skapa** .

5. På sidan **Logic app** anger du följande värden:
    * Den *prenumeration* som du vill använda för den här Logic-appen.
    * *Resurs grupps* namnet för den här Logic-appen. Du kan välja att *skapa ny* eller *använda befintlig* resursgrupp.
    * *Logic App-namnet* för din Logi Kap par. I det här fallet ska vi använda `Equipment-Enter` som namn.

    I den här självstudien behåller du resten av värdena i standardinställningarna.

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="Skapa en logikapp":::

6. Klicka på knappen **Granska + skapa** . Granska inställningarna och klicka på **skapa** för att skicka distribution. När distributionen har slutförts klickar **du på gå till resurs**. Du kommer till **Logic Apps designer**

7. Nu ska vi välja en utlösnings typ. Rulla ned en bit till avsnittet *börja med en vanlig utlösare**. Klicka på **när en HTTP-begäran tas emot**.

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="Skapa en HTTP-utlösare för Logic app":::

8. Klicka på **Spara** i det övre högra hörnet i designern. **Http post-URL: en** kommer att genereras automatiskt. Spara URL: en som du behöver i nästa avsnitt för att skapa en händelse slut punkt.

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="URL för HTTP-begäran för Logic app och JSON":::

9. Välj **+ nytt steg**. Nu ska vi välja en åtgärd. Skriv `outlook.com email` i sökrutan. Rulla ned i listan **åtgärder** och klicka på **Skicka ett e-postmeddelande (v2)**.
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="Skapa en Logic App Designer":::

10. Logga in på ditt Outlook.com-konto. Se till att klicka på **Ja** för att tillåta att Logic-appen får åtkomst till kontot. Fyll i fälten för att skicka ett e-postmeddelande.

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="Skapa ett Logic app-skicka e-post steg":::

    >[!TIP]
    > Du kan hämta data från en interjson-respons, till exempel `geometryId` eller `deviceId` i dina e-postaviseringar genom att konfigurera Logic app för att läsa data som skickas av event Grid. Information om hur du konfigurerar Logi Kap par för att använda och skicka händelse data till e-postaviseringar finns i [Självstudier: skicka e-postaviseringar om Azure IoT Hub händelser med event Grid och Logic Apps](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps).

11. Klicka på **Spara** i det övre vänstra hörnet i Logic Apps designer.

12. Upprepa steg 3-11 för att skapa en andra Logic-app för att meddela chefen när utrustningen avslutar Bygg platsen. Namnge Logic-appen `Equipment-Exit` .

## <a name="create-azure-maps-events-subscriptions"></a>Skapa prenumerationer för Azure Maps händelser

Azure Maps har stöd för tre händelsetyper. Du kan ta en titt på händelsetyper som Azure Maps stöder [här](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps).  Vi måste skapa två olika händelse prenumerationer: en för ett geografiskt avgränsnings tecken som anger händelser och en för att avsluta händelser.

Följ stegen nedan för att skapa en händelseprenumeration för geofence-inträdeshändelserna. Du kan prenumerera på händelser med en inhägnad genom att upprepa stegen på liknande sätt.

1. Navigera till ditt Azure Maps-konto. I instrument panelen väljer du **prenumerationer**. Klicka på ditt prenumerations namn och välj **händelser** på menyn Inställningar.

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Navigera till Azure Maps konto händelser":::

2. Om du vill skapa en händelse prenumeration väljer du **+ händelse prenumeration** på sidan händelser.

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Skapa en prenumeration för Azure Maps-händelser":::

3. På sidan **Skapa händelse prenumeration** anger du följande värden:
    * *Namnet* på händelse prenumerationen.
    * *Händelse schemat* ska vara *Event Grid schema*.
    * *Systemets ämnes namn* för den här händelse prenumerationen. I det här fallet ska vi använda `Contoso-Construction` .
    * Välj som händelse typ på *Filtrera efter händelse typer* `Geofence Entered` .
    * För *slut punkts typ*väljer du `Web Hook` .
    * För *slut punkt*kopierar du HTTP post-URL: en för Logic app. Ange slut punkten som du skapade i föregående avsnitt. Om du har glömt att spara det kan du bara gå tillbaka till Logic App Designer och kopiera den från steget HTTP-utlösare.

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Prenumerations information för Azure Maps händelser":::

4. Klicka på **Skapa**.

5. Upprepa steg 1-4 för den logiska appens slut punkt som du skapade i föregående avsnitt. I steg 3, se till att välja `Geofence Exited` som händelse typ.

## <a name="use-search-geofence-get-api"></a>Använd Search-API för hämtning av geografiskt avgränsning

Nu ska vi använda Sök funktionen för att [Hämta API: et](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) för att skicka e-postmeddelanden till Operations Manager när en del av utrustningen går in eller avslutar-avgränsningarna.

Varje utrustning har en `deviceId` . I den här självstudien kommer vi att spåra en enda utrustning, vars unika ID är `device_1` .

För tydlighetens skull visar följande diagram de fem platserna för utrustningen över tid, med början från *Start* platsen, som ligger någonstans utanför de yttre avgränsningarna. I den här självstudien är *Start* platsen odefinierad, eftersom vi inte kommer att fråga enheten på den platsen.

När vi frågar [efter funktionen](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) för att hämta ett API med en utrustnings plats som anger inledande gräns eller avsluta, kommer Event Grid att anropa rätt Logic app-slutpunkt för att skicka ett e-postmeddelande till Operations Manager.

Vart och ett av följande avsnitt gör HTTP GET-API-begäranden med de fem olika plats koordinaterna för utrustningen.

![Gräns karta i Azure Maps](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>Utrustnings plats 1 (47.638237,-122,132483)

1. Längst upp i Postman-appen väljer du **nytt**. I fönstret **Skapa nytt** väljer du **begäran**.  Ange ett **namn** för begäran. Vi använder namnet, *plats 1*. Välj den samling som du skapade i [avsnittet Ladda upp polystaket-data](#upload-geofencing-geojson-data)och välj sedan **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL-adress `{Azure-Maps-Primary-Subscription-key}` . Ersätt med den primära prenumerations nyckeln och `{udid}` med den `udid` som du sparade i [avsnittet Ladda upp polyinhägnade-data](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Klicka på knappen **Skicka** . Följande interjson visas i svars fönstret.

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

4. I det inre JSON-svaret ovan innebär det negativa avståndet från huvud platsens inre gräns att utrustningen ligger innanför den inre gränsen. Det positiva avståndet från under platsens yttre gräns innebär att utrustningen ligger utanför under platsens yttre gräns. Eftersom det här är första gången den här enheten finns inuti den huvudsakliga platsens inre gräns, `isEventPublished` anges parametern till `true` och Operations Manager skulle ha fått ett e-postmeddelande om att utrustningen har fyllt i avgränsningen.

### <a name="location-2-4763800-122132531"></a>Plats 2 (47.63800,-122,132531)

1. Längst upp i Postman-appen väljer du **nytt**. I fönstret **Skapa nytt** väljer du **begäran**.  Ange ett **namn** för begäran. Vi använder namnet, *plats 2*. Välj den samling som du skapade i [avsnittet Ladda upp polystaket-data](#upload-geofencing-geojson-data)och välj sedan **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL-adress `{Azure-Maps-Primary-Subscription-key}` . Ersätt med den primära prenumerations nyckeln och `{udid}` med den `udid` som du sparade i [avsnittet Ladda upp polyinhägnade-data](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Klicka på knappen **Skicka** . Följande interjson visas i svars fönstret:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

4. I det geografiskt JSON-svaret ovan har utrustningen hållits på den huvudsakliga platsens gräns och har inte angett under platsens gräns. Därför `isEventPublished` är parametern inställd på `false` och Operations Manager får inte några e-postaviseringar.

### <a name="location-3-4763810783315048-12213336020708084"></a>Plats 3 (47.63810783315048,-122.13336020708084)

1. Längst upp i Postman-appen väljer du **nytt**. I fönstret **Skapa nytt** väljer du **begäran**.  Ange ett **namn** för begäran. Vi använder namnet, *plats 3*. Välj den samling som du skapade i [avsnittet Ladda upp polystaket-data](#upload-geofencing-geojson-data)och välj sedan **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL-adress `{Azure-Maps-Primary-Subscription-key}` . Ersätt med den primära prenumerations nyckeln och `{udid}` med den `udid` som du sparade i [avsnittet Ladda upp polyinhägnade-data](#upload-geofencing-geojson-data).

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. Klicka på knappen **Skicka** . Följande interjson visas i svars fönstret:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

4. I ett geografiskt JSON-svar ovan har utrustningen hållits i den huvudsakliga platsens grund gräns, men har angett under platsens gräns. Därför `isEventPublished` anges parametern till `true` och Operations Manager får ett e-postmeddelande som anger att utrustningen har angett ett avgränsnings tecken.

    >[!NOTE]
    >Om utrustningen har flyttats till under platsen efter kontors tid skulle ingen händelse publiceras och Operations Manager inte ta emot några meddelanden.  

### <a name="location-4-47637988-1221338344"></a>Plats 4 (47.637988,-122,1338344)

1. Längst upp i Postman-appen väljer du **nytt**. I fönstret **Skapa nytt** väljer du **begäran**.  Ange ett **namn** för begäran. Vi använder namnet, *plats 4*. Välj den samling som du skapade i [avsnittet Ladda upp polystaket-data](#upload-geofencing-geojson-data)och välj sedan **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL-adress `{Azure-Maps-Primary-Subscription-key}` . Ersätt med den primära prenumerations nyckeln och `{udid}` med den `udid` som du sparade i [avsnittet Ladda upp polyinhägnade-data](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Klicka på knappen **Skicka** . Följande interjson visas i svars fönstret:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

4. I det geografiskt JSON-svaret ovan har utrustningen hållits på huvud platsens grund gräns, men har slut på under platsens gräns. Men om du märker det `userTime` är värdet efter det `expiredTime` som definierats i dessa data. Därför `isEventPublished` är parametern inställd på `false` och Operations Manager får inte något e-postmeddelande.

### <a name="location-547637988-1221338344"></a>Plats 5 (47.637988,-122,1338344)

1. Längst upp i Postman-appen väljer du **nytt**. I fönstret **Skapa nytt** väljer du **begäran**.  Ange ett **namn** för begäran. Vi använder namnet, *plats 4*. Välj den samling som du skapade i [avsnittet Ladda upp polystaket-data](#upload-geofencing-geojson-data)och välj sedan **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL-adress `{Azure-Maps-Primary-Subscription-key}` . Ersätt med den primära prenumerations nyckeln och `{udid}` med den `udid` som du sparade i [avsnittet Ladda upp polyinhägnade-data](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Klicka på knappen **Skicka** . Följande interjson visas i svars fönstret:

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

4. I det geografiskt JSON-svaret ovan har utrustningen avslutat huvud platsens gräns. Därför `isEventPublished` anges parametern till `true` och Operations Manager får ett e-postmeddelande som anger att utrustningen har avslutat ett avgränsnings tecken.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera innehålls typer i Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)

> [!div class="nextstepaction"]
> [Skicka e-postmeddelanden med hjälp av Event Grid och Logic Apps](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps)

> [!div class="nextstepaction"]
> [Händelse hanterare som stöds i Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers).

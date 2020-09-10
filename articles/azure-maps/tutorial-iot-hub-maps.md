---
title: 'Självstudie: implementera IoT spatial Analytics med Microsoft Azure Maps'
description: 'Integrera IoT Hub med Microsoft Azure Maps-tjänst-API: er.'
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 86ae186d3d8b131955be7d9fa2c305316dea9f00
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658438"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Självstudie: implementera IoT spatial Analytics med hjälp av Azure Maps

I ett IoT-scenario är det vanligt att fånga och spåra relevanta händelser som inträffar i utrymme och tid. Exempel scenarier är hantering av flottan, till gångs spårning, mobilitet och smarta stads program. Den här självstudien vägleder dig genom en lösning som spårar användning av Hyr Hyr bilar med hjälp av Azure Maps API: er

I den här självstudien kommer du att:

> [!div class="checklist"]
> * Skapa ett Azure Storage-konto för att logga data för Car-spårning.
> * Överför en avgränsning till Azure Maps data tjänsten med hjälp av API: et för data överföring.
> * Skapa en IoT Hub och registrera en enhet.
> * Skapa en funktion i Azure Functions, implementera affärs logik baserat på Azure Maps spatial analys.
> * Prenumerera på IoT Device telemetri-händelser från Azure-funktionen via Event Grid.
> * Filtrera telemetri-händelserna med IoT Hub meddelanderoutning.

## <a name="prerequisites"></a>Förutsättningar

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. [Skapa ett Azure Maps-konto](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel. Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](how-to-manage-authentication.md).

4. [Skapa en resurs grupp](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups). I den här självstudien namnger vi vår resurs grupp *ContosoRental*, men du kan välja det namn du vill.

5. Ladda ned [C#-projektet rentalCarSimulation ](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

I den här självstudien används [Postman](https://www.postman.com/) -programmet, men du kan välja en annan API utvecklings miljö.

## <a name="use-case-rental-car-tracking"></a>Användnings fall: Hyr bils spårning

I den här självstudien demonstreras följande scenario: ett bil uthyrnings företag vill logga plats information, passerat och köra tillstånd för sina hyr bilar. Företaget vill också lagra denna information varje gång en bil lämnar rätt auktoriserade geografiska region.

I vårt användnings fall är hyr bilar utrustade med IoT-enheter som regelbundet skickar telemetridata till Azure IoT Hub. Telemetrin innehåller den aktuella platsen och anger om bilens motor körs. Schemat för enhets platsen följer IoT Plug and Play- [schemat för geospatiala data](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). Schemat för hyr bils enhets telemetri ser ut som följande JSON-kod:

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": {
            "location": {
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            }
        }
    }
}
```

I den här självstudien spårar vi bara ett fordon. När vi har konfigurerat Azure-tjänsterna måste du ladda ned [RentalCarSimulation C#-projektet ](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) för att köra fordon simulatorn. Hela processen, från händelse till Function-körning, sammanfattas i följande steg:

1. Enheten i fordonet skickar telemetridata till IoT Hub.

2. Om bilen körs publicerar IoT-hubben telemetri-data till Event Grid.

3. En Azure-funktion utlöses på grund av händelse prenumerationen på händelser för telemetri.

4. Funktionen kommer att logga plats koordinaterna för fordons enheten, händelse tiden och enhets-ID: t. Sedan används den [spatiala netstängsel-API: n](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) för att avgöra om bilen har bearbetats utanför avgränsnings gränsen. Om den har passerat utanför gränserna, lagrar funktionen plats data som tas emot från händelsen i en BLOB-behållare. Funktionen ställer också frågor till [Omvänd adresss ökning](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) för att översätta koordinatens plats till en gatuadress och lagra den med resten av enhetens plats data.

Följande diagram innehåller en översikt över systemet.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="System översikt":::

Följande bild visar det yt avgränsnings ytan i blått. Hyr bils väg visas med en grön linje.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Netstängsel-väg":::

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

Vi kommer att skapa ett [Allmänt-syfte v2-lagrings konto](https://docs.microsoft.com/azure/storage/common/storage-account-overview#general-purpose-v2-accounts) i din resurs grupp för att lagra data för spårning av Car Om du inte har skapat någon resurs grupp följer du anvisningarna i [skapa en resurs grupp](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups). I den här självstudien namnger vi vår resurs grupp *ContosoRental*.

Följ instruktionerna i [skapa ett lagrings konto](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)för att skapa ett lagrings konto. I den här självstudien namnger vi lagrings kontots *contosorentalstorage*, men du kan ge det ett namn som du vill.

När ditt lagrings konto har skapats måste du skapa en behållare för att lagra loggnings data.

1. Navigera till ditt nyligen skapade lagrings konto. Klicka på **behållare** -länken i avsnittet Essentials.

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Behållare för Blob Storage":::

2. Klicka på knappen **+ container** i det övre vänstra hörnet. En panel visas på höger sida av webbläsaren. Namnge din container *contoso-hyr loggar* och klicka på **skapa**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Skapa en blobcontainer":::

3. Gå till bladet **åtkomst nycklar** i ditt lagrings konto och kopiera **lagrings kontots namn** och **nyckel** värde i avsnittet **KEY1** . Vi behöver båda värdena i avsnittet [skapa en Azure-funktion och lägga till en Event Grid prenumeration](#create-an-azure-function-and-add-an-event-grid-subscription) .

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Kopiera lagrings kontots namn och nyckel":::

## <a name="upload-a-geofence"></a>Ladda upp en avgränsning

Nu ska vi använda [Postman-appen](https://www.getpostman.com) för att [överföra den här gränsen](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) till tjänsten Azure Maps. Den här gränsen definierar det tillåtna geografiska området för vårt hyr fordon.  Vi använder den här funktionen i Azure-funktionen för att avgöra om en bil har flyttat utanför avgränsnings området.

Öppna Postman-appen och följ stegen nedan för att ladda upp den här gränsen med hjälp av API: et för Azure Maps data uppladdning.  

1. Öppna Postman-appen. Längst upp i Postman-appen väljer du **nytt**. I fönstret **Skapa nytt** väljer du **samling**.  Namnge samlingen och välj knappen **skapa** .

2. Välj **nytt** om du vill skapa en begäran. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn** för begäran. Välj den samling som du skapade i föregående steg och välj sedan **Spara**.

3. Välj metoden **post** http på fliken Builder och ange följande URL för att ladda upp avgränsningen till API: et för data uppladdning. Se till att ersätta `{subscription-key}` med den primära prenumerations nyckeln.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    Värdet "interjson" mot `dataFormat` parametern i URL-sökvägen representerar formatet för de data som laddas upp.

4. Klicka på **brödtext** och välj sedan **RAW** -indataformat och välj **JSON** som indata-format i list rutan. Öppna JSON-datafilen [här](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)och kopiera JSON till avsnittet brödtext. Klicka på **Skicka**.

5. Klicka på knappen blå **sändning** och vänta tills begäran har bearbetats. När begäran har slutförts går du till fliken **sidhuvud** i svaret. Kopiera värdet för **plats** nyckeln, som är `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Om du vill kontrol lera status för API-anropet skapar du en **Get** http-begäran på `status URL` . Du måste lägga till din primära prenumerations nyckel till URL: en för autentisering. **Get** -begäran bör likna följande URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}

7. When the **GET** HTTP request completes successfully, it will return a `resourceLocation`. The `resourceLocation` contains the unique `udid` for the uploaded content. You'll need to copy this `udid` for later use in this tutorial.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-azure-iot-hub"></a>Skapa en Azure IoT-hubb

Azure IoT Hub möjliggör säker och tillförlitlig dubbelriktad kommunikation mellan ett IoT-program och de enheter som hanteras av.  I vårt scenario vill vi hämta information från vår fordons enhet för att fastställa placeringen av hyr bilen. I det här avsnittet ska vi skapa en IoT-hubb i resurs gruppen *ContosoRental* . IoT Hub ansvarar för att publicera våra telemetri händelser.

> [!NOTE]
> IoT Hub-funktionen för att publicera enhets telemetri händelser på Event Grid finns i offentlig för hands version. Offentliga för hands versions funktioner är tillgängliga i alla regioner utom **östra USA, västra USA, Västeuropa, Azure Government, Azure Kina 21Vianet** och **Azure Germany**.

Om du vill skapa en IoT-hubb i resurs gruppen *ContosoRental* följer du stegen i [skapa en IoT-hubb](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).

## <a name="register-a-device-in-iot-hub"></a>Registrera en enhet i IoT Hub

Enheter kan inte ansluta till IoT Hub om de inte är registrerade i IoT Hub Identity-registret. I vårt scenario skapar vi en enskild enhet med namnet *InVehicleDevice*. Om du vill skapa och registrera enheten i IoT Hub följer du stegen i [Registrera en ny enhet i IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#register-a-new-device-in-the-iot-hub). Se till att kopiera den **primära anslutnings strängen** för enheten, eftersom vi kommer att använda den i ett senare steg.

## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Skapa en Azure-funktion och Lägg till en Event Grid-prenumeration

Azure Functions är en server lös beräknings tjänst som gör att du kan köra små delar av kod ("Functions"), utan att uttryckligen behöva etablera eller hantera beräknings infrastruktur. Mer information om Azure Functions finns i [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) -dokumentationen.

En funktion är "utlöst" av en viss händelse. I vårt scenario skapar vi en funktion som utlöses av en Event Grid-utlösare. Vi skapar relationen mellan utlösare och funktion genom att skapa en händelse prenumeration för IoT Hub-enheter för telemetri. När en händelse för telemetri om enhet inträffar, kommer vår funktion att anropas som en slut punkt och får relevanta data för [enheten som vi tidigare registrerade i IoT Hub](#register-a-device-in-iot-hub).

C#-skript koden som vår funktion kommer att innehålla kan visas [här](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx).

Nu ska vi konfigurera vår Azure-funktion.

1. Klicka på **skapa en resurs**i Azure Portal instrument panelen. Skriv **Funktionsapp** i text rutan Sök. Klicka på **Funktionsapp**. Klicka på **Skapa**.

2. På sidan Skapa **Funktionsapp** namnger du din Function-app. Under **resurs grupp**väljer du *ContosoRental* i list rutan.  Välj *.net Core* som **körnings stack**. Klicka på **Nästa: värd >** längst ned på sidan.

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Skapa en funktionsapp":::

3. För **lagrings konto**väljer du det lagrings konto som du skapade i [skapa ett Azure Storage-konto](#create-an-azure-storage-account). Klicka på **Granska + skapa**.

4. Granska programmets information om appen och klicka på **skapa**.

5. När appen har skapats lägger vi till en funktion i den. Gå till Function-appen. Klicka på **funktions** bladet. Klicka på **+ Lägg till** överst på sidan. Panelen funktion mall visas. Rulla nedåt i panelen. Klicka på **Azure Event Grid utlösare**.

     >[!WARNING]
    > **Azure Event Hub-utlösaren** och **Azure Event Grid utlösare** har liknande namn. Se till att du klickar på mallen för **Azure Event Grid-utlösare** .

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Skapa en funktion":::

6. Ge funktionen ett namn. I den här självstudien använder vi namnet *GetGeoFunction*, men du kan använda vilket namn som helst. Klicka på **skapa funktion**.

7. Klicka på bladet **kod och test** på den vänstra menyn. Kopiera och klistra in [C#-skriptet](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) i fönstret kod.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Kopiera och klistra in kod i funktions fönstret":::

8. Ersätt följande parametrar i C#-koden. Klicka på **Spara**. Klicka inte på **test/kör** ännu
    * Ersätt **SUBSCRIPTION_KEY** med din primära prenumerations nyckel för Azure Maps-kontot.
    * Ersätt **UDID** med det geografiskt `udid` avgränsnings tecken som du laddade upp i [överför ett avgränsnings](#upload-a-geofence)tecken.
    * Funktionen **CreateBlobAsync** i skriptet skapar en BLOB per händelse i data lagrings kontot. Ersätt **ACCESS_KEY**, **ACCOUNT_NAME**och **STORAGE_CONTAINER_NAME** med lagrings kontots åtkomst nyckel, konto namn och data lagrings behållare. Dessa värden genererades när du skapade ditt lagrings konto i [skapa ett Azure Storage-konto](#create-an-azure-storage-account).

9. Klicka på bladet **integration** på menyn till vänster. Klicka på **Event Grid utlösare** i diagrammet. Skriv ett namn för utlösaren, *eventGridEvent*och klicka på **Skapa event Grid prenumeration**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Lägga till händelseprenumeration":::

10. Fyll i prenumerations information. Namnge händelse prenumerationen. För *händelse schema*väljer du *Event Grid schema*. För **ämnes typer**väljer du *Azure IoT Hub-konton*. För **resurs grupp**väljer du den resurs grupp som du skapade i början av den här självstudien. För **resurs**väljer du den IoT-hubb som du skapade i [skapa en Azure IoT Hub](#create-an-azure-iot-hub). För **filter till händelse typer**väljer du *telemetri för enhet*. När du har valt de här alternativen visas **ämnes typen** ändra till *IoT Hub*. I **namn på system ämne**kan du använda samma namn som din resurs.  Klicka slutligen på **Välj en slut punkt** i avsnittet **information om slut punkt** . Godkänn alla inställningar och klicka på **Bekräfta markering**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Skapa händelse prenumeration":::

11. Granska inställningarna. Kontrol lera att slut punkten anger den funktion som du skapade i början av det här avsnittet. Klicka på **Skapa**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Bekräfta skapande av händelse prenumeration":::

12. Nu är du tillbaka på panelen **Redigera utlösare** . Klicka på **Spara**.

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrera händelser med IoT Hub meddelanderoutning

När du lägger till en Event Grid-prenumeration i Azure-funktionen skapas en meddelande väg automatiskt i den angivna IoT-hubben. Med meddelanderoutning kan du dirigera olika data typer till olika slut punkter. Du kan till exempel dirigera meddelanden om enhets telemetri, livs cykel händelser för enhet och enhets dubbla ändrings händelser. Mer information om IoT Hub-meddelanderoutning finns i [använda IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)meddelanderoutning.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Meddelanderoutning i IoT Hub":::

I vårt exempel scenario vill vi bara ta emot meddelanden när hyr bil flyttas. Vi ska skapa en cirkulations fråga för att filtrera händelserna där `Engine` egenskapen är lika med **"på"**. Om du vill skapa en cirkulations fråga, klickar du på **RouteToEventGrid** -vägen och ersätter **Oper. åga** med **"motor =" på "** och klickar på **Spara**. Nu kan IoT Hub bara publicera enhets telemetri där motorn är på.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Filtrera meddelanden i Routning":::

>[!TIP]
>Det finns olika sätt att fråga IoT-meddelanden från enhet till molnet för att lära dig mer om syntax för meddelanderoutning, se [IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax)meddelanderoutning.

## <a name="send-telemetry-data-to-iot-hub"></a>Skicka telemetridata till IoT Hub

När vår Azure-funktion är igång kan vi nu skicka telemetridata till IoT Hub, som dirigerar den till Event Grid. Vi använder ett C#-program för att simulera plats data för en fordons enhet i en hyr bil. Om du vill köra programmet måste du ha .NET Core SDK 2.1.0 eller senare på din utvecklings dator. Följ stegen nedan för att skicka simulerade telemetridata till IoT Hub.

1. Om du inte redan har gjort det kan du ladda ned [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C#-projektet.

2. Öppna simulatedCar.cs-filen i valfri text redigerare och Ersätt värdet för `connectionString` med den som du sparade när du registrerade enheten och spara ändringarna i filen.

3. Kontrol lera att du har .NET Core installerat på datorn. I det lokala terminalfönstret navigerar du till mappen C# i C#-projektet och kör följande kommando för att installera de nödvändiga paketen för det simulerade enhets programmet:

    ```cmd/sh
    dotnet restore
    ```

4. I samma Terminal kör du följande kommando för att skapa och köra ett simulerings program för hyr bil:

    ```cmd/sh
    dotnet run
    ```

  Den lokala terminalen bör se ut som den som anges nedan.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Terminal-utdata":::

Om du öppnar Blob storage-behållaren nu kan du se fyra blobbar för platser där fordonet befinner sig utanför gränsen.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Visa blobar i behållaren":::

Kartan nedan visar fyra fordons plats punkter utanför den yttre gränsen. Varje plats loggades med regelbundna tidsintervall.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Överträdelse karta":::

## <a name="explore-azure-maps-and-iot"></a>Utforska Azure Maps och IoT

Information om Azure Maps-API: er som används i den här självstudien finns i:

* [Hämta omvänd söknings adress](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Hämta avgränsning](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

En fullständig lista över Azure Maps REST API: er finns i:

* [Azure Maps REST-API: er](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Mer information om IoT Plug and Play finns i:

* [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp)

Om du vill hämta en lista över enheter som är Azure-certifierade för IoT går du till:

* [Azure Certified-enheter](https://catalog.azureiotsolutions.com/)

## <a name="next-steps"></a>Efterföljande moment

Om du vill veta mer om hur du skickar enheter till molnbaserad telemetri och det andra sättet runt, se:

> [!div class="nextstepaction"]
> [Skicka telemetri från en enhet](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

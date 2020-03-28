---
title: 'Handledning: Implementera IoT spatial analytics | Microsoft Azure Maps'
description: Integrera IoT Hub med Microsoft Azure Maps-tjänst-API:er.
author: philmea
ms.author: philmea
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: cfea9aa7bfcc9a9698bb93bdf54797481b8539ce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333969"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Självstudiekurs: Implementera spatialanalys för IoT med Azure Maps

I ett IoT-scenario är det vanligt att fånga in och spåra relevanta händelser som inträffar i tid och rum. Exempel på scenarier är vagnparkshantering, tillgångsspårning, mobilitet och smarta stadsprogram. Den här självstudien guidar dig genom ett lösningsmönster med azure maps API:er. Relevanta händelser fångas in av IoT Hub med hjälp av händelseprenumerationsmodellen som tillhandahålls av Event Grid.

I den här guiden kommer du att:

> [!div class="checklist"]
> * Skapa en IoT Hub.
> * Ladda upp geofence-området i Azure Maps-datatjänsten med hjälp av API:et för datauppladdning.
> * Skapa en funktion i Azure Functions, implementera affärslogik baserat på Azure Maps spatial analytics.
> * Prenumerera på IoT-enhetstelemetrihändelser från Azure-funktionen via Event Grid.
> * Filtrera telemetrihändelserna med hjälp av IoT Hub-meddelanderoutning.
> * Skapa ett lagringskonto för att lagra relevanta händelsedata.
> * Simulera en IoT-enhet i fordonet.
    

## <a name="use-case"></a>Användningsfall

Denna lösning visar ett scenario där ett biluthyrningsföretag planerar att övervaka och logga händelser för sina hyrbilar. Biluthyrningsföretag hyr vanligtvis ut bilar till en viss geografisk region. De måste spåra bilarna var de är medan de hyrs. Instanser av en bil som lämnar den valda geografiska regionen måste loggas. Loggningsdata säkerställer att policyer, avgifter och andra affärsaspekter hanteras korrekt.

I vårt användningsfall är hyrbilarna utrustade med IoT-enheter som regelbundet skickar telemetridata till Azure IoT Hub. Telemetrin innehåller den aktuella platsen och anger om bilens motor är igång. Enhetsplaceringsschemat följer IoT [Plug and Play-schemat för geospatiala data](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md). Hyrbilens enhetstelemetrischema ser ut:

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

Låt oss använda telemetri i fordon för att uppnå vårt mål. Vi vill genomföra geofencing regler. Och vi vill svara när vi får en händelse som anger att bilen har flyttat. För att göra det prenumererar vi på enhetstelemetrihändelserna från IoT Hub via Event Grid. 

Det finns flera sätt att prenumerera på Event Grid, i den här självstudien använder vi Azure Functions. Azure Functions reagerar på händelser som publiceras i händelserutnätet. Den implementerar också biluthyrning affärslogik, som bygger på Azure Maps spatial analytics. 

Kod i Azure-funktionen kontrollerar om fordonet har lämnat geofence. Om fordonet lämnade geofence samlar Azure-funktionen in ytterligare information, till exempel adressen som är kopplad till den aktuella platsen. Funktionen implementerar också logik för att lagra meningsfulla händelsedata i en datablobblagring som hjälper till att ge beskrivning av händelseomständigheterna. 

Evenemanget omständigheter kan vara till hjälp för biluthyrningsföretaget och hyrkunden. Följande diagram ger dig en översikt på hög nivå av systemet.

 
  <center>

  ![Systemöversikt](./media/tutorial-iot-hub-maps/system-diagram.png)
  
  </center>

Följande bild representerar geofence-området markerat i blått. Hyrbilens rutt indikeras av en grön linje.

  ![Geofence rutt](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Krav 

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

För att slutföra stegen i den här självstudien måste du först skapa en resursgrupp i Azure-portalen. Så här skapar du en resursgrupp:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Resursgrupper**.
    
   ![Resursgrupper](./media/tutorial-iot-hub-maps/resource-group.png)

3. Under **Resursgrupper**väljer du **Lägg till**.
    
   ![Lägga till resursgrupp](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Ange följande egenskapsvärden:
    * **Prenumeration:** Välj din Azure-prenumeration.
    * **Resursgrupp:** Ange "ContosoRental" som resursgruppsnamn.
    * **Region:** Välj en region för resursgruppen.  

    ![Information om resursgrupp](./media/tutorial-iot-hub-maps/resource-details.png)

    Välj **Granska + skapa**och välj sedan **Skapa** på nästa sida.

### <a name="create-an-azure-maps-account"></a>Skapa ett Azure Maps-konto 

Om du vill implementera affärslogik baserad på Azure Maps spatialanalys måste vi skapa ett Azure Maps-konto i resursgruppen som vi skapade. Följ instruktionerna i [Skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps) för att skapa en Azure Maps-kontoprenumeration med S1-prisnivå. Följ stegen för [att få primärnyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account) för att få din primära nyckel för ditt konto. Mer information om autentisering i Azure Maps finns [i hantera autentisering i Azure Maps](how-to-manage-authentication.md).



### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

För att logga händelsedata skapar vi en allmän **v2storage** som ger åtkomst till alla Azure Storage-tjänster: blobbar, filer, köer, tabeller och diskar.  Vi måste placera det här lagringskontot i resursgruppen "ContosoRental" för att lagra data som blobbar. Om du vill skapa ett lagringskonto följer du anvisningarna i [att skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal). Därefter måste vi skapa en behållare för att lagra blobbar. Följ stegen nedan för att göra det:

1. I ditt "lagringskonto - blob, fil, tabell, kö", navigera till Behållare.

    ![Blobbar](./media/tutorial-iot-hub-maps/blobs.png)

2. Klicka på behållarknappen längst upp till vänster och namnge din behållare "contoso-rental-logs" och klicka på "OK".

    ![blob-behållare](./media/tutorial-iot-hub-maps/blob-container.png)

3. Navigera till **bladet Access-nycklar** i ditt lagringskonto och kopiera "lagringskontonamnet" och "åtkomstnyckeln". De behövs i ett senare steg.

    ![åtkomstnycklar](./media/tutorial-iot-hub-maps/access-keys.png)


Nu har vi ett lagringskonto och en behållare för att logga händelsedata. Därefter skapar vi ett IoT-nav.

### <a name="create-an-iot-hub"></a>Skapa en IoT Hub

IoT Hub är en hanterad tjänst i molnet. IoT Hub fungerar som en central meddelandehubb för dubbelriktad kommunikation mellan ett IoT-program och de enheter som hanteras av den. Skapa en IoT Hub i resursgruppen "ContosoRental" för att dirigera enhetstelemetrimeddelanden till ett händelserutnät. Ställ in en meddelanderuttintegration där vi filtrerar meddelanden baserat på bilens motorstatus. Vi skickar även e-postmeddelanden till eventrutnätet när bilen rör sig.

> [!Note] 
> IoT Hub s funktioner för att publicera enhet telemetri händelser på Event Grid är i offentlig förhandsvisning. Offentliga förhandsversionsfunktioner är tillgängliga i alla regioner utom **östra USA, västra USA, Västeuropa, Azure Government, Azure China 21Vianet** och **Azure Germany**. 

Skapa en Iot Hub genom att följa stegen i [skapa en IoT Hub avsnitt](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).


### <a name="register-a-device"></a>Registrera en enhet 

För att kunna ansluta till IoT Hub måste en enhet registreras. Så här registrerar du en enhet med IoT-hubb:

1. I din IoT Hub klickar du på bladet "IoT-enheter" och klickar på "Nytt".

    ![tilläggsenhet](./media/tutorial-iot-hub-maps/add-device.png)

2. På sidan Skapa en enhet namnger du IoT-enheten och klickar på "Spara".
    
    ![registrera-enhet](./media/tutorial-iot-hub-maps/register-device.png)

3. Spara **enhetens primära anslutningssträng** för att använda den i ett senare steg, där du måste ändra en platshållare med den här anslutningssträngen.

    ![tilläggsenhet](./media/tutorial-iot-hub-maps/connection-string.png)

## <a name="upload-geofence"></a>Ladda upp geofence

Vi använder [Postman-programmet](https://www.getpostman.com) för att [ladda upp geofence](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) till Azure Maps-tjänsten med hjälp av Azure Maps Data Upload API. Alla händelser när bilen är utanför denna geofence kommer att loggas.

Öppna Postman-appen och följ stegen nedan för att ladda upp geofence med hjälp av Azure Maps, Data Upload API.  

1. Klicka på nya | Skapa nytt och välj Begär. Ange ett namn för Request (Begäran) för Upload geofence data (Ladda upp geofence-data), välj en samling eller en mapp att spara den i och klicka på Save (Spara).

    ![Ladda upp geofences med hjälp av Postman](./media/tutorial-iot-hub-maps/postman-new.png)

2. Välj POST HTTP-metod på fliken Builder (Byggare) och ange följande URL för att göra en POST-begäran.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Värdet "geojson" mot `dataFormat` parametern i URL-sökvägen representerar formatet på de data som överförs.

3. Klicka på **Params** och ange följande nyckel/värde-par som ska användas för POST-begäran-URL. Ersätt värdet för prenumerationsnyckel med nyckeln Azure Maps.
   
    ![Key-Value-parametrar för Postman](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Klicka på **Brödtext** och välj sedan råinmatningsformat och välj **raw** **JSON (program/text)** som indataformat i listrutan. Öppna JSON-datafilen [här](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)och kopiera Json i brödtextavsnittet som data som ska laddas upp och klicka på **Skicka**.
    
    ![publicera data](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Granska svarsrubrikerna. När en lyckad begäran har slutförts innehåller **platshuvudet** status-URI för att kontrollera aktuell status för överföringsbegäran. Status-URI:n kommer att vara av följande format. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Kopiera din status-URI `subscription-key` och lägg till en parameter i den. Tilldela värdet för prenumerationsnyckeln för `subscription-key` ditt Azure Maps-konto till parametern. Status-URI-formatet ska vara som `{Subscription-key}` det nedan och ersättas med din prenumerationsnyckel.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. För att `udId` få, öppna en ny flik i Postman-appen och välj GET HTTP-metoden på fliken Builder och gör en GET-begäran på status-URI. Om dataöverföringen lyckades får du en udId i svarstexten. Kopiera udId för senare användning.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


Därefter skapar vi en Azure-funktion i resursgruppen "ContosoRental" och konfigurerar sedan en meddelandeväg i IoT Hub för att filtrera enhetstelemetrimeddelanden.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Skapa en Azure-funktion och lägga till en Event Grid-prenumeration

Azure Functions är en serverlös beräkningstjänst som gör det möjligt för oss att köra kod på begäran, utan att uttryckligen etablera eller hantera beräkningsinfrastruktur. Om du vill veta mer om Azure Functions kan du läsa dokumentationen till [Azure-funktioner.](https://docs.microsoft.com/azure/azure-functions/functions-overview) 

Logiken vi implementerar i funktionen använder platsdata som kommer från telemetrin i fordon för att bedöma geofence-status. Om ett visst fordon går utanför geofence, kommer funktionen att samla in mer information som adressen till platsen via [Get Search Address Reverse API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Detta API översätter en viss platskoordinat till en mänsklig begriplig gatuadress. 

All relevant händelseinformation lagras sedan i blob-butiken. Steg 5 nedan pekar på den körbara koden som implementerar sådan logik. Följ stegen nedan för att skapa en Azure-funktion som skickar dataloggar till blob-behållaren i blob-lagringskontot och lägga till en Event Grid-prenumeration på den.

1. Välj skapa en resurs i Azure portal-instrumentpanelen. Välj **Beräkna** i listan över tillgängliga resurstyper och välj sedan **Funktionsapp**.

    ![skapa-resurs](./media/tutorial-iot-hub-maps/create-resource.png)

2. Namnge **funktionsappen** på sidan Skapa funktionsapp. Under **Resursgrupp**väljer du **Använd befintlig**och väljer "ContosoRental" i listrutan. Välj ".NET Core" som Runtime Stack. Under **Hosting**för **lagringskonto**väljer du lagringskontonamnet från ett tidigare steg. I vårt tidigare steg namngav vi lagringskontot **v2storage**.  Välj sedan **Granska+Skapa**.
    
    ![skapa-app](./media/tutorial-iot-hub-maps/rental-app.png)

2. Granska information om funktionsappen och välj "Skapa".

3. När appen har skapats måste vi lägga till en funktion i den. Gå till funktionsappen. Klicka på **Ny funktion** om du vill lägga till en funktion och välja **In-Portal** som utvecklingsmiljö. Välj sedan **Fortsätt**.

    ![skapa-funktion](./media/tutorial-iot-hub-maps/function.png)

4. Välj **Fler mallar** och klicka på **Slutför och visa mallar**. 

5. Välj mallen med en **Azure Event Grid-utlösare**. Installera tillägg om du uppmanas att ange, namnge funktionen och välj **Skapa**.

    ![funktionsmall](./media/tutorial-iot-hub-maps/eventgrid-funct.png)
    
    **Azure Event Hub-utlösaren** och **Azure Event Grid-utlösaren** har liknande ikoner. Se till att du väljer **Azure Event Grid-utlösaren**.

6. Kopiera [C#-koden](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) till din funktion.
 
7. Ersätt följande parametrar i C#-skriptet. Klicka på **Spara**. Klicka inte på **Kör** ännu
    * Ersätt **SUBSCRIPTION_KEY** med primär prenumerationsnyckel för Ditt Azure Maps-konto.
    * Ersätt **UDID** med udId av geofence du laddade upp, 
    * Funktionen **CreateBlobAsync** i skriptet skapar en blob per händelse i datalagringskontot. Ersätt **ACCESS_KEY,** **ACCOUNT_NAME**och **STORAGE_CONTAINER_NAME** med åtkomstnyckeln för lagringskontot, kontonamnet och datalagringsbehållaren.

10. Klicka på **Lägg till prenumeration i händelserutnät**.
    
    ![tilläggs-händelse-rutnät](./media/tutorial-iot-hub-maps/add-egs.png)

11. Fyll i prenumerationsuppgifter, under **EVENT PRENUMERATIONSINFORMATION** NAMN din händelseprenumeration. För händelseschema väljer du "Event Grid Schema". Under **ÄMNESINFORMATION** väljer du "Azure IoT Hub Accounts" som ämnestyp. Välj samma prenumeration som du använde för att skapa resursgruppen, välj "ContosoRental" som "Resursgrupp". Välj den IoT Hub som du skapade som en "Resurs". Välj **enhetstelemetri** som händelsetyp. När du har valt dessa alternativ visas ändras "Ämnestyp" till "IoT Hub" automatiskt.

    ![prenumeration på event-grid-](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrera händelser med hjälp av IoT Hub-meddelanderoutning

När du har lagt till en Event Grid-prenumeration i Azure-funktionen visas en standardmeddelandeväg till Event Grid i IoT **Hub:s meddelanderoutningsblad.** Med meddelanderoutning kan du dirigera olika datatyper till olika slutpunkter. Du kan till exempel dirigera enhetstelemetrimeddelanden, enhetens livscykelhändelser och händelser för enhets dubbla ändringar. Mer information om IoT-hubbmeddelanderoutning finns i [Använda IoT Hub-meddelanderoutning](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

![nav-EG-rutt](./media/tutorial-iot-hub-maps/hub-route.png)

I vårt exempel scenario vill vi filtrera bort alla meddelanden där hyrbilen är i rörelse. För att publicera sådana enhetstelemetrihändelser i Event Grid använder vi routningsfrågan för att filtrera de händelser där egenskapen `Engine` är **"ON".** Det finns olika sätt att fråga IoT-meddelanden från enhet till moln, om du vill veta mer om syntax för meddelanderoutning, se [IoT Hub-meddelanderoutning](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax). Om du vill skapa en routningsfråga klickar du på vägen **RouteToEventGrid** och **ersätter routningsfrågan** med **"Engine='ON"och** klickar på **Spara**. Nu publicerar IoT-hubben endast enhetstelemetri där motorn är PÅ.

![hub-EG-filter](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Skicka telemetridata till IoT Hub

När vår Azure-funktion är igång kan vi nu skicka telemetridata till IoT Hub, som dirigerar den till händelserutnätet. Låt oss använda ett C#-program för att simulera platsdata för en fordonsenhet i en hyrbil. Om du vill köra programmet behöver du .NET Core SDK 2.1.0 eller senare på utvecklingsdatorn. Följ stegen nedan för att skicka simulerade telemetridata till IoT Hub.

1. Ladda ner [projektet RentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C# . 

2. Öppna den simulatedCar.cs filen i en textredigerare som `connectionString` du väljer och ersätt värdet på den med den du sparade när du registrerade enheten och spara ändringar i filen.
 
3. Kontrollera att du har .NET Core installerat på datorn. I det lokala terminalfönstret navigerar du till rotmappen i C#-projektet och kör följande kommando för att installera de paket som krävs för simulerat enhetsprogram:
    
    ```cmd/sh
    dotnet restore
    ```

4. I samma terminal kör du följande kommando för att bygga och köra simuleringsprogrammet för hyrbil:

    ```cmd/sh
    dotnet run
    ```

  Din lokala terminal ska se ut som den nedan.

  ![Terminalutgång](./media/tutorial-iot-hub-maps/terminal.png)

Om du öppnar blob förvaringsbehållaren nu, bör du kunna se fyra blobbar för platser där fordonet var utanför geofence.

![Ange blob](./media/tutorial-iot-hub-maps/blob.png)

Kartan nedan visar fyra punkter där fordonet var utanför geofence, loggas med jämna mellanrum.

![karta över överträdelser](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>Nästa steg

Information om hur du utforskar Api:er för Azure Maps som används i den här självstudien finns i:

* [Få sökadressen omvänd](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Hämta Geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

En fullständig lista över AZURE Maps REST API:er finns i:

* [AZURE Maps REST API:er](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Mer information om IoT Plug and Play finns i:

* [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp)

Om du vill få en lista över enheter som är Azure-certifierade för IoT besöker du:

* [Azure-certifierade enheter](https://catalog.azureiotsolutions.com/)

Mer information om hur du skickar enhet till molntelemetri och tvärtom finns i:

* [Skicka telemetri från en enhet](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

---
title: 'Självstudie: implementera IoT spatial Analytics | Microsoft Azure Maps'
description: 'Integrera IoT Hub med Microsoft Azure Maps-tjänst-API: er.'
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 24295e27a3b94f6960777a8704fdf448697da4e1
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987320"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Självstudie: implementera IoT spatial Analytics med hjälp av Azure Maps

Att spåra och fånga relevanta händelser som inträffar i utrymme och tid är ett vanligt IoT-scenario. Exempel scenarier är hantering av flottan, till gångs spårning, mobilitet och smarta stads program. Den här självstudien vägleder dig genom ett lösnings mönster för att använda Azure Maps API: er Relevanta händelser registreras av IoT Hub med hjälp av den händelse prenumerations modell som tillhandahålls av Event Grid.

I den här självstudien kommer du att:

> [!div class="checklist"]
> * Skapa en IoT Hub.
> * Ladda upp geofence-området i Azure Maps-datatjänsten med hjälp av API:et för datauppladdning.
> * Skapa en funktion i Azure Functions, implementera affärs logik baserat på Azure Maps spatial analys.
> * Prenumerera på IoT Device telemetri-händelser från Azure-funktionen via Event Grid.
> * Filtrera telemetri-händelserna med IoT Hub meddelanderoutning.
> * Skapa ett lagrings konto för att lagra relevanta händelse data.
> * Simulera en IoT-enhet i fordonet.
    

## <a name="use-case"></a>Användningsfall

Den här lösningen visar ett scenario där en bil hyr företag planerar att övervaka och logga händelser för sina hyr bilar. Leverantörer av hyr bil hyr ut bilar för en bestämd geografisk region och behöver hålla koll på sina till-platser medan de hyrs. Instanser av en bil som lämnar den valda geografiska regionen måste loggas. Loggnings data garanterar att principer, avgifter och andra affärs aspekter hanteras korrekt.

I vårt användnings fall är hyr bilar utrustade med IoT-enheter som skickar telemetridata till Azure IoT Hub med jämna mellanrum. Telemetrin innehåller den aktuella platsen och anger om bilens motor körs. Schemat för enhets platsen följer IoT Plug and Play- [schemat för geospatiala data](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md). Schemat för hyr bils enhets telemetri ser ut så här:

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

Nu ska vi använda telemetri från fordons enheter för att uppnå målet. Vi vill köra regler för avgränsning och svara när vi får en händelse som anger att bilen har flyttat. För att göra det kommer vi att prenumerera på händelser för enhets telemetri från IoT Hub via Event Grid. Det finns flera sätt att prenumerera på Event Grid i den här självstudien använder vi Azure Functions. Azure Functions reagerar på händelser som publicerats i Event Grid. Den implementerar också affärs logik för hyr bilar, som baseras på Azure Maps rums analys. Kod inuti Azure Function kontrollerar om fordonet har lämnat det inre avgränsnings gränsen. Om fordonet har lämnat den här gränsen, samlar Azure-funktionen in ytterligare information, till exempel den adress som är kopplad till den aktuella platsen. Funktionen implementerar också logik för att lagra meningsfulla händelse data i en datablob-lagring som ger en beskrivning av händelse omständigheterna. Händelse omständigheterna kan vara till hjälp för bil uthyrnings företaget och hyres kunden.

Följande diagram innehåller en översikt över systemet.

 
  <center>

  ![System översikt](./media/tutorial-iot-hub-maps/system-diagram.png)
  
  </center>

Följande bild visar avgränsnings ytan, markerat i blått och hyres fordonets väg, vilket visas med en grön linje.

  ![Netstängsel-väg](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Krav 

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

För att slutföra stegen i den här självstudien måste du först skapa en resurs grupp i Azure Portal. Gör så här om du vill skapa en resurs grupp:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Resursgrupper**.
    
   ![Resursgrupper](./media/tutorial-iot-hub-maps/resource-group.png)

3. Under **resurs grupper**väljer du **Lägg till**.
    
   ![Lägg till resurs grupp](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Ange följande egenskaps värden:
    * **Prenumeration:** Välj din Azure-prenumeration.
    * **Resurs grupp:** Ange "ContosoRental" som resurs grupps namn.
    * **Region:** Välj en region för resurs gruppen.  

    ![Resurs grupps information](./media/tutorial-iot-hub-maps/resource-details.png)

    Välj **Granska + skapa**och välj sedan **skapa** på nästa sida.

### <a name="create-an-azure-maps-account"></a>Skapa ett Azure Maps-konto 

För att implementera affärs logik baserat på Azure Maps spatial analys måste vi skapa ett Azure Maps-konto i resurs gruppen som vi har skapat. Följ instruktionerna i [skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps) om du vill skapa en Azure Maps konto prenumeration med pris nivån S1 och följ stegen i [Hämta primär nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account) för att hämta den primära nyckeln för ditt konto. Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](how-to-manage-authentication.md).



### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

För att logga händelse data skapar vi ett **v2storage** -konto för generell användning i resurs gruppen "ContosoRental" för att lagra data som blobar. Om du vill skapa ett lagrings konto följer du anvisningarna i [skapa ett lagrings konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal). Därefter måste vi skapa en behållare för att lagra blobbar. Följ stegen nedan för att göra det:

1. I ditt lagrings konto navigerar du till behållare.

    ![blobar](./media/tutorial-iot-hub-maps/blobs.png)

2. Klicka på knappen behållare längst upp till vänster och namnge behållaren "contoso-hyr-logs" och klicka på "OK".

    ![BLOB-container](./media/tutorial-iot-hub-maps/blob-container.png)

3. Gå till bladet **åtkomst nycklar** i ditt lagrings konto och kopiera "lagrings kontots namn" och "åtkomst nyckel". De behövs i ett senare steg.

    ![åtkomst nycklar](./media/tutorial-iot-hub-maps/access-keys.png)


Nu har vi ett lagrings konto och en behållare för att logga händelse data. Nu ska vi skapa en IoT-hubb.

### <a name="create-an-iot-hub"></a>Skapa en IoT Hub

En IoT Hub är en hanterad tjänst i molnet som fungerar som en central meddelande hubb för dubbelriktad kommunikation mellan ett IoT-program och de enheter som hanteras av den. Skapa ett IoT Hub i resurs gruppen "ContosoRental" för att dirigera meddelanden om telemetri till en Event Grid. Konfigurera en meddelande vägs integrering där vi ska filtrera meddelanden baserat på bil status. Vi kommer också att skicka meddelanden om enhets telemetri till Event Grid när bilen flyttas.

> [!Note] 
> IoT Hub funktioner för att publicera händelser på enhets telemetri på Event Grid finns i offentlig för hands version. Offentliga för hands versions funktioner är tillgängliga i alla regioner utom **östra USA, västra USA, Västeuropa, Azure Government, Azure Kina 21Vianet** och **Azure Germany**. 

Skapa en IoT-hubb genom att följa stegen i [avsnittet Skapa en IoT Hub](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).


### <a name="register-a-device"></a>Registrera en enhet 

För att kunna ansluta till den IoT Hub måste en enhet registreras. Följ stegen nedan om du vill registrera en enhet med IoT Hub:

1. Klicka på bladet "IoT-enheter" i IoT Hub och klicka på "nytt".

    ![Lägg till enhet](./media/tutorial-iot-hub-maps/add-device.png)

2. På sidan Skapa en enhet namnger du din IoT-enhet och klickar på Spara.
    
    ![registrera – enhet](./media/tutorial-iot-hub-maps/register-device.png)


## <a name="upload-geofence"></a>Ladda upp gräns

Vi använder [Postman-programmet](https://www.getpostman.com) för att [överföra den här gränsen](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) till Azure Maps tjänsten med hjälp av Azure Maps data överförings-API: et. Alla händelser när bilen är utanför det här avgränsnings felet loggas.

Öppna Postman-appen och följ stegen nedan för att ladda upp den här gränsen med hjälp av Azure Maps, API för data överföring.  

1. I Postman-appen klickar du på ny | Skapa ny och välj begäran. Ange ett namn för Request (Begäran) för Upload geofence data (Ladda upp geofence-data), välj en samling eller en mapp att spara den i och klicka på Save (Spara).

    ![Ladda upp geofences med hjälp av Postman](./media/tutorial-iot-hub-maps/postman-new.png)

2. Välj POST HTTP-metod på fliken Builder (Byggare) och ange följande URL för att göra en POST-begäran.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Värdet "interjson" mot `dataFormat`-parametern i URL-sökvägen representerar formatet för de data som laddas upp.

3. Klicka på **Params** och ange följande nyckel/värde-par som ska användas för POST-begäran-URL. Ersätt prenumerations nyckel värde med din Azure Maps nyckel.
   
    ![Key-Value-parametrar för Postman](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Klicka på **brödtext** och välj sedan **RAW** -indataformat och välj **JSON (program/text)** som indata-format i list rutan. Öppna JSON-datafilen [här](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)och kopiera JSON i avsnittet brödtext som de data som ska överföras och klicka på **Skicka**.
    
    ![publicera data](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Granska svarshuvuden. Vid en lyckad begäran kommer **plats** huvudet innehålla status-URI för att kontrol lera den aktuella statusen för uppladdnings förfrågan. Status-URI: n kommer att ha följande format. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Kopiera din status-URI och Lägg till en `subscription-key`-parameter till den med dess värde som din Azure Maps konto prenumerations nyckel. Status-URI-formatet ska vara som det som anges nedan:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. För att hämta `udId` öppnar du en ny flik i Postman-appen och väljer Hämta HTTP-metod på fliken Builder och gör en GET-begäran i status-URI: n. Om din data uppladdning lyckades får du en udId i svars texten. Kopiera udId för senare användning.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


Nu ska vi skapa en Azure-funktion i resurs gruppen "ContosoRental" och sedan konfigurera en meddelande väg i IoT Hub för att filtrera enhets telemetri meddelanden.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Skapa en Azure-funktion och Lägg till en Event Grid-prenumeration

Azure Functions är en server lös beräknings tjänst som gör det möjligt för oss att köra kod på begäran utan att uttryckligen behöva etablera eller hantera beräknings infrastruktur. Ta en titt på [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) -dokumentationen om du vill veta mer om Azure Functions. Logiken som vi implementerar i funktionen använder de plats data som kommer från enheten för enhets telemetri för att bedöma den logiska statusen. Om ett fordon hamnar utanför den yttre gränsen kommer funktionen att samla in mer information, t. ex. adressen till platsen via [Hämta Sök adress omvänt API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) som översätter en specifik plats till en human adress. All relevant händelse information lagras sedan i BLOB Store. Steg 5 nedan pekar på den körbara kod som implementerar en sådan logik. Följ stegen nedan för att skapa en Azure-funktion som skickar data loggar till BLOB-behållaren i lagrings kontot och lägger till en Event Grid-prenumeration på den.

1. I instrument panelen Azure Portal väljer du skapa en resurs. Välj **Beräkna** i listan över tillgängliga resurs typer och välj sedan **Function app**.

    ![Skapa-resurs](./media/tutorial-iot-hub-maps/create-resource.png)

2. På sidan Skapa **Funktionsapp** namnger du din Function-app. Under **resurs grupp**väljer du **Använd befintlig**och sedan "ContosoRental" i list rutan. Välj ".NET Core" som körnings stack. Under **lagring**väljer du **Använd befintlig**, väljer "contosorentaldata" i list rutan och väljer sedan **Granska + skapa**.
    
    ![Skapa – app](./media/tutorial-iot-hub-maps/rental-app.png)

2. Granska programmets information och välj "skapa".

3. När appen har skapats måste vi lägga till en funktion i den. Gå till Function-appen och klicka på **ny funktion** för att lägga till en funktion, Välj **på Portal** som utvecklings miljö och välj sedan **Fortsätt**.

    ![Skapa – funktion](./media/tutorial-iot-hub-maps/function.png)

4. Välj **Fler mallar** och klicka på **Slutför och Visa mallar**. 

5. Välj mallen med en **Azure Event Grid-utlösare**. Installera tillägg om du uppmanas till det, namnge funktionen och välj **skapa**.

    ![funktion-mall](./media/tutorial-iot-hub-maps/eventgrid-funct.png)

6. Kopiera [c#-koden](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) till din funktion och klicka på **Spara**.
 
7. Ersätt följande parametrar i c#-skriptet:
    * Ersätt **SUBSCRIPTION_KEY** med din primära prenumerations nyckel för Azure Maps-kontot.
    * Ersätt **UDID** med UDID för den avgränsning som du laddade upp, 
    * Funktionen **CreateBlobAsync** i skriptet skapar en BLOB per händelse i data lagrings kontot. Ersätt **ACCESS_KEY**, **ACCOUNT_NAME** och **STORAGE_CONTAINER_NAME** med lagrings kontots åtkomst nyckel, konto namn och data lagrings behållare.

10. Klicka på **Lägg till Event Grid prenumeration**.
    
    ![Lägg till händelse-rutnät](./media/tutorial-iot-hub-maps/add-egs.png)

11. Fyll i prenumerations information, under **information om händelse prenumerations** namn din prenumeration och för händelse schema väljer du Event Grid schema. Under **ämnes information** väljer du "Azure IoT Hub Accounts" som typ av ämne. Välj samma prenumeration som du använde för att skapa resurs gruppen, välj "ContosoRental" som "resurs grupp" och välj den IoT Hub som du skapade som "resurs". Välj **telemetri för enhet** som händelse typ. När du har valt dessa alternativ visas "ämnes typ"-ändringen till "IoT Hub" automatiskt.

    ![händelse – rutnät – prenumeration](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrera händelser med IoT Hub meddelanderoutning

När du har lagt till en Event Grid-prenumeration i Azure-funktionen kan du nu se en standard meddelande väg för att Event Grid i IoT Hub **meddelande cirkulations** bladet. Med meddelanderoutning kan du dirigera olika data typer, t. ex. meddelanden om telemetri, enhetens livs cykel händelser och enhetens dubbla ändrings händelser till olika slut punkter. Mer information om IoT Hub-meddelanderoutning finns i [använda IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)meddelanderoutning.

![hubb – tex-Route](./media/tutorial-iot-hub-maps/hub-route.png)

I vårt exempel scenario vill vi filtrera ut alla meddelanden där hyr fordonet flyttar. För att det ska gå att publicera sådana telemetri händelser till Event Grid ska vi använda routnings frågan för att filtrera händelserna där egenskapen `Engine` är **"på"** . Det finns olika sätt att fråga IoT-meddelanden från enhet till molnet för att lära dig mer om syntax för meddelanderoutning, se [IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax)meddelanderoutning. Om du vill skapa en cirkulations fråga, klickar du på **RouteToEventGrid** -vägen och ersätter **Oper. åga** med **"motor =" på "** och klickar på **Spara**. Nu kan IoT Hub bara publicera enhets telemetri där motorn är på.

![hubb – tex-filter](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Skicka telemetridata till IoT Hub

När vår Azure-funktion är igång kan vi nu skicka telemetridata till IoT Hub som dirigerar den till Event Grid. Vi använder ett c#-program för att simulera plats data för en fordons enhet i en hyr bil. Om du vill köra programmet måste du ha .NET Core SDK 2.1.0 eller senare på din utvecklings dator. Följ stegen nedan för att skicka simulerade telemetridata till IoT Hub.

1. Ladda ned c#-projektet [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) . 

2. Öppna simulatedCar.cs-filen i valfri text redigerare och Ersätt värdet för `connectionString` med den som du sparade när du registrerade enheten och spara ändringarna i filen.
 
3. Kontrol lera att du har .NET Core installerat på datorn. I det lokala terminalfönstret går du till rotmappen i C# projektet och kör följande kommando för att installera de nödvändiga paketen för det simulerade enhets programmet:
    
    ```cmd/sh
    dotnet restore
    ```

4. I samma Terminal kör du följande kommando för att skapa och köra ett simulerings program för hyr bil:

    ```cmd/sh
    dotnet run
    ```

  Den lokala terminalen bör se ut som den som anges nedan.

  ![Terminal-utdata](./media/tutorial-iot-hub-maps/terminal.png)

Om du öppnar Blob storage-behållaren nu bör du se fyra blobbar för platser där fordonet låg utanför gränsen.

![Ange BLOB](./media/tutorial-iot-hub-maps/blob.png)

Kartan nedan visar fyra punkter där fordonet befinner sig utanför gränsen, loggas med regelbundna tidsintervall.

![överträdelse karta](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>Nästa steg

Information om Azure Maps-API: er som används i den här självstudien finns i:

* [Hämta omvänd söknings adress](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Hämta avgränsning](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

En fullständig lista över Azure Maps REST API: er finns i:

* [Azure Maps REST-API: er](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Mer information om IoT Plug and Play finns i:

* [IoT-Plug and Play](https://docs.microsoft.com/azure/iot-pnp)

Om du vill hämta en lista över enheter som är Azure-certifierade för IoT går du till:

* [Azure Certified-enheter](https://catalog.azureiotsolutions.com/)

Om du vill veta mer om hur du skickar enheter till molnbaserad telemetri och det andra sättet runt, se:

* [Skicka telemetri från en enhet](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

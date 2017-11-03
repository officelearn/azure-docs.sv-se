## <a name="typical-output"></a>Vanliga utdata

I följande exempel visas utdata skrivs till loggfilen av Hello World-exempel. Utdata formateras för att de ska vara enkla att läsa:

```json
[{
    "time": "Mon Apr 11 13:42:50 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:42:50 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:42:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:43:00 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:45:00 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>Kodfragment

I det här avsnittet beskrivs viktiga delar av koden i Hello\_World-exemplet.

### <a name="iot-edge-gateway-creation"></a>Skapa en IoT-Edge gateway

Om du vill skapa en gateway, implementera en *gateway-processen*. Det här programmet skapar den interna infrastrukturen (broker), laddar IoT kant-moduler och konfigurerar gateway-processen. IoT Edge innehåller funktionen **Gateway\_Create\_From\_JSON** för att du ska kunna starta en gateway från en JSON-fil. Att använda den **Gateway\_skapa\_från\_JSON** fungera, ange sökvägen till en JSON-fil som anger IoT kant-moduler för att läsa in.

Du hittar koden för gateway-processen i den *Hello World* exempel i den [main.c] [ lnk-main-c] fil. För att göra det enklare visas följande kodfragment som en förkortad version av gatewayprocesskoden. Det här exempelprogrammet skapar en gateway och väntar sedan på att användaren ska trycka på tangenten **RETUR** innan den monterar ned gatewayen.

```C
int main(int argc, char** argv)
{
    GATEWAY_HANDLE gateway;
    if ((gateway = Gateway_Create_From_JSON(argv[1])) == NULL)
    {
        printf("failed to create the gateway from JSON\n");
    }
    else
    {
        printf("gateway successfully created from JSON\n");
        printf("gateway shall run until ENTER is pressed\n");
        (void)getchar();
        Gateway_LL_Destroy(gateway);
    }
    return 0;
}
```

Inställningar för JSON-filen innehåller en lista över IoT kant moduler att läsa in och länkarna mellan moduler. Varje gräns för IoT-modul måste ange en:

* **module_name**: ett unikt namn för modulen.
* **loader**: en inläsare som anger hur önskad modul ska läsas in. Inläsare är en tilläggspunkt för inläsning av olika typer av moduler. IoT-Edge ger inläsare för användning med moduler som skrivits i interna C, Node.js, Java och .NET. Hello World-exempel endast använder inbyggd C inläsaren eftersom alla moduler i det här exemplet är dynamisk bibliotek som skrivits i C. Mer information om hur du använder IoT kant-moduler som skrivits i olika språk finns i [Node.js](https://github.com/Azure/iot-edge/blob/master/samples/nodejs_simple_sample/), [Java](https://github.com/Azure/iot-edge/tree/master/samples/java_sample), eller [.NET](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_binding_sample) prover.
    * **namnet**: namnet på inläsaren används för att läsa in modulen.
    * **entrypoint**: sökvägen till biblioteket som innehåller modulen. Det här biblioteket är en .so-fil på Linux, på Windows det här biblioteket är en DLL-fil. Den här registreringspunkten är specifik för den typ av inläsare som används. Startpunkten för Node.js-inläsaren är en JS-fil. Startpunkten för Java-inläsaren är en klassökväg och ett klassnamn. Startpunkten för .NET-inläsaren är ett namn på sammansättning och ett klassnamn.

* **args**: all konfigurationsinformation modulen behöver.

Följande kod visar JSON som används för att deklarera moduler Hello World-exempel för IoT-gränsen på Linux. Huruvida en modul kräver argument eller inte beror på modulens design. I det här exemplet använder loggningsmodulen ett argument som är sökvägen till utdatafilen och Hello\_World-modulen har inga argument.

```json
"modules" :
[
    {
        "name" : "logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/logger/liblogger.so"
        }
        },
        "args" : {"filename":"log.txt"}
    },
    {
        "name" : "hello_world",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/hello_world/libhello_world.so"
        }
        },
        "args" : null
    }
]
```

JSON-filen innehåller också länkarna mellan de moduler som skickas till den asynkrona meddelandekön. En länk har två egenskaper:

* **källan**: ett Modulnamn från den `modules` avsnittet eller `\*`.
* **mottagare**: ett modulnamn från avsnittet `modules`.

Varje länk definierar en meddelandeväg och -riktning. Meddelanden från den **källa** modulen levereras till den **sink** modul. Du kan ange den **källa** modulen `\*`, vilket indikerar att den **sink** modulen tar emot meddelanden från en modul.

Följande kod visar den JSON som används för att konfigurera länkar mellan moduler som används i Hello\_World-exemplet för Linux. Alla meddelanden som genereras av modul `hello_world` används av modul `logger`.

```json
"links":
[
    {
        "source": "hello_world",
        "sink": "logger"
    }
]
```

### <a name="helloworld-module-message-publishing"></a>Meddelandepublicering från Hello\_World-modulen

Du kan hitta koden som används av modulen Hello\_World för att publicera meddelanden i filen [”hello_world.c”][lnk-helloworld-c]. Kodfragmentet nedan visar en ändrad version med ytterligare kommentarer tillagda och viss felhanteringskod tas bort för att förenkla:

```C
int helloWorldThread(void *param)
{
    // create data structures used in function.
    HELLOWORLD_HANDLE_DATA* handleData = param;
    MESSAGE_CONFIG msgConfig;
    MAP_HANDLE propertiesMap = Map_Create(NULL);

    // add a property named "helloWorld" with a value of "from Azure IoT
    // Gateway SDK simple sample!" to a set of message properties that
    // will be appended to the message before publishing it. 
    Map_AddOrUpdate(propertiesMap, "helloWorld", "from Azure IoT Gateway SDK simple sample!")

    // set the content for the message
    msgConfig.size = strlen(HELLOWORLD_MESSAGE);
    msgConfig.source = HELLOWORLD_MESSAGE;

    // set the properties for the message
    msgConfig.sourceProperties = propertiesMap;

    // create a message based on the msgConfig structure
    MESSAGE_HANDLE helloWorldMessage = Message_Create(&msgConfig);

    while (1)
    {
        if (handleData->stopThread)
        {
            (void)Unlock(handleData->lockHandle);
            break; /*gets out of the thread*/
        }
        else
        {
            // publish the message to the broker
            (void)Broker_Publish(handleData->brokerHandle, helloWorldMessage);
            (void)Unlock(handleData->lockHandle);
        }

        (void)ThreadAPI_Sleep(5000); /*every 5 seconds*/
    }

    Message_Destroy(helloWorldMessage);

    return 0;
}
```

Hello\_world modulen bearbetar aldrig meddelanden som andra IoT kant-moduler som publicerar i Service broker. Detta gör implementeringen av meddelandets återanrop i modulen Hello\_World till en icke-alternativ funktion.

```C
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-processing"></a>Loggaren modulen meddelandebehandling

Loggningsmodulen tar emot meddelanden från den asynkrona meddelandekön och skriver dem till en fil. Den publicerar aldrig meddelanden. Därför anropar koden för loggningsmodulen aldrig funktionen **Broker_Publish**.

Den **Logger_Receive** fungera i den [logger.c] [ lnk-logger-c] filen är återanropet Service broker anropar för att leverera meddelanden till modulen meddelandeloggfiler. Kodfragmentet nedan visar en ändrad version med kommentarer tillagda och viss felhanteringskod tas bort för att förenkla:

```C
static void Logger_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{

    time_t temp = time(NULL);
    struct tm* t = localtime(&temp);
    char timetemp[80] = { 0 };

    // Get the message properties from the message
    CONSTMAP_HANDLE originalProperties = Message_GetProperties(messageHandle); 
    MAP_HANDLE propertiesAsMap = ConstMap_CloneWriteable(originalProperties);

    // Convert the collection of properties into a JSON string
    STRING_HANDLE jsonProperties = Map_ToJSON(propertiesAsMap);

    //  base64 encode the message content
    const CONSTBUFFER * content = Message_GetContent(messageHandle);
    STRING_HANDLE contentAsJSON = Base64_Encode_Bytes(content->buffer, content->size);

    // Start the construction of the final string to be logged by adding
    // the timestamp
    STRING_HANDLE jsonToBeAppended = STRING_construct(",{\"time\":\"");
    STRING_concat(jsonToBeAppended, timetemp);

    // Add the message properties
    STRING_concat(jsonToBeAppended, "\",\"properties\":"); 
    STRING_concat_with_STRING(jsonToBeAppended, jsonProperties);

    // Add the content
    STRING_concat(jsonToBeAppended, ",\"content\":\"");
    STRING_concat_with_STRING(jsonToBeAppended, contentAsJSON);
    STRING_concat(jsonToBeAppended, "\"}]");

    // Write the formatted string
    LOGGER_HANDLE_DATA *handleData = (LOGGER_HANDLE_DATA *)moduleHandle;
    addJSONString(handleData->fout, STRING_c_str(jsonToBeAppended);
}
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln får körde du en enkel IoT gräns-gatewayen som skriver meddelanden till en loggfil. Om du vill köra ett exempel som skickar meddelanden till IoT-hubb finns:

- [IoT-Edge – skicka meddelanden från enhet till moln med en simulerad enhet med hjälp av Linux][lnk-gateway-simulated-linux] 
- [IoT-Edge – skicka meddelanden från enhet till moln med en simulerad enhet med Windows][lnk-gateway-simulated-windows].


<!-- Links -->
[lnk-main-c]: https://github.com/Azure/iot-edge/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/iot-edge/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/iot-edge/blob/master/modules/logger/src/logger.c
[lnk-iot-edge]: https://github.com/Azure/iot-edge/
[lnk-gateway-simulated-linux]: ../articles/iot-hub/iot-hub-linux-iot-edge-simulated-device.md
[lnk-gateway-simulated-windows]: ../articles/iot-hub/iot-hub-windows-iot-edge-simulated-device.md
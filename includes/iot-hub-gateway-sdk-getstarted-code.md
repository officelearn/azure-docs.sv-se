## <a name="typical-output"></a>Vanliga utdata

Nedan visas ett exempel på utdata som skrivits till loggfilen av Hello World-exemplet. Utdata formateras för att de ska vara enkla att läsa:

```json
[{
    "time": "Mon Apr 11 13:48:07 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:48:48 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:48:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:01 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:04 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>Kodfragment

I det här avsnittet beskrivs viktiga delar av koden i Hello\_World-exemplet.

### <a name="gateway-creation"></a>Skapa en gateway

Utvecklaren måste skriva *gateway-processen*. Det här programmet skapar den interna infrastrukturen (asynkron meddelandekö), läser in moduler och ställer in så att allt ska fungera korrekt. SDK innehåller funktionen **Gateway\_Create\_From\_JSON** för att du ska kunna starta en gateway från en JSON-fil. För att kunna använda funktionen **Gateway\_Create\_From\_JSON** måste du ange sökvägen till en JSON-fil som anger vilka moduler som ska läsas in.

Du hittar koden för gateway-processen i Hello World-exemplet i filen [main.c][lnk-main-c]. För att göra det enklare visas följande kodfragment som en förkortad version av gatewayprocesskoden. Det här exempelprogrammet skapar en gateway och väntar sedan på att användaren ska trycka på tangenten **RETUR** innan den monterar ned gatewayen.

```c
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

JSON-inställningsfilen innehåller en lista med moduler för inläsning samt länkarna mellan modulerna. Varje modul måste ange:

* **module_name**: ett unikt namn för modulen.
* **loader**: en inläsare som anger hur önskad modul ska läsas in. Inläsare är en tilläggspunkt för inläsning av olika typer av moduler. Vi tillhandahåller inläsare för användning med moduler som skrivits i intern C, Node.js, Java och .NET. I Hello World-exemplet används bara den interna C-inläsaren eftersom alla moduler i det här exemplet är dynamiska bibliotek skrivna i C. Mer information om hur du använder moduler skrivna med olika språk finns i [Node.js](https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/nodejs_simple_sample/)-, [Java](https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/java_sample)- och [.NET](https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/dotnet_binding_sample)-exemplen.
    * **name**: namnet på inläsaren som används för att läsa in modulen.
    * **entrypoint**: sökvägen till biblioteket som innehåller modulen. För Linux är det här biblioteket en SO-fil, i Windows är det en DLL-fil. Den här registreringspunkten är specifik för den typ av inläsare som används. Startpunkten för Node.js-inläsaren är en .js-fil. Registreringspunkten för Java-inläsaren är en klassökväg plus ett klassnamn. Registreringspunkten för .NET-inläsaren är ett sammansättningsnamn plus ett klassnamn.

* **args**: all konfigurationsinformation modulen behöver.

Följande kod visar den JSON som används för att deklarera alla modulerna för Hello World-exemplet för Linux. Huruvida en modul kräver argument eller inte beror på modulens design. I det här exemplet använder loggningsmodulen ett argument som är sökvägen till utdatafilen och Hello\_World-modulen har inga argument.

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

* **källa**: ett modulnamn från avsnittet `modules` eller ”\*”.
* **mottagare**: ett modulnamn från avsnittet `modules`.

Varje länk definierar en meddelandeväg och -riktning. Meddelanden från modul `source` levereras till modul `sink`. `source` kan anges till ”\*”, vilket visar att meddelanden från alla moduler tas emot av `sink`.

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

```c
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

### <a name="helloworld-module-message-processing"></a>Meddelandebearbetning från Hello\_World-modulen

Modulen Hello\_World behandlar aldrig meddelanden som andra moduler publicerar till den asynkrona meddelandekön. Detta gör implementeringen av meddelandets återanrop i modulen Hello\_World till en icke-alternativ funktion.

```c
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-publishing-and-processing"></a>Loggningsmodulens meddelandepublicering och bearbetning

Loggningsmodulen tar emot meddelanden från den asynkrona meddelandekön och skriver dem till en fil. Den publicerar aldrig meddelanden. Därför anropar koden för loggningsmodulen aldrig funktionen **Broker_Publish**.

Funktionen **Logger_Recieve** i filen [logger.c][lnk-logger-c] är det återanrop som den asynkrona meddelandekön anropar för att skicka meddelanden till loggningsmodulen. Kodfragmentet nedan visar en ändrad version med kommentarer tillagda och viss felhanteringskod tas bort för att förenkla:

```c
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

Mer information om hur du använder IoT Gateway SDK finns i följande artiklar:

* [IoT Gateway SDK – skicka enhet-till-moln-meddelanden med en simulerad enhet med Linux][lnk-gateway-simulated].
* [Azure IoT Gateway SDK][lnk-gateway-sdk] på GitHub.

<!-- Links -->
[lnk-main-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/logger/src/logger.c
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-gateway-simulated]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md
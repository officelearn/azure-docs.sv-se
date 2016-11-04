## Vanliga utdata
Nedan visas ett exempel på utdata som skrivits till loggfilen av Hello World-exemplet. Tecken för ny rad och flik har lagts till för anpassning:

```
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

## Kodfragment
Det här avsnittet beskriver viktiga delar av koden i Hello World-exemplet.

### Skapa en gateway
Utvecklaren måste skriva *gateway-processen*. Det här programmet skapar den interna infrastrukturen (asynkron meddelandekö), läser in moduler och ställer in så att allt ska fungera korrekt. SDK innehåller funktionen **Gateway_Create_From_JSON** för att du ska kunna starta en gateway från en JSON-fil. För att använda funktionen **Gateway_Create_From_JSON** måste du ange sökvägen till en JSON-fil som anger vilka moduler som ska läsas in. 

Du hittar koden för gateway-processen i Hello World-exemplet i filen [main.c][lnk-main-c]. För att göra det enklare, visas fragmentet nedan som en förkortad version av gateway-processkoden. Det här programmet skapar en gateway och väntar sedan på att användaren ska trycka på tangenten **RETUR** innan den monterar ned gatewayen. 

```
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

JSON-inställningsfilen innehåller en lista med moduler att läsa in. Varje modul måste ange:

* **module_name**: ett unikt namn för modulen.
* **module_path**: sökvägen till biblioteket som innehåller modulen. För Linux är detta en SO-fil, i Windows är detta en DLL-fil.
* **args**: all konfigurationsinformation modulen behöver.

JSON-filen innehåller också länkar mellan moduler som kommer att skickas till den asynkrona meddelandekön. En länk har två egenskaper:

* **källa**: ett modulnamn från avsnittet `modules` eller ”\*”.
* **mottagare**: ett modulnamn från avsnittet `modules`

Varje länk definierar en meddelandeväg och -riktning. Meddelanden från modul `source` ska levereras till modul `sink`. `source` kan anges till ”\*”, som anger att meddelanden från alla moduler tas emot av `sink`.

I följande exempel visas JSON-inställningsfilen som används för att konfigurera Hello World-exemplet på Linux. Alla meddelanden som genereras av modul `hello_world` används av modul `logger`. Om en modul kräver ett argument eller inte är beroende av modulens design. I det här exemplet kräver loggningsmodulen ett argument som är sökväg till utdatafilen och Hello World-modulen kräver inte några argument:

```
{
    "modules" :
    [ 
        {
            "module name" : "logger",
            "module path" : "./modules/logger/liblogger_hl.so",
            "args" : {"filename":"log.txt"}
        },
        {
            "module name" : "hello_world",
            "module path" : "./modules/hello_world/libhello_world_hl.so",
            "args" : null
        }
    ],
    "links" :
    [
        {
            "source" : "hello_world",
            "sink" : "logger"
        }
    ]
}
```

### Meddelandepublicering från Hello World-modulen
Du kan hitta koden som används av modulen "hello world" för att publicera meddelanden i filen ['hello_world.c'][lnk-helloworld-c]. Fragmentet nedan visar en ändrad version med ytterligare kommentarer och viss felhanteringskod tas bort för att förenkla:

```
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

### Meddelandebearbetning från Hello World-modulen
Modulen Hello World måste aldrig bearbeta några meddelanden som andra moduler publicerar till den asynkrona meddelandekön. Detta gör implementeringen av meddelandets återanrop i modulen Hello World till en icke-alternativ funktion.

```
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### Loggningsmodulens meddelandepublicering och bearbetning
Loggningsmodulen tar emot meddelanden från den asynkrona meddelandekön och skriver dem till en fil. Den publicerar aldrig meddelanden. Därför anropar koden för loggningsmodulen aldrig funktionen **Broker_Publish**.

Funktionen **Logger_Recieve** i filen [logger.c][lnk-logger-c] är det återanrop som den asynkrona meddelandekön anropar för att skicka meddelanden till loggningsmodulen. Fragmentet nedan visar en ändrad version med ytterligare kommentarer och viss felhanteringskod tas bort för att förenkla:

```
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

## Nästa steg
Mer information om hur du använder Gateway-SDK finns i följande:

* [IoT Gateway SDK – skicka enhet-till-moln-meddelanden med en simulerad enhet med Linux][lnk-gateway-simulated].
* [Azure IoT Gateway SDK][lnk-gateway-sdk] på GitHub.

<!-- Links -->
[lnk-main-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/logger/src/logger.c
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-gateway-simulated]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md

<!--HONumber=Sep16_HO4-->



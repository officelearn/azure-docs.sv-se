---
title: Mata in telemetri från IoT Hub
titleSuffix: Azure Digital Twins
description: Se hur du matar in meddelanden för telemetri från IoT Hub.
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7c73f007f85a963a09de4e05222082fd52f784c0
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131573"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Mata in IoT Hub telemetri i Azure Digitals, dubbla

Digitala Azure-enheter drivs med data från IoT-enheter och andra källor. En gemensam källa för enhets data som ska användas i Azure Digitals dubbla är [IoT Hub](../iot-hub/about-iot-hub.md).

Under för hands versionen är processen för att mata in data i Azure Digitals, att skapa en extern beräknings resurs, till exempel en [Azure-funktion](../azure-functions/functions-overview.md), som tar emot data och använder [DigitalTwins-API: er](how-to-use-apis-sdks.md) för att ange egenskaper eller brand släcknings händelser på [digitala dubbla](concepts-twins-graph.md) . 

Det här dokumentet vägleder dig genom processen för att skriva en Azure-funktion som kan mata in telemetri från IoT Hub.

## <a name="example-telemetry-scenario"></a>Exempel scenario för telemetri

Den här instruktionen beskriver hur du skickar meddelanden från IoT Hub till Azures digitala dubbla, med hjälp av en Azure-funktion. Det finns många möjliga konfigurationer och matchnings strategier som du kan använda för detta, men exemplet för den här artikeln innehåller följande delar:
* En termometer-enhet i IoT Hub, med ett känt enhets-ID.
* En digital enhet som representerar enheten, med ett matchande ID
* En digital som representerar ett rum

> [!NOTE]
> I det här exemplet används en enkel ID-matchning mellan enhets-ID: t och motsvarande digitala dubbla ID, men det är möjligt att ge mer sofistikerade mappningar från enheten till dess dubbla (till exempel med en mappnings tabell).

När en händelse för temperatur telemetri skickas av termometer-enheten bör *rummets* *temperatur* -egenskap uppdateras. För att göra detta kommer du att mappa från en telemetri-händelse på en enhet till en egenskaps inställning på den digitala, dubbla. Du kommer att använda Topology-information från den [dubbla grafen](concepts-twins-graph.md) för att hitta *rummet* , och sedan kan du ange den dubbla egenskapen. I andra scenarier kanske användarna vill ange en egenskap för matchningen av dubbla (i det här exemplet är den dubbla med ID: t *123*). Med Azure Digitals dubblare får du stor flexibilitet när du ska bestämma hur telemetridata ska mappas till dubbla. 

Det här scenariot beskrivs i ett diagram nedan:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="En IoT Hub-enhet skickar temperatur telemetri genom IoT Hub, Event Grid eller system ämnen till en Azure-funktion, som uppdaterar en temperatur egenskap på dubbla i Azures digitala dubbla." border="false":::

## <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter med det här exemplet måste du uppfylla följande krav.
1. Skapa en IoT-hubb. Anvisningar finns i avsnittet *skapa en IoT Hub* i [den här IoT Hub snabb](../iot-hub/quickstart-send-telemetry-cli.md) starten.
2. Skapa minst en Azure-funktion för att bearbeta händelser från IoT Hub. Se [*anvisningar: Konfigurera en Azure-funktion för bearbetning av data*](how-to-create-azure-function.md) för att skapa en grundläggande Azure-funktion som kan ansluta till Azure Digitals dubbla och anropa Azure Digitals dubbla API-funktioner. Resten av den här instruktionen bygger på den här funktionen.
3. Konfigurera ett händelse mål för Hub-data. I [Azure Portal](https://portal.azure.com/)navigerar du till IoT Hub-instansen. Under *händelser*skapar du en prenumeration för Azure-funktionen. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Azure Portal: lägga till en händelse prenumeration":::

4. På sidan *Skapa händelse prenumeration* fyller du i fälten enligt följande:
   * Under *information om händelse prenumerationer*namnger du den prenumeration som du vill ha
   * Under *händelse typer*väljer du *enhets telemetri* som händelse typ för att filtrera på
      - Lägg till filter till andra händelse typer, om du vill.
   * Under *information om slut punkt*väljer du din Azure-funktion som en slut punkt

## <a name="create-an-azure-function-in-visual-studio"></a>Skapa en Azure-funktion i Visual Studio

I det här avsnittet används samma start steg i Visual Studio och Azure Function Skeleton från [*anvisningar: Konfigurera en Azure-funktion för bearbetning av data*](how-to-create-azure-function.md). Skeleton hanterar autentisering och skapar en tjänst klient som är redo att bearbeta data och anropa Azure Digitals dubbla API: er som svar. 

Hjärtat i Skeleton-funktionen är följande:

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

I de steg som följer ska du lägga till en speciell kod i den för att bearbeta IoT telemetri-händelser från IoT Hub.  

## <a name="add-telemetry-processing"></a>Lägg till telemetri-bearbetning

Telemetri händelser levereras i form av meddelanden från enheten. Det första steget vid tillägg av telemetri – bearbetnings kod extraherar relevant del av enhets meddelandet från Event Grid-händelsen. 

Olika enheter kan strukturera sina meddelanden på olika sätt, så koden för det här steget beror på den anslutna enheten. 

Följande kod visar ett exempel på en enkel enhet som skickar telemetri som JSON. Exemplet extraherar enhets-ID: t för enheten som skickade meddelandet, samt temperatur svärdet.

```csharp
JObject job = eventGridEvent.Data as JObject;
string devid = (string)job["systemProperties"].ToObject<JObject>().Property("IoT-hub-connection-device-ID").Value;
double temp = (double)job["body"].ToObject<JObject>().Property("temperature").Value;
```

Kom ihåg att syftet med den här övningen är att uppdatera temperaturen i ett *rum* i det dubbla diagrammet. Det innebär att vår mål för meddelandet inte är den digitala dubbla som är kopplad till den här enheten, men *rummets* dubbla som är överordnad. Du kan hitta det överordnade dubbla med det enhets-ID-värde som du extraherade från telemetri-meddelandet med hjälp av koden ovan.

Det gör du genom att använda Azure Digitals dubbla API: er för att komma åt inkommande relationer till enheten – som motsvarar dubbla (som i det här fallet har samma ID som enheten). Från den inkommande relationen kan du hitta ID: t för den överordnade med kodfragmentet nedan.

Kodfragmentet nedan visar hur du hämtar inkommande relationer av en dubbel:

```csharp
AsyncPageable<IncomingRelationship> res = client.GetIncomingRelationshipsAsync(twin_id);
await foreach (IncomingRelationship irel in res)
{
    Log.Ok($"Relationship: {irel.RelationshipName} from {irel.SourceId} | {irel.RelationshipId}");
}
```

Överordnad för din dubbla är i relationens *SourceId* -egenskap.

Det är ganska vanligt att en modell av en enhet som representerar en enhet bara har en enda inkommande relation. I det här fallet kan du välja den första relationen (och endast) som returneras. Om dina modeller tillåter flera typer av relationer till denna dubbla, kan du behöva ange ytterligare för att välja mellan flera inkommande relationer. Ett vanligt sätt att göra detta är att välja relationen av `RelationshipName` . 

När du har ID: t för den överordnade enhet som representerar *rummet*, kan du "korrigering" (göra uppdateringar till) som är dubbla. Använd följande kod för att göra detta:

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", temp);
try
{
    await client.UpdateDigitalTwinAsync(twin_id, uou.Serialize());
    Log.Ok($"Twin '{twin_id}' updated successfully!");
}
...
```

### <a name="full-azure-function-code"></a>Fullständig Azure Function-kod

Med hjälp av koden från de tidigare exemplen är följande hela Azure-funktionen i sammanhanget:

```csharp
[FunctionName("ProcessHubToDTEvents")]
public async void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
{
    // After this is deployed, in order for this function to be authorized on Azure Digital Twins APIs,
    // you'll need to turn the Managed Identity Status to "On", 
    // grab the Object ID of the function, and assign the "Azure Digital Twins Owner (Preview)" role to this function identity.

    DigitalTwinsClient client = null;
    //log.LogInformation(eventGridEvent.Data.ToString());
    // Authenticate on Azure Digital Twins APIs
    try
    {
        
        ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
        client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
        log.LogInformation($"ADT service client connection created.");
    }
    catch (Exception e)
    {
        log.LogError($"ADT service client connection failed. " + e.ToString());
        return;
    }

    if (client != null)
    {
        try
        {
            if (eventGridEvent != null && eventGridEvent.Data != null)
            {
                #region Open this region for message format information
                // Telemetry message format
                //{
                //  "properties": { },
                //  "systemProperties": 
                // {
                //    "iothub-connection-device-id": "thermostat1",
                //    "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
                //    "iothub-connection-auth-generation-id": "637199981642612179",
                //    "iothub-enqueuedtime": "2020-03-18T18:35:08.269Z",
                //    "iothub-message-source": "Telemetry"
                //  },
                //  "body": "eyJUZW1wZXJhdHVyZSI6NzAuOTI3MjM0MDg3MTA1NDg5fQ=="
                //}
                #endregion

                // Reading deviceId from message headers
                log.LogInformation(eventGridEvent.Data.ToString());
                JObject job = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                string deviceId = (string)job["systemProperties"]["iothub-connection-device-id"];
                log.LogInformation($"Found device: {deviceId}");

                // Extracting temperature from device telemetry
                byte[] body = System.Convert.FromBase64String(job["body"].ToString());
                var value = System.Text.ASCIIEncoding.ASCII.GetString(body);
                var bodyProperty = (JObject)JsonConvert.DeserializeObject(value);
                var temperature = bodyProperty["Temperature"];
                log.LogInformation($"Device Temperature is:{temperature}");

                // Update device Temperature property
                await AdtUtilities.UpdateTwinProperty(client, deviceId, "/Temperature", temperature, log);

                // Find parent using incoming relationships
                string parentId = await AdtUtilities.FindParent(client, deviceId, "contains", log);
                if (parentId != null)
                {
                    await AdtUtilities.UpdateTwinProperty(client, parentId, "/Temperature", temperature, log);
                }

            }
        }
        catch (Exception e)
        {
            log.LogError($"Error in ingest function: {e.Message}");
        }
    }
}
```

Funktionen Utility för att hitta inkommande relationer:
```csharp
public static async Task<string> FindParent(DigitalTwinsClient client, string child, string relname, ILogger log)
{
    // Find parent using incoming relationships
    try
    {
        AsyncPageable<IncomingRelationship> rels = client.GetIncomingRelationshipsAsync(child);

        await foreach (IncomingRelationship ie in rels)
        {
            if (ie.RelationshipName == relname)
                return (ie.SourceId);
        }
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error in retrieving parent:{exc.Status}:{exc.Message}");
    }
    return null;
}
```

Och funktionen Utility för att korrigera den dubbla:
```csharp
public static async Task UpdateTwinProperty(DigitalTwinsClient client, string twinId, string propertyPath, object value, ILogger log)
{
    // If the twin does not exist, this will log an error
    try
    {
        // Update twin property
        UpdateOperationsUtility uou = new UpdateOperationsUtility();
        uou.AppendAddOp(propertyPath, value);
        await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error:{exc.Status}/{exc.Message}");
    }
}
```

Nu har du en Azure-funktion som är utrustad att läsa och tolka de scenario data som kommer från IoT Hub.

## <a name="debug-azure-function-apps-locally"></a>Felsöka Azure Function Apps lokalt

Det går att felsöka Azure Functions med en Event Grid-utlösare lokalt. Mer information om detta finns i [*felsöka Event Grid utlösare lokalt*](../azure-functions/functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Nästa steg

Läs om data ingress och utgånget med Azure Digitals:
* [*Koncept: integrering med andra tjänster*](concepts-integration.md)

---
title: Förstå Azure IoT Hub meddelanderoutning | Microsoft Docs
description: Developer guide – hur du använder meddelanderoutning för att skicka meddelanden från enheten till molnet. Innehåller information om hur du skickar telemetri såväl som icke-telemetridata.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: dc5bfe6b431659b7b99140eb29a0e64922a42275
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576352"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Använd IoT Hub meddelanderoutning för att skicka enhet-till-moln-meddelanden till olika slutpunkter

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Meddelanderoutning kan du skicka meddelanden från dina enheter till molntjänster i ett automatiserade, skalbart och tillförlitligt sätt. Meddelanderoutning kan användas för: 

* **Skicka meddelanden från enheten telemetri samt händelser** nämligen Livscykelhändelser för enhet och enhetstvillingen ändra händelser till inbyggda – slutpunkt och anpassade slutpunkter. Lär dig mer om [routning slutpunkter](#routing-endpoints).

* **Filtrera data innan du skicka det till olika slutpunkter** genom att använda komplexa frågor. Meddelanderoutning kan du fråga på meddelandeegenskaperna och meddelandetexten som enheten twin taggar och tvillingegenskaper. Läs mer om hur du använder [frågor i meddelanderoutning](iot-hub-devguide-routing-query-syntax.md).

IoT Hub måste ha skrivbehörighet för dessa tjänstslutpunkter för meddelanderoutning för att fungera. Om du konfigurerar dina slutpunkter via Azure portal, läggs behörigheterna som krävs för dig. Kontrollera att du konfigurerar dina tjänster för att stödja det förväntade dataflödet. När du först konfigurera din IoT-lösning kan du behöva övervaka din ytterligare slutpunkter och gör eventuella ändringar för den faktiska belastningen.

IoT-hubben som definierar en [vanligt format](iot-hub-devguide-messages-construct.md) för alla enhet till moln-meddelanden för samverkan mellan protokoll. Om ett meddelande matchar flera routningstabeller som pekar på samma slutpunkt, IoT-hubb levererar meddelanden till denna slutpunkt bara en gång. Därför behöver du inte konfigurera deduplicering på din Service Bus-kö eller ämne. I partitionerade köer garanterar partition tillhörighet ordningsföljd för meddelanden. Använd den här självstudiekursen för att lära dig hur du [konfigurera meddelanderoutning](tutorial-routing.md).

## <a name="routing-endpoints"></a>Routning slutpunkter

En IoT-hubb har en inbyggd-i-standardslutpunkten (**meddelanden/händelser**) som är kompatibel med Event Hubs. Du kan skapa [anpassade slutpunkter](iot-hub-devguide-endpoints.md#custom-endpoints) skicka meddelanden till genom att länka andra tjänster i din prenumeration till IoT Hub. IoT Hub stöder för närvarande följande tjänster som anpassade slutpunkter:

### <a name="built-in-endpoint"></a>Inbyggd slutpunkt

Du kan använda standard [Event Hubs-integrering och SDK: er](iot-hub-devguide-messages-read-builtin.md) att ta emot meddelanden från enheten till molnet från den inbyggda slutpunkten (**meddelanden/händelser**). När du har skapat en väg, stoppar data flöda till inbyggda-i-slutpunkten såvida inte en väg skapas till denna slutpunkt.

### <a name="azure-blob-storage"></a>Azure Blob Storage

IoT-hubb har stöd för skrivning av data till Azure Blob Storage i den [Apache Avro](https://avro.apache.org/) samt JSON-format. Möjligheten att koda JSON-format finns i förhandsversion i alla regioner som IoT Hub inte är tillgängligt i östra USA, västra USA och Västeuropa. Standardvärdet är AVRO. Kodningsformatet kan anges endast när bloblagringsslutpunkt konfigureras. Formatet kan inte redigeras för en befintlig slutpunkt. När du använder JSON-kodning, måste du ange contentType till JSON och contentEncoding till UTF-8 i meddelandet [Systemegenskaper](iot-hub-devguide-routing-query-syntax.md#system-properties). Du kan välja Kodningsformatet med hjälp av IoT Hub Create eller Update REST API, särskilt de [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), Azure-portalen [Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest) eller [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0). Följande diagram visar hur du väljer Kodningsformatet i Azure Portal.

![BLOB storage endpoint kodning](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT Hub slår ihop meddelanden och skriver data till en blob när batchen når en viss storlek eller en viss tidsperiod har gått ut. IoT Hub som standard följande namngivningskonvention för filen:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Du kan använda alla filnamnskonvention, men du måste använda alla listade token. IoT Hub skriver till en tom blob om det finns inga data att skriva.

När routning till blob storage, rekommenderar vi ta blobar och sedan iterera över dem, så läses alla behållare utan att göra några antaganden för partitionen. Partitionsintervall potentiellt kan ändra under en [Microsoft-initierad redundans](iot-hub-ha-dr.md#microsoft-initiated-failover) eller IoT-hubb [manuell redundans](iot-hub-ha-dr.md#manual-failover-preview). Du kan använda den [lista Blobbar API](https://docs.microsoft.com/rest/api/storageservices/list-blobs) att räkna upp listan över blobar. Se följande exempel som vägledning.

   ```csharp
        public void ListBlobsInContainer(string containerName, string iothub)
        {
            var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
            var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
            if (cloudBlobContainer.Exists())
            {
                var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
                foreach (IListBlobItem item in results)
                {
                    Console.WriteLine(item.Uri);
                }
            }
        }
   ```

### <a name="service-bus-queues-and-service-bus-topics"></a>Service Bus-köer och Service Bus-ämnen

Service Bus-köer och ämnen som används som IoT Hub-slutpunkter inte får ha **sessioner** eller **dubblettidentifiering** aktiverat. Om något av dessa alternativ är aktiverade ändpunkt som **tillbaka** i Azure-portalen.

### <a name="event-hubs"></a>Event Hubs

Du kan också vidarebefordra data till anpassade slutpunkter av typen Event Hubs förutom kompatibel slutpunkt inbyggda – Event Hubs. 

## <a name="reading-data-that-has-been-routed"></a>Läsning av data som har dirigerats

Du kan konfigurera en väg genom att följa det här [självstudien](tutorial-routing.md).

Använd följande självstudier om du vill veta hur du läser meddelandet från en slutpunkt.

* Läsa från [inbyggda-slutpunkt](quickstart-send-telemetry-node.md)

* Läsa från [Blob-lagring](../storage/blobs/storage-blob-event-quickstart.md)

* Läsa från [Händelsehubbar](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Läsa från [Service Bus-köer](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Läsa från [Service Bus-ämnen](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Återställningsplats väg

Återställningsplats vägen skickar alla meddelanden som inte uppfyller villkoren för frågan på någon av de befintliga vägarna till inbyggda-Event-Hubs (**meddelanden/händelser**), som är kompatibel med [Händelsehubbar](/azure/event-hubs/). Om meddelanderoutning är aktiverat kan du aktivera funktionen återställningsplats väg. När du har skapat en väg data slutar flöda till inbyggda-i-slutpunkten, såvida inte en väg skapas till denna slutpunkt. Om det finns inga vägar till inbyggda-i-slutpunkten och en återställningsplats väg är aktiverad, skickas endast meddelanden som inte matchar någon fråga villkoren på vägar till inbyggda-i-slutpunkten. Även om alla befintliga vägar tas bort måste återställningsplats väg aktiveras att ta emot alla data i inbyggda-i-slutpunkten. 

Du kan aktivera/inaktivera återställningsplats vägen i Azure Portal -> meddelanderoutning bladet. Du kan också använda Azure Resource Manager för [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) att använda en anpassad slutpunkt för återställningsplats vägen.

## <a name="non-telemetry-events"></a>Icke-telemetrihändelser

Förutom enhetstelemetri kan meddelanderoutning också skicka enheten Ändringshändelser för tvilling och Livscykelhändelser för enhet. Exempel: om en väg som har skapats med konfigurationen av datakällan **enhet Ändringshändelser för tvilling**, IoT Hub skickar meddelanden till slutpunkten som innehåller ändringen i enhetstvillingen. På samma sätt, om ett flöde skapas med datakällan som har angetts till **Livscykelhändelser för enhet**, IoT Hub skickar ett meddelande som anger om enheten har tagits bort eller skapas. 

[IoT Hub är integrerat med Azure Event Grid](iot-hub-event-grid.md) att publicera enhetshändelser för att stödja realtid integreringar och automatisering av arbetsflöden baserat på dessa händelser. Se nyckeln [skillnaderna mellan meddelanderoutning och Event Grid](iot-hub-event-grid-routing-comparison.md) vill veta vilken som fungerar bäst för ditt scenario.

## <a name="testing-routes"></a>Testa vägar

När du skapar en ny väg eller redigera ett befintligt flöde, bör du testa vägen frågan med ett exempelmeddelande. Du kan testa enskilda vägar eller testa alla vägar på samma gång och inga meddelanden dirigeras till slutpunkterna under testet. Azure-portalen, Azure Resource Manager, Azure PowerShell och Azure CLI kan användas för testning. Resultat att identifiera om exempelmeddelande matchar frågan, meddelande matchade inte frågan eller test gick inte att köra eftersom exempelsyntax för meddelande eller fråga är felaktiga. Mer information finns i [testa flödet](/rest/api/iothub/iothubresource/testroute) och [testa alla vägar](/rest/api/iothub/iothubresource/testallroutes).

## <a name="latency"></a>Svarstid

När du vidarebefordra telemetrimeddelanden från enheten till molnet med hjälp av inbyggda slutpunkter finns en liten ökning i svarstiden slutpunkt till slutpunkt när du har skapat för den första rutten.

I de flesta fall är den genomsnittliga ökningen av svarstiden mindre än 500 ms. Du kan övervaka svarstid med **routning: meddelande svarstiden för meddelanden/händelser** eller **d2c.endpoints.latency.builtIn.events** IoT Hub-mått. Skapa eller ta bort någon väg efter den första påverkar inte svarstiden slutpunkt till slutpunkt.

## <a name="monitoring-and-troubleshooting"></a>Övervakning och felsökning

IoT Hub innehåller flera Routning och slutpunkten relaterade mått för att ge dig en översikt över hälsotillståndet för din hubb och meddelanden som skickas. Du kan kombinera information från flera mått för att identifiera rotorsaken till problem. Till exempel använda mått **routning: telemetrimeddelanden bort** eller **d2c.telemetry.egress.dropped** att identifiera hur många meddelanden som har tagits bort när de inte matchade frågor på någon av vägarna och återställningsplats vägen har inaktiverats. [IoT Hub mått](iot-hub-metrics.md) visar en lista över alla mått som är aktiverade som standard för din IoT-hubb.

Du kan använda REST-API [hämta Slutpunktshälsa](https://docs.microsoft.com/de-de/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) att hämta [hälsostatus](iot-hub-devguide-endpoints.md#custom-endpoints) slutpunkter. Vi rekommenderar att du använder den [IoT Hub mått](iot-hub-metrics.md) rör Routning meddelande svarstid för att identifiera och felsöka fel när slutpunktshälsa är döda eller är skadad. Till exempel för typ av slutpunkt Event Hubs, du kan övervaka **d2c.endpoints.latency.eventHubs**. Status för en defekt slutpunkt uppdateras till felfritt läge när IoT Hub har etablerat en konsekvent hälsotillstånd.

Med hjälp av den **vägar** diagnostikloggar i Azure Monitor [diagnostikinställningar](../iot-hub/iot-hub-monitor-resource-health.md), kan du spårar fel som uppstår under utvärderingen av ett routning hälsa för frågan och slutpunkten som uppfattas av IoT Hub, till exempel När en slutpunkt är inaktiv. Dessa diagnostikloggar kan skickas till Azure Monitor-loggar, Event Hubs eller Azure Storage för anpassad bearbetning.

## <a name="next-steps"></a>Nästa steg

* Läs hur du skapar meddelandevägar i [Process IoT Hub enhet-till-moln-meddelanden med vägar](tutorial-routing.md).

* [Hur du skickar meddelanden från enheten till molnet](quickstart-send-telemetry-node.md)

* Information om SDK: erna som du kan använda för att skicka meddelanden från enheten till molnet finns i [Azure IoT SDK: er](iot-hub-devguide-sdks.md).

---
title: Förstå Azure IoT Hub meddelanderoutning | Microsoft Docs
description: Guide för utvecklare – hur du använder meddelanderoutning för att skicka meddelanden från enheten till molnet. Innehåller information om hur du skickar både telemetri och data som inte är telemetri.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: d2d4d39cc7b330794094745851856365ef54b42f
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828196"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Använd IoT Hub meddelanderoutning för att skicka meddelanden från enheten till molnet till olika slut punkter

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Med meddelanderoutning kan du skicka meddelanden från dina enheter till moln tjänster på ett automatiserat, skalbart och tillförlitligt sätt. Meddelanderoutning kan användas för: 

* Att **skicka meddelanden om enhets telemetri och händelser** , t. ex. enhetens livs cykel händelser och enhetens dubbla ändrings händelser till den inbyggda slut punkten och anpassade slut punkter. Lär dig mer om [routning slut punkter](#routing-endpoints).

* **Filtrering av data innan de dirigeras till olika slut punkter** genom att använda omfattande frågor. Med meddelanderoutning kan du fråga efter meddelande egenskaper och meddelande text samt enhetens dubbla Taggar och enhetens dubbla egenskaper. Läs mer om hur du använder [frågor i](iot-hub-devguide-routing-query-syntax.md)meddelanderoutning.

IoT Hub behöver skriv åtkomst till dessa tjänst slut punkter för att meddelanderoutning ska fungera. Om du konfigurerar dina slut punkter via Azure Portal läggs de nödvändiga behörigheterna till. Se till att du konfigurerar dina tjänster så att de stöder det förväntade data flödet. När du först konfigurerar din IoT-lösning kan du behöva övervaka ytterligare slut punkter och göra nödvändiga justeringar för den faktiska belastningen.

IoT Hub definierar ett [gemensamt format](iot-hub-devguide-messages-construct.md) för all kommunikation från enhet till moln för samverkan mellan protokoll. Om ett meddelande matchar flera vägar som pekar på samma slut punkt, IoT Hub levererar meddelandet till slut punkten bara en gång. Därför behöver du inte konfigurera deduplicering i Service Bus kö eller ämne. I partitionerade köer garanterar partitionens tillhörighet meddelande ordning. Använd den här självstudien för att lära dig hur du [konfigurerar meddelanderoutning](tutorial-routing.md).

## <a name="routing-endpoints"></a>Dirigera slut punkter

En IoT-hubb har en inbyggd standard slut punkt (**meddelanden/händelser**) som är kompatibel med Event Hubs. Du kan skapa [anpassade slut punkter](iot-hub-devguide-endpoints.md#custom-endpoints) för att dirigera meddelanden till genom att länka andra tjänster i din prenumeration till IoT Hub. IoT Hub stöder för närvarande följande tjänster som anpassade slut punkter:

### <a name="built-in-endpoint"></a>Inbyggd slut punkt

Du kan använda standard [Event Hubs integration och SDK](iot-hub-devguide-messages-read-builtin.md) : er för att ta emot meddelanden från enheten till molnet från den inbyggda slut punkten (**meddelanden/händelser**). När en väg har skapats slutar data flöda till den inbyggda slut punkten om inte en väg skapas till den slut punkten.

### <a name="azure-blob-storage"></a>Azure Blob Storage

IoT Hub stöder skrivning av data till Azure Blob Storage i [Apache Avro](https://avro.apache.org/) -format samt i JSON-format. Möjligheten att koda JSON-format är allmänt tillgänglig i alla regioner där IoT Hub är tillgängligt. Standardvärdet är AVRO. Kodnings formatet kan bara anges när Blob Storage-slutpunkten har kon figurer ATS. Det går inte att redigera formatet för en befintlig slut punkt. När du använder JSON-kodning måste du ange contentType till JSON och contentEncoding till UTF-8 i meddelande [systemets egenskaper](iot-hub-devguide-routing-query-syntax.md#system-properties). Om detta inte anges kommer IoT Hub att skriva meddelanden i bas 64-kodat format. Du kan välja kodnings formatet med IoT Hub skapa eller uppdatera REST API, särskilt [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), Azure Portal, [Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)eller [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0). Följande diagram visar hur du väljer kodnings formatet i Azure Portal.

![Slut punkts kodning för Blob Storage](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT Hub också stöd för att dirigera meddelanden till ADLS Gen2 konton, som är [hierarkiska namn rymds](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)-aktiverade lagrings konton som skapats ovanpå Blob Storage. Den här funktionen finns i en offentlig för hands version och är tillgänglig för nya ADLS Gen2 konton i USA, västra 2 och västra centrala USA. Vi kommer snart att distribuera den här funktionen till alla moln regioner.

IoT Hub batchar meddelanden och skriver data till en BLOB när batchen når en viss storlek eller en viss tid har förflutit. IoT Hub standardvärdet för följande fil namns konvention:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Du kan använda valfri fil namns konvention, men du måste använda alla listade tokens. IoT Hub skrivs till en tom BLOB om det inte finns några data att skriva.

Vid routning till Blob Storage rekommenderar vi att du skapar en lista över Blobbarna och sedan går över dem, för att se till att alla behållare är lästa utan att behöva göra några antaganden om partitionen. Partitions intervallet kan eventuellt ändras under en [Microsoft-initierad redundansväxling](iot-hub-ha-dr.md#microsoft-initiated-failover) eller IoT Hub [manuell redundans](iot-hub-ha-dr.md#manual-failover-preview). Du kan använda [list-BLOB-API: et](https://docs.microsoft.com/rest/api/storageservices/list-blobs) för att räkna upp listan över blobbar. Se följande exempel som vägledning.

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

### <a name="service-bus-queues-and-service-bus-topics"></a>Service Bus köer och Service Bus ämnen

Service Bus köer och ämnen som används som IoT Hub slut punkter får inte ha **sessioner** eller **dubblettidentifiering** aktiverade. Om något av dessa alternativ är aktiverat visas slut punkten som **ej nåbar** i Azure Portal.

### <a name="event-hubs"></a>Event Hubs

Förutom den inbyggda-Event Hubs-kompatibla slut punkten kan du också dirigera data till anpassade slut punkter av typen Event Hubs. 

## <a name="reading-data-that-has-been-routed"></a>Läser data som har dirigerats

Du kan konfigurera en väg genom att följa [](tutorial-routing.md)den här självstudien.

Använd följande självstudier om du vill lära dig mer om att läsa meddelanden från en slut punkt.

* Läser från [inbyggd slut punkt](quickstart-send-telemetry-node.md)

* Läser från [Blob Storage](../storage/blobs/storage-blob-event-quickstart.md)

* Läser från [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Läser från [Service Bus köer](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Läsa från [Service Bus ämnen](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Reserv väg

Reserv vägen skickar alla meddelanden som inte uppfyller frågevillkor på någon av de befintliga vägarna till de inbyggda Event Hubs (**meddelanden/händelser**) som är kompatibla med [Event Hubs](/azure/event-hubs/). Om meddelanderoutning är aktiverat kan du aktivera reserv väg funktionen. När en väg har skapats slutar data flöda till den inbyggda slut punkten, om inte en väg skapas till den slut punkten. Om det inte finns några vägar till den inbyggda slut punkten och en återställnings väg är aktive rad skickas endast meddelanden som inte matchar några frågevillkor i vägar till den inbyggda slut punkten. Om alla befintliga vägar tas bort måste återställnings vägen vara aktive rad för att ta emot alla data vid den inbyggda slut punkten.

Du kan aktivera/inaktivera återställnings vägen på bladet Azure Portal-> meddelande cirkulation. Du kan också använda Azure Resource Manager för [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) om du vill använda en anpassad slut punkt för återställnings väg.

## <a name="non-telemetry-events"></a>Händelser som inte är telemetri

Förutom telemetri, aktiverar meddelanderoutning även överföring av enhets dubbla ändrings händelser och enhets livs cykel händelser. Om en väg till exempel skapas med data källa inställt på **enhet dubbla ändrings händelser**skickar IoT Hub meddelanden till slut punkten som innehåller ändringen i enheten. På samma sätt kommer IoT Hub att skicka ett meddelande som anger om enheten har tagits bort eller skapats, om en väg skapas med data källa inställt på **enhetens livs cykel händelser**. 

[IoT Hub integreras också med Azure Event Grid](iot-hub-event-grid.md) för att publicera enhets händelser som stöder real tids integrering och automatisering av arbets flöden baserat på dessa händelser. Se viktiga [skillnader mellan meddelanderoutning och event Grid](iot-hub-event-grid-routing-comparison.md) för att se vilka som fungerar bäst för ditt scenario.

## <a name="testing-routes"></a>Testa vägar

När du skapar en ny väg eller redigerar en befintlig väg bör du testa väg frågan med ett exempel meddelande. Du kan testa enskilda vägar eller testa alla vägar samtidigt och inga meddelanden dirigeras till slut punkterna under testet. Azure Portal, Azure Resource Manager, Azure PowerShell och Azure CLI kan användas för testning. Med hjälp av kan du se om exempel meddelandet matchade frågan, meddelandet inte matchade frågan eller eftersom det inte gick att köra testet eftersom exempel meddelandet eller frågesyntaxen är felaktiga. Läs mer i [testa väg](/rest/api/iothub/iothubresource/testroute) och [testa alla vägar](/rest/api/iothub/iothubresource/testallroutes).

## <a name="latency"></a>Svarstid

När du dirigerar meddelanden från enhet till molnet med hjälp av inbyggda slut punkter, finns det en liten ökning av svars tiden från slut punkt till slut punkt efter att den första vägen har skapats.

I de flesta fall är den genomsnittliga ökningen i svars tiden mindre än 500 ms. Du kan övervaka svars tiden med **Routning: meddelande fördröjning för meddelanden/händelser** eller **D2C. endpoints. latens. builtity. events** IoT Hub mått. Om du skapar eller tar bort en väg när den första inte påverkar svars tiden från slut punkt till slut punkt.

## <a name="monitoring-and-troubleshooting"></a>Övervakning och fel sökning

IoT Hub tillhandahåller flera mått som rör Routning och slut punkter för att ge dig en översikt över hälsan för ditt nav och meddelanden som skickas. Du kan kombinera information från flera mått för att identifiera rotor saken till problem. Använd t. ex. mått **Routning: telemetri ignoreras** eller **D2C. telemetri. utgående. släppt** för att identifiera antalet meddelanden som släpptes när de inte matchade frågor på någon av vägarna och återställnings vägen inaktiverades. [IoT Hub mått](iot-hub-metrics.md) visar en lista över alla mått som är aktiverade som standard för din IoT Hub.

Du kan använda REST API [Hämta slut punkts hälsa](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) för att få [hälso status](iot-hub-devguide-endpoints.md#custom-endpoints) för slut punkterna. Vi rekommenderar att du använder [IoT Hub mått](iot-hub-metrics.md) som rör svars tiden för routning av meddelanden för att identifiera och felsöka fel när slut punkts hälsan är död eller ohälsosam. För slut punkts typ Event Hubs kan du till exempel övervaka **D2C. endpoints. latens. eventHubs**. Statusen för en felaktig slut punkt kommer att uppdateras till felfri när IoT Hub har upprättat en konsekvent hälso status.

Med hjälp av **vägar** diagnostikloggar i Azure Monitor [diagnostikinställningar](../iot-hub/iot-hub-monitor-resource-health.md)kan du spåra fel som uppstår under utvärderingen av en cirkulations fråga och slut punkts hälsa som uppfattas av IoT Hub, till exempel när en slut punkt är död. Dessa diagnostikloggar kan skickas till Azure Monitor loggar, Event Hubs eller Azure Storage för anpassad bearbetning.

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar meddelande vägar finns i [Process IoT Hub enhet-till-moln-meddelanden med hjälp av vägar](tutorial-routing.md).

* [Skicka meddelanden från enheten till molnet](quickstart-send-telemetry-node.md)

* Information om de SDK: er som du kan använda för att skicka meddelanden från enheten till molnet finns i [Azure IoT SDK](iot-hub-devguide-sdks.md): er.

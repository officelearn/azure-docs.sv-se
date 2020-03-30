---
title: Förstå Azure IoT Hub-meddelanderoutning | Microsoft-dokument
description: Utvecklarguide – hur du använder meddelanderoutning för att skicka meddelanden från enhet till moln. Innehåller information om hur du skickar både telemetri- och icke-telemetridata.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: d10744f2536cdf89115cdccd0bea6f1e5155774c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370465"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Använda IoT Hub-meddelanderoutning för att skicka meddelanden från enhet till moln till olika slutpunkter

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Med meddelanderoutning kan du skicka meddelanden från dina enheter till molntjänster på ett automatiserat, skalbart och tillförlitligt sätt. Meddelanderoutning kan användas för: 

* **Skicka enhetstelemetrimeddelanden samt händelser** nämligen enhetslivscykelhändelser och enhetstvillingen ändra händelser till den inbyggda slutpunkten och anpassade slutpunkter. Läs mer om [routningslutpunkter](#routing-endpoints).

* **Filtrera data innan du dirigerar dem till olika slutpunkter** genom att använda omfattande frågor. Med meddelanderoutning kan du fråga efter meddelandeegenskaperna och meddelandetexten samt enhetstvillingtaggar och enhetstvillingegenskaper. Läs mer om hur du använder [frågor i meddelanderoutning](iot-hub-devguide-routing-query-syntax.md).

IoT Hub behöver skrivåtkomst till dessa tjänstslutpunkter för att meddelanderoutning ska fungera. Om du konfigurerar dina slutpunkter via Azure-portalen läggs nödvändiga behörigheter till för dig. Se till att du konfigurerar dina tjänster för att stödja det förväntade dataflödet. Om du till exempel använder Event Hubs som en anpassad slutpunkt måste du konfigurera **dataflödesenheterna** för händelsehubben så att den kan hantera inträngningen av händelser som du planerar att skicka via IoT Hub-meddelanderoutning. På samma sätt måste du konfigurera den **maximala storleken** när du använder en servicebusskö för att säkerställa att kön kan hålla alla data ingående, tills den har gått ut av konsumenterna. När du först konfigurerar IoT-lösningen kan du behöva övervaka dina ytterligare slutpunkter och göra nödvändiga justeringar för den faktiska belastningen.

IoT Hub definierar ett [gemensamt format](iot-hub-devguide-messages-construct.md) för alla enheter-till-moln-meddelanden för interoperabilitet mellan protokoll. Om ett meddelande matchar flera vägar som pekar på samma slutpunkt levererar IoT Hub endast meddelande till den slutpunkten en gång. Därför behöver du inte konfigurera deduplicering i kö eller ämne för servicebuss. I partitionerade köer garanterar partitionstillhörighet meddelandeordning. Använd den här självstudien om du vill lära dig hur du [konfigurerar meddelanderoutning](tutorial-routing.md).

## <a name="routing-endpoints"></a>Slutpunkter för routning

En IoT-hubb har en standard inbyggd slutpunkt (**meddelanden/händelser**) som är kompatibel med Event Hubs. Du kan skapa [anpassade slutpunkter](iot-hub-devguide-endpoints.md#custom-endpoints) för att dirigera meddelanden till genom att länka andra tjänster i din prenumeration till IoT Hub. 

Varje meddelande dirigeras till alla slutpunkter vars routningsfrågor det matchar. Med andra ord kan ett meddelande dirigeras till flera slutpunkter.

IoT Hub stöder för närvarande följande tjänster som anpassade slutpunkter:

### <a name="built-in-endpoint"></a>Inbyggd slutpunkt

Du kan använda [standardintegration av eventhubbar och SDK:er](iot-hub-devguide-messages-read-builtin.md) för att ta emot meddelanden från den inbyggda slutpunkten (**meddelanden/händelser**). När en rutt har skapats slutar data att flöda till den inbyggda slutpunkten om inte en rutt skapas till den slutpunkten.

### <a name="azure-storage"></a>Azure Storage

Det finns två IoT-lagringstjänster som IoT Hub kan dirigera meddelanden till ADLS Gen2-konton [(Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) och [Azure Data Lake Storage Gen2).](../storage/blobs/data-lake-storage-introduction.md) Azure Data Lake Storage-konton är [hierarkiska namnområdesaktiverade](../storage/blobs/data-lake-storage-namespace.md)lagringskonton som skapats ovanpå blob-lagring. Båda dessa använder blobbar för lagring.

IoT Hub stöder att skriva data till Azure Storage i [Apache Avro-format](https://avro.apache.org/) samt i JSON-format. Standard är AVRO. Kodningsformatet kan bara ställas in när blob-lagringsslutpunkten är konfigurerad. Det går inte att redigera formatet för en befintlig slutpunkt. När du använder JSON-kodning måste du ange contentType till **application/json** och contentEncoding till **UTF-8** i [meddelandesystemets egenskaper](iot-hub-devguide-routing-query-syntax.md#system-properties). Båda dessa värden är skiftlägesokänsliga. Om innehållskodningen inte är inställd skriver IoT Hub meddelandena i bas 64-kodat format. Du kan välja kodningsformat med IoT Hub Create eller Update REST API, särskilt [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), Azure-portalen, [Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)eller [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0). Följande diagram visar hur du väljer kodningsformatet i Azure-portalen.

![Kodning av bloblagringsslutpunkt](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT Hub batchar meddelanden och skriver data till lagring när batchen når en viss storlek eller en viss tid har förflutit. IoT Hub är standard standard för följande filnamnskonvention: 

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Du kan använda alla filnamnskonventioner, men du måste använda alla listade token. IoT Hub skriver till en tom blob om det inte finns några data att skriva.

Vi rekommenderar att du listar blobbar eller filer och sedan itererar över dem, för att säkerställa att alla blobbar eller filer läss utan att göra några antaganden om partition. Partitionsintervallet kan eventuellt ändras under en [Microsoft-initierad redundans-](iot-hub-ha-dr.md#microsoft-initiated-failover) eller IoT [Hub-manuell redundans](iot-hub-ha-dr.md#manual-failover). Du kan använda [API:et lista blobbar](https://docs.microsoft.com/rest/api/storageservices/list-blobs) för att räkna upp listan över blobbar eller [Lista ADLS Gen2 API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) för listan över filer. Se följande exempel som vägledning.

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

> [!NOTE]
> Om ditt lagringskonto har brandväggskonfigurationer som begränsar IoT Hub-anslutningen kan du överväga att använda [Microsofts betrodda undantag](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) från första part (tillgängligt i vissa regioner för IoT-hubbar med hanterad tjänstidentitet).

Om du vill skapa ett Azure Data Lake Gen2-kompatibelt lagringskonto skapar du ett nytt V2-lagringskonto och väljer aktiverat på fältet *Hierarkiskt namnområde* på fliken *Avancerat* som visas i följande avbildning: **Advanced**

![Välj Azure Date Lake Gen2-lagring](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)


### <a name="service-bus-queues-and-service-bus-topics"></a>Servicebussköer och servicebussavsnitt

Service Bus köer och ämnen som används som IoT Hub slutpunkter får inte ha **sessioner** eller **dubblettidentifiering** aktiverat. Om något av dessa alternativ är aktiverat visas slutpunkten som **kan inte nås** i Azure-portalen.

> [!NOTE]
> Om servicebussresursen har brandväggskonfigurationer som begränsar IoT Hub:s anslutning kan du använda [Microsofts betrodda undantag](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing) från första parten (tillgängligt i vissa regioner för IoT-hubbar med hanterad tjänstidentitet).


### <a name="event-hubs"></a>Händelsehubbar

Förutom den inbyggda hubbar-hubbar-kompatibla slutpunkten kan du också dirigera data till anpassade slutpunkter av typen Event Hubs. 

> [!NOTE]
> Om händelsehubbarresursen har brandväggskonfigurationer som begränsar IoT Hubs anslutning kan du överväga att använda [Microsofts betrodda undantag](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing) från första parten (tillgängligt i vissa regioner för IoT-hubbar med hanterad tjänstidentitet).


## <a name="reading-data-that-has-been-routed"></a>Läsa data som har dirigerats

Du kan konfigurera en väg genom att följa den här [självstudien](tutorial-routing.md).

Använd följande självstudier för att lära dig hur du läser meddelande från en slutpunkt.

* Läsa från [inbyggd slutpunkt](quickstart-send-telemetry-node.md)

* Läsa från [Blob-lagring](../storage/blobs/storage-blob-event-quickstart.md)

* Läsa från [eventhubbar](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Läsa från [servicebussköer](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Läs från [Service Bus Ämnen](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)


## <a name="fallback-route"></a>Återgångsväg

Reservvägen skickar alla meddelanden som inte uppfyller frågevillkoren på någon av de befintliga vägarna till de inbyggda eventhubbar **(meddelanden/händelser),** som är kompatibel med [eventhubbar](/azure/event-hubs/). Om meddelanderoutning är aktiverat kan du aktivera returvägsfunktionen. När en rutt har skapats slutar data att flöda till den inbyggda slutpunkten, såvida inte en väg skapas till den slutpunkten. Om det inte finns några vägar till den inbyggda slutpunkten och en återgångsväg är aktiverad, skickas endast meddelanden som inte matchar några frågevillkor på vägar till den inbyggda slutpunkten. Om alla befintliga vägar tas bort måste återställningsvägen också aktiveras för att ta emot alla data vid den inbyggda slutpunkten.

Du kan aktivera/inaktivera återställningsvägen i Azure portal->Meddelanderoutningsblad. Du kan också använda Azure Resource Manager för [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) för att använda en anpassad slutpunkt för återställningsväg.

## <a name="non-telemetry-events"></a>Icke-telemetrihändelser

Förutom enhetstelemetri gör meddelanderoutning också det möjligt att skicka händelser för enhets dubbla ändringar, händelser för enhetslivscykel och digitala dubbla ändringshändelser (i offentlig förhandsversion). Om till exempel en väg skapas med datakälla inställd på **enhets dubbla ändringshändelser**skickar IoT Hub meddelanden till slutpunkten som innehåller ändringen i enhetstvillingen. Om en väg skapas med datakällan inställd på **enhetslivscykelhändelser**skickar IoT Hub ett meddelande som anger om enheten har tagits bort eller skapats. Slutligen, som en del av [IoT Plug and Play offentliga förhandsvisning,](../iot-pnp/overview-iot-plug-and-play.md)en utvecklare kan skapa vägar med datakälla inställd på **digitala twin change händelser** och IoT Hub skickar meddelanden när en digital twin [egendom](../iot-pnp/iot-plug-and-play-glossary.md) är inställd eller ändras, en [digital tvilling](../iot-pnp/iot-plug-and-play-glossary.md) ersätts, eller när en förändring händelse händer för den underliggande enheten tvilling.

[IoT Hub integreras också med Azure Event Grid](iot-hub-event-grid.md) för att publicera enhetshändelser för att stödja realtidsintegreringar och automatisering av arbetsflöden baserat på dessa händelser. Se viktiga [skillnader mellan meddelanderoutning och Händelserutnät](iot-hub-event-grid-routing-comparison.md) om du vill veta vilket som fungerar bäst för ditt scenario.

## <a name="testing-routes"></a>Testa rutter

När du skapar en ny väg eller redigerar en befintlig väg bör du testa flödesfrågan med ett exempelmeddelande. Du kan testa enskilda vägar eller testa alla vägar samtidigt och inga meddelanden dirigeras till slutpunkterna under testet. Azure portal, Azure Resource Manager, Azure PowerShell och Azure CLI kan användas för testning. Resultat hjälper till att identifiera om exempelmeddelandet matchade frågan, meddelandet matchade inte frågan eller testet kunde inte köras eftersom exempelmeddelandet eller frågesyntaxen är felaktiga. Mer information finns i [Testa rutt](/rest/api/iothub/iothubresource/testroute) och testa [alla rutter](/rest/api/iothub/iothubresource/testallroutes).

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>Beställningsgarantier med minst en gång leverans

IoT Hub-meddelanderoutning garanterar beställda och minst en gång leverans av meddelanden till slutpunkterna. Det innebär att det kan finnas dubblettmeddelanden och en serie meddelanden kan skickas om för att uppfylla den ursprungliga meddelandeordningen. Om den ursprungliga meddelandeordningen till exempel är [1,2,3,4] kan du få en meddelandesekvens som [1,2,1,2,3,1,2,3,4]. Beställningsgarantin är att om du någonsin får meddelande [1], skulle det alltid följas av [2,3,4].

För hantering av meddelandedubbletter rekommenderar vi att du stämplar en unik identifierare i meddelandets programegenskaper vid ursprungsplatsen, som vanligtvis är en enhet eller en modul. Tjänsten som förbrukar meddelandena kan hantera dubblettmeddelanden med den här identifieraren.

## <a name="latency"></a>Svarstid

När du dirigerar telemetrimeddelanden från enhet till moln med hjälp av inbyggda slutpunkter ökar svarstiden från slutpunkt till slutpunkt efter att den första vägen har skapats.

I de flesta fall är den genomsnittliga ökningen av latens mindre än 500 ms. Du kan övervaka svarstiden med **routning: meddelandefördröjning för meddelanden/händelser** eller **d2c.endpoints.latency.builtIn.events** IoT Hub-mått. Om du skapar eller tar bort en väg efter den första påverkas inte svarstiden från på slutna till slutna dagar.

## <a name="monitoring-and-troubleshooting"></a>Övervakning och felsökning

IoT Hub innehåller flera mått relaterade till routning och slutpunkter för att ge dig en översikt över hälsotillståndet för dina hubben och meddelanden som skickas. Du kan kombinera information från flera mått för att identifiera grundorsaken till problem. Använd till exempel mått **routning: telemetrimeddelanden som har tagits bort** eller **d2c.telemetry.egress.dropped** för att identifiera antalet meddelanden som togs bort när de inte matchade frågor på någon av vägarna och återställningsvägen inaktiverades. [IoT Hub-mått](iot-hub-metrics.md) visar alla mått som är aktiverade som standard för din IoT Hub.

Du kan använda REST API [Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) för att få [hälsostatus](iot-hub-devguide-endpoints.md#custom-endpoints) för slutpunkterna. Vi rekommenderar att du använder [IoT Hub-måtten](iot-hub-metrics.md) som är relaterade till routningsmeddelandesvarstid för att identifiera och felsöka fel när slutpunktshälsan är död eller felaktig. För slutpunktstyp händelsehubbar kan du till exempel övervaka **d2c.endpoints.latency.eventHubs**. Statusen för en felaktig slutpunkt uppdateras till felfri när IoT Hub har etablerat ett så småningom konsekvent hälsotillstånd.

Med hjälp av **diagnostikloggar** för vägar i [diagnostikinställningarna](../iot-hub/iot-hub-monitor-resource-health.md)för Azure Monitor kan du spåra fel som uppstår under utvärdering av en routningsfråga och slutpunktshälsa som uppfattas av IoT Hub, till exempel när en slutpunkt är död. Dessa diagnostikloggar kan skickas till Azure Monitor-loggar, eventhubbar eller Azure Storage för anpassad bearbetning.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du skapar meddelandevägar finns i [Bearbeta IoT Hub-meddelanden från enhet till moln med hjälp av vägar](tutorial-routing.md).

* [Så här skickar du meddelanden från enhet till moln](quickstart-send-telemetry-node.md)

* Information om de SDK:er som du kan använda för att skicka meddelanden från enhet till moln finns i [Azure IoT SDK:er](iot-hub-devguide-sdks.md).

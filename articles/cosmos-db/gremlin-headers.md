---
title: Svarshuvuden för Azure Cosmos DB Gremlin
description: Referensdokumentation för serversvarmetadata som möjliggör ytterligare felsökning
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 95677f4c45c0213de5ffac5521bac1c6bf7294e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72755081"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Svarshuvuden för Azure Cosmos DB Gremlin-server
Den här artikeln beskriver rubriker som Cosmos DB Gremlin-servern returnerar till anroparen när begäran har körts. De här rubrikerna är användbara för att felsöka prestanda för förfrågningar, bygga program som integreras internt med Cosmos DB-tjänsten och förenklar kundsupporten.

Tänk på att med beroendet av dessa rubriker begränsar du portabiliteten för ditt program till andra Gremlin-implementeringar. I gengäld får du en hårdare integration med Cosmos DB Gremlin. Dessa rubriker är inte en TinkerPop-standard.

## <a name="headers"></a>Rubriker

| Huvud | Typ | Exempelvärde | När det ingår | Förklaring |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | double | 11.3243 | Lyckade och misslyckade | Mängden insamlings- eller databasdataflöde som förbrukas i [begäranheter (RU/s eller RU: er)](request-units.md) för ett partiellt svarsmeddelande. Det här huvudet finns i varje fortsättning för begäranden som har flera segment. Det återspeglar laddningen av en viss svarsbit. Endast för begäranden som består av ett enda svarssegment matchar det här huvudet den totala kostnaden för traversal. Men för de flesta komplexa traversals detta värde representerar en partiell kostnad. |
| **x-ms-total-request-charge** | double | 423.987 | Lyckade och misslyckade | Mängden insamlings- eller databasdataflöde som förbrukas i [begäranheter (RU/s eller RU: er)](request-units.md) för hela begäran. Det här huvudet finns i varje fortsättning för begäranden som har flera segment. Den anger ackumulerad avgift sedan begäran började. Värdet för det här huvudet i det sista segmentet anger fullständig begärsträvning. |
| **x-ms-server-tid-ms** | double | 13.75 | Lyckade och misslyckade | Det här huvudet ingår för felsökningsändamål. Det anger hur lång tid, i millisekunder, som Cosmos DB Gremlin-servern tog för att köra och producera ett partiellt svarsmeddelande. Om du använder värdet för det här huvudet och jämför det med övergripande begärandenstencyprogram kan du beräkna omkostnader för nätverksfördröjning. |
| **x-ms-total-server-time-ms** | double | 130.512 | Lyckade och misslyckade | Total tid, i millisekunder, som Cosmos DB Gremlin server tog att köra hela traversal. Det här huvudet ingår i varje partiellt svar. Den representerar sammanlagd körningstid sedan begäran startade. Det senaste svaret anger total körningstid. Det här huvudet är användbart för att skilja mellan klient och server som en källa till svarstid. Du kan jämföra körningstid för körning på klienten med värdet för det här huvudet. |
| **x-ms-status-code** | long | 200 | Lyckade och misslyckade | Huvudet anger intern orsak till slutförande eller avslutning av begäran. Ansökan rekommenderas att titta på värdet av det här huvudet och vidta korrigerande åtgärder. |
| **x-ms-substatus-kod** | long | 1003 | Endast fel | Cosmos DB är en databas med flera modeller som bygger ovanpå enhetligt lagringslager. Det här huvudet innehåller ytterligare insikter om felorsaken när fel inträffar i lägre lager med hög tillgänglighetsstapel. Programmet rekommenderas att lagra det här huvudet och använda det när du kontaktar Cosmos DB kundsupport. Värdet för det här huvudet är användbart för Cosmos DB-tekniker för snabb felsökning. |
| **x-ms-retry-after-ms** | sträng (TimeSpan) | "00:00:03.9500000" | Endast fel | Det här huvudet är en strängrepresentation av en .NET [TimeSpan-typ.](https://docs.microsoft.com/dotnet/api/system.timespan) Det här värdet inkluderas endast i begäranden som inte förfaller till betalning för att tillhandahålla utmattning av dataflöde. Ansökan ska skicka om traversal igen efter instruerad tidsperiod. |
| **x-ms-activity-id** | sträng (Guid) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Lyckade och misslyckade | Huvudet innehåller en unik server-side identifierare för en begäran. Varje begäran tilldelas en unik identifierare av servern för spårningsändamål. Program ska logga aktivitetsidentifierare som returneras av servern för förfrågningar som kunder kanske vill kontakta kundsupport om. Cosmos DB supportpersonal kan hitta specifika begäranden av dessa identifierare i Cosmos DB service telemetri. |

## <a name="status-codes"></a>Statuskoder

De vanligaste statuskoderna som returneras av servern visas nedan.

| Status | Förklaring |
| --- | --- |
| **401** | Felmeddelandet `"Unauthorized: Invalid credentials provided"` returneras när autentiseringslösenordet inte matchar Cosmos DB-kontonyckel. Navigera till ditt Cosmos DB Gremlin-konto i Azure-portalen och bekräfta att nyckeln är korrekt.|
| **404** | Samtidiga åtgärder som försöker ta bort och uppdatera samma kant eller hörn samtidigt. Felmeddelandet `"Owner resource does not exist"` anger att den angivna databasen eller samlingen har det felaktiga formatet `/dbs/<database name>/colls/<collection or graph name>` för anslutningsparametrarna.|
| **408** | `"Server timeout"`anger att traversal tog mer än **30 sekunder** och avbröts av servern. Optimera dina traversals så att de körs snabbt genom att filtrera hörn eller kanter på varje hopp av traversal för att begränsa sökomfånget.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` Detta inträffar vanligtvis när ett hörn eller en kant med en identifierare redan finns i grafen.| 
| **412** | Statuskoden kompletteras med `"PreconditionFailedException": One of the specified pre-condition is not met`felmeddelande . Det här felet är ett tecken på en optimistisk samtidighetskontrollfel mellan att läsa en kant eller ett hörn och skriva tillbaka det till arkivet efter ändring. De vanligaste situationerna när det här `g.V('identifier').property('name','value')`felet uppstår är egenskapsändring, till exempel . Gremlin motorn skulle läsa vertex, ändra den, och skriva tillbaka den. Om det finns en annan traversal som körs parallellt försöker skriva samma hörn eller en kant, kommer en av dem att få det här felet. Programmet ska skicka genomgående till servern igen.| 
| **429** | Begäran begränsades och ska göras om efter värde i **x-ms-retry-after-ms**| 
| **500** | Ett felmeddelande som innehåller `"NotFoundException: Entity with the specified id does not exist in the system."` anger att en databas och/eller samling har återskapats med samma namn. Det här felet försvinner inom 5 minuter när ändringen sprids och upphäver cacheminnen i olika Cosmos DB-komponenter. Undvik det här problemet genom att använda unika databas- och samlingsnamn varje gång.| 
| **1000** | Den här statuskoden returneras när servern har tolkat ett meddelande men inte kunde köras. Det indikerar vanligtvis ett problem med frågan.| 
| **1001** | Den här koden returneras när servern slutför körningen men misslyckas med att serialisera svaret tillbaka till klienten. Det här felet kan inträffa när traversal genererar komplexa resultat, som är för stort eller inte överensstämmer med TinkerPop-protokollspecifikationen. Programmet bör förenkla traversal när det stöter på detta fel. | 
| **1003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"`returneras när traversal överskrider tillåten minnesgräns. Minnesgränsen är **2 GB** per traversal.| 
| **1004** | Den här statuskoden anger felaktigt beteckningsgrafbegäran. Begäran kan missbildas när den misslyckas avserialisering, icke-värdetyp deserialiseras som värdetyp eller ostödd gremlin-åtgärd begärs. Programmet bör inte försöka igen begäran eftersom det inte kommer att lyckas. | 
| **1007** | Vanligtvis returneras den här statuskoden med felmeddelande `"Could not process request. Underlying connection has been closed."`. Den här situationen kan inträffa om klientdrivrutinen försöker använda en anslutning som stängs av servern. Programmet ska försöka genomfarten på en annan anslutning.
| **1008** | Cosmos DB Gremlin-servern kan avsluta anslutningar för att balansera om trafiken i klustret. Klientdrivrutiner bör hantera den här situationen och endast använda liveanslutningar för att skicka begäranden till servern. Ibland kanske klientdrivrutiner inte upptäcker att anslutningen stängdes. När ett fel uppstår `"Connection is too busy. Please retry after sometime or open more connections."` på ett sätt bör det återförsöka genomgående på en annan anslutning.

## <a name="samples"></a>Exempel

Ett exempelklientprogram baserat på Gremlin.Net som läser ett statusattribut:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Ett exempel som visar hur du läser statusattribut från Gremlin java-klient:

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>Nästa steg
* [HTTP-statuskoder för Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Vanliga Azure Cosmos DB REST-svarshuvuden](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [Krav på provider för TinkerPop-grafdrivrutin]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)

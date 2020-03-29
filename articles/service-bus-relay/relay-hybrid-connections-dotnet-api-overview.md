---
title: Översikt över Azure Relay .NET Standard API:er | Microsoft-dokument
description: Den här artikeln sammanfattar några av de viktigaste en översikt över Azure Relay Hybrid Connections .NET Standard API.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 18eaf2d2daae817107be6cdb0da9359bb5f9b4e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514543"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Azure Relay Hybrid-anslutningar .NET Standard API översikt

Den här artikeln sammanfattar några av de viktigaste Azure Relay Hybrid-anslutningar .NET Standard [klient API: er](/dotnet/api/microsoft.azure.relay).
  
## <a name="relay-connection-string-builder-class"></a>Reläanslutningssträngbyggare klass

Klassen [RelayConnectionStringBuilder][RelayConnectionStringBuilder] formaterar anslutningssträngar som är specifika för Relay Hybrid-anslutningar. Du kan använda den för att verifiera formatet på en anslutningssträng eller för att skapa en anslutningssträng från grunden. Se följande kod för ett exempel:

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

Du kan också skicka en `RelayConnectionStringBuilder` anslutningssträng direkt till metoden. Med den här åtgärden kan du kontrollera att anslutningssträngen är i ett giltigt format. Om någon av parametrarna är ogiltiga `ArgumentException`genererar konstruktorn en .

```csharp
var myConnectionString = "[RelayConnectionString]";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>Hybridanslutningsström

[Klassen HybridConnectionStream][HCStream] är det primära objekt som används för att skicka och ta emot data från en Azure Relay-slutpunkt, oavsett om du arbetar med en [HybridConnectionClient][HCClient]eller en [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>Hämta en hybridanslutningsström

#### <a name="listener"></a>Lyssnare

Med ett [HybridConnectionListener-objekt][HCListener] kan `HybridConnectionStream` du hämta ett objekt på följande sätt:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Client

Med ett [HybridConnectionClient-objekt][HCClient] kan `HybridConnectionStream` du hämta ett objekt på följande sätt:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Ta emot data

Klassen [HybridConnectionStream][HCStream] möjliggör tvåvägskommunikation. I de flesta fall får du kontinuerligt från strömmen. Om du läser text från strömmen kanske du också vill använda ett [StreamReader-objekt,](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) vilket gör det enklare att tolka data. Du kan till exempel läsa data som `byte[]`text i stället för som .

Följande kod läser enskilda textrader från dataströmmen tills en annullering begärs:

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>Skicka data

När du har upprättat en anslutning kan du skicka ett meddelande till relay-slutpunkten. Eftersom anslutningsobjektet ärver [Stream](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx)skickar `byte[]`du data som en . I följande exempel visas hur du gör detta:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Om du vill skicka text direkt, utan att behöva koda strängen `hybridConnectionStream` varje gång, kan du radbrytas mot ett [StreamWriter-objekt.](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx)

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Relay finns i följande länkar:

* [Referens för Microsoft.Azure.Relay](/dotnet/api/microsoft.azure.relay)
* [Vad är Azure Relay?](relay-what-is-it.md)
* [Tillgängliga relay-API:er](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener
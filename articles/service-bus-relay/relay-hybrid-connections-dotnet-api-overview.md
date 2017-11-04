---
title: "Översikt över Azure Relay .NET Standard API: er | Microsoft Docs"
description: "Översikt över Azure Relay .NET Standard-API"
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 58451bae409c74c319f41c38a1cec5f051619e0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Översikt över Azure Relay Hybrid anslutningar .NET Standard-API

Den här artikeln sammanfattas några av nyckeln Azure Relay Hybrid anslutningar .NET Standard [klientens API: er](/dotnet/api/microsoft.azure.relay).
  
## <a name="relay-connection-string-builder"></a>Relay-anslutning sträng Builder

Den [RelayConnectionStringBuilder] [ RelayConnectionStringBuilder] klassen formaterar anslutningssträngar som är specifika för Relay Hybridanslutningar. Du kan använda den för att verifiera en Anslutningssträngens format eller att skapa en anslutningssträng från grunden. Se följande kod för ett exempel:

```csharp
var endpoint = "{Relay namespace}";
var entityPath = "{Name of the Hybrid Connection}";
var sharedAccessKeyName = "{SAS key name}";
var sharedAccessKey = "{SAS key value}";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

Du kan också ange en anslutningssträng direkt till den `RelayConnectionStringBuilder` metoden. Den här åtgärden kan du kontrollera att anslutningssträngen är i ett giltigt format. Om någon av parametrarna är ogiltiga konstruktorn genererar en `ArgumentException`.

```csharp
var myConnectionString = "{RelayConnectionString}";
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

## <a name="hybrid-connection-stream"></a>Dataströmmen för hybrid-anslutning
Den [HybridConnectionStream] [ HCStream] klass är det primära objekt som används för att skicka och ta emot data från Azure Relay slutpunkt, oavsett om du arbetar med en [HybridConnectionClient] [ HCClient], eller en [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>Hämtar en dataström för Hybrid-anslutning

#### <a name="listener"></a>Lyssnare
Med hjälp av en [HybridConnectionListener][HCListener], kan du hämta en `HybridConnectionStream` objekt på följande sätt:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Client
Med hjälp av en [HybridConnectionClient][HCClient], kan du hämta en `HybridConnectionStream` objekt på följande sätt:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Ta emot data
Den [HybridConnectionStream] [ HCStream] klassen kan dubbelriktad kommunikation. I de flesta fall felmeddelandet kontinuerligt från dataströmmen. Om du läser text från dataströmmen kan du också vill använda en [StreamReader](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) -objektet, vilket möjliggör enklare tolkning av data. Du kan till exempel läsa data som text i stället för som `byte[]`.

Följande kod läser enskilda rader med text från dataströmmen tills en annullering begärs:

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
När du har skapat en koppling kan du skicka ett meddelande till Relay-slutpunkten. Eftersom anslutningsobjektet ärver [dataströmmen](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx), skicka dina data som en `byte[]`. I följande exempel visas hur du gör detta:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Men om du vill skicka text direkt, utan att behöva koda strängen varje gång du kan packa ihop den `hybridConnectionStream` objekt med en [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) objekt.

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Relay finns i följande länkar:

* [Microsoft.Azure.Relay referens](/dotnet/api/microsoft.azure.relay)
* [Vad är Azure Relay?](relay-what-is-it.md)
* [Tillgängliga Relay-API: er](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener
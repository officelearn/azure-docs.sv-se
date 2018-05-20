---
title: ta med fil
description: ta med fil
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 4a3f38e1423db0755d8c76f8850e41173d250f43
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
### <a name="create-a-console-application"></a>Skapa ett konsolprogram

Om du inaktiverade alternativet ”Klientauktorisering krävs” när du skapade reläet kan du skicka förfrågningar till URL:en för Hybridanslutningar i valfri webbläsare. För att komma åt skyddade slutpunkter måste du skapa och skicka en token i `ServiceBusAuthorization`-huvudet, som du ser här.

Skapa ett nytt projekt för **Konsolprogram (.NET Framework)** i Visual Studio.

### <a name="add-the-relay-nuget-package"></a>Lägg till Relay NuGet-paketet

1. Högerklicka på det nyskapade projektet och välj sedan **Hantera NuGet-paket**.
2. Välj **Bläddra** och sök sedan efter **Microsoft.Azure.Relay**. I sökresultaten väljer du **Microsoft Azure Relay**. 
3. Välj **Installera** för att slutföra installationen. Stäng dialogrutan.

### <a name="write-code-to-send-requests"></a>Skriv kod för att skicka begäranden

1. Ersätt de befintliga `using`-satserna överst i filen Program.cs med följande `using`-satser:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
    ```
2. Lägg till konstanter i klassen `Program` för hybridanslutningsinformationen. Ersätt platshållarna inom hakparentes med de värden du erhöll när du skapade hybridanslutningen. Se till att du använder det fullständiga namnområdesnamnet.
   
    ```csharp
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
    private const string ConnectionName = "{HybridConnectionName}";
    private const string KeyName = "{SASKeyName}";
    private const string Key = "{SASKey}";
    ```
3. Lägg till följande metod i klassen `Program`:
   
    ```csharp
    private static async Task RunAsync()
    {
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
        var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
        var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
        var client = new HttpClient();
        var request = new HttpRequestMessage()
        {
            RequestUri = uri,
            Method = HttpMethod.Get,
        };
        request.Headers.Add("ServiceBusAuthorization", token);
        var response = await client.SendAsync(request);
        Console.WriteLine(await response.Content.ReadAsStringAsync());
    }
    ```
4. Lägg till följande kodrad till `Main`-metoden i `Program`-klassen.
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    Program.cs bör se ut så här:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
   
    namespace Client
    {
        class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
               var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
                var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
                var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
                var client = new HttpClient();
                var request = new HttpRequestMessage()
                {
                    RequestUri = uri,
                    Method = HttpMethod.Get,
                };
                request.Headers.Add("ServiceBusAuthorization", token);
                var response = await client.SendAsync(request);
                Console.WriteLine(await response.Content.ReadAsStringAsync());
            }
        }
    }
    ```


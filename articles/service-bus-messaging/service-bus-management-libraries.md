---
title: Azure Service Bus management bibliotek| Microsoft-dokument
description: I den här artikeln beskrivs hur du använder Azure Service Bus-hanteringsbibliotek för att dynamiskt etablera servicebussnamnområden och entiteter.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: d0e90d9278ede97de04ad8efeaa59d94a4567f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756274"
---
# <a name="service-bus-management-libraries"></a>Service Bus-hanteringsbibliotek

Azure Service Bus-hanteringsbiblioteken kan dynamiskt etablera Service Bus-namnområden och entiteter. Detta möjliggör komplexa distributioner och meddelandescenarier och gör det möjligt att programmässigt avgöra vilka entiteter som ska etableras. Dessa bibliotek är för närvarande tillgängliga för .NET.

## <a name="supported-functionality"></a>Funktioner som stöds

* Skapa, uppdatera, ta bort namnområdet
* Skapa, uppdatera, ta bort kö
* Skapa, uppdatera, ta bort ämne
* Skapande, uppdatering, borttagning av prenumerationer

## <a name="prerequisites"></a>Krav

Om du vill komma igång med servicebusshanteringsbiblioteken måste du autentisera med Azure Active Directory (Azure AD) -tjänsten. Azure AD kräver att du autentiserar som ett tjänsthuvudnamn, vilket ger åtkomst till dina Azure-resurser. Information om hur du skapar ett huvudnamn för tjänsten finns i någon av följande artiklar:  

* [Använd Azure-portalen för att skapa Active Directory-program och tjänsthuvudnamn som kan komma åt resurser](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Använd Azure PowerShell för att skapa ett huvudnamn för tjänsten för resursåtkomst](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Använd Azure CLI för att skapa tjänstens huvudnamn för att få åtkomst till resurser](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Dessa självstudier ger `AppId` dig ett (klient-ID), `TenantId`och `ClientSecret` (autentiseringsnyckel), som alla används för autentisering av hanteringsbiblioteken. Du måste ha **ägarbehörigheter** för den resursgrupp som du vill köra.

## <a name="programming-pattern"></a>Programmeringsmönster

Mönstret för att manipulera en Service Bus-resurs följer ett gemensamt protokoll:

1. Hämta en token från Azure AD med **microsoft.identitymodel.clients.ActiveDirectory-biblioteket:**
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Skapa `ServiceBusManagementClient` objektet:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Ställ `CreateOrUpdate` in parametrarna på de angivna värdena:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Utför samtalet:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="complete-code-to-create-a-queue"></a>Fullständig kod för att skapa en kö
Här är den fullständiga koden för att skapa en Service Bus kö: 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

> [!IMPORTANT]
> Ett fullständigt exempel finns i [exempelt på .NET-hantering på GitHub](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

## <a name="next-steps"></a>Nästa steg
[Microsoft.Azure.Management.ServiceBus API-referens](/dotnet/api/Microsoft.Azure.Management.ServiceBus)

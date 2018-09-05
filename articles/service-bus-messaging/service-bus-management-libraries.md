---
title: Azure Service Bus-hanteringsbibliotek | Microsoft Docs
description: Hantera Service Bus-namnområden och meddelandeentiteter från .NET.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/05/2018
ms.author: spelluru
ms.openlocfilehash: a959687fbf6e296cab7e0d8ca49ae97a005622cf
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696583"
---
# <a name="service-bus-management-libraries"></a>Service Bus-hanteringsbibliotek

Azure Service Bus-hanteringsbibliotek kan dynamiskt etablera Service Bus-namnområden och entiteter. Detta möjliggör komplexa distributioner och scenarier för meddelanden och gör det möjligt att avgöra vilka entiteter för att etablera programmässigt. Dessa bibliotek är tillgängliga för .NET.

## <a name="supported-functionality"></a>Funktioner som stöds

* Skapa en Namespace-, update-, borttagning
* Skapa en kö-, update-, borttagning
* Skapa ämne, uppdatera, ta bort
* Skapa en prenumeration-, update-, borttagning

## <a name="prerequisites"></a>Förutsättningar

Kom igång med Service Bus-hanteringsbibliotek, måste du autentisera med tjänsten Azure Active Directory (AD Azure). Azure AD kräver att du autentisera som ett huvudnamn för tjänsten som ger åtkomst till dina Azure-resurser. Information om hur du skapar ett tjänstens huvudnamn finns i någon av följande artiklar:  

* [Använd Azure-portalen för att skapa Active Directory-program och tjänstens huvudnamn som kan komma åt resurser](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Använd Azure PowerShell för att skapa ett huvudnamn för tjänsten för resursåtkomst](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Använd Azure CLI för att skapa ett huvudnamn för tjänsten för resursåtkomst](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

De här självstudierna får du en `AppId` (klient-ID), `TenantId`, och `ClientSecret` (autentiseringsnyckeln) som används för autentisering av hanteringsbiblioteken. Du måste ha **ägare** behörigheter för resursgruppen som du vill köra.

## <a name="programming-pattern"></a>Mönster för programmering

Mönster för att ändra alla Service Bus-resurser följer ett gemensamt protokoll:

1. Hämta en token från Azure AD via den **Microsoft.IdentityModel.Clients.ActiveDirectory** bibliotek:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.core.windows.net/", new ClientCredential(clientId, clientSecret));
   ```
2. Skapa den `ServiceBusManagementClient` objekt:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Ange den `CreateOrUpdate` parametrar till dina angivna värden:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Kör anropet:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="next-steps"></a>Nästa steg

* [.NET-hanteringsexempel](https://github.com/Azure-Samples/service-bus-dotnet-management/)
* [Microsoft.Azure.Management.ServiceBus API-referens](/dotnet/api/Microsoft.Azure.Management.ServiceBus)

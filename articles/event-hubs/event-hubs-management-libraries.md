---
title: – Bibliotek för Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om biblioteket som du kan använda för att hantera Azure Event Hubs-namnområden och entiteter från .NET.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 431fe04461f422274697d1e91c4b56e914ce2d4e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53102731"
---
# <a name="event-hubs-management-libraries"></a>Event Hubs-hanteringsbibliotek

Du kan använda Azure Event Hubs-hanteringsbibliotek för att dynamiskt etablera Event Hubs-namnrymder och entiteter. Den här dynamiska utformningen kan komplexa distributioner och meddelandescenarier, så att du kan fastställa vilka entiteter för att etablera programmässigt. Dessa bibliotek är tillgängliga för .NET.

## <a name="supported-functionality"></a>Funktioner som stöds

* Skapa en Namespace-, update-, borttagning
* Skapa för Event Hubs-, update-, borttagning
* Skapa en konsumentgrupp-, update-, borttagning

## <a name="prerequisites"></a>Förutsättningar

Kom igång med Event Hubs-hanteringsbibliotek, måste du autentisera med Azure Active Directory (AAD). AAD kräver att du autentisera som ett huvudnamn för tjänsten som ger åtkomst till dina Azure-resurser. Information om hur du skapar ett tjänstens huvudnamn finns i någon av följande artiklar:  

* [Använd Azure-portalen för att skapa Active Directory-program och tjänstens huvudnamn som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md)
* [Använd Azure PowerShell för att skapa ett huvudnamn för tjänsten för resursåtkomst](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Använd Azure CLI för att skapa ett huvudnamn för tjänsten för resursåtkomst](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

De här självstudierna får du en `AppId` (klient-ID), `TenantId`, och `ClientSecret` (autentiseringsnyckeln) som används för autentisering av hanteringsbiblioteken. Du måste ha **ägare** behörigheter för resursgruppen som du vill köra.

## <a name="programming-pattern"></a>Mönster för programmering

Mönster för att ändra alla Event Hubs-resurser följer ett gemensamt protokoll:

1. Hämta en token från AAD med den `Microsoft.IdentityModel.Clients.ActiveDirectory` biblioteket.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Skapa den `EventHubManagementClient` objekt.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Ange den `CreateOrUpdate` parametrar till dina angivna värden.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Körning av anropet.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Nästa steg
* [Exempel på .NET-hantering](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Microsoft.Azure.Management.EventHub referens](/dotnet/api/Microsoft.Azure.Management.EventHub) 

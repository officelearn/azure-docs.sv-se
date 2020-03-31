---
title: Hanteringsbibliotek – Azure Event Hubs| Microsoft-dokument
description: Den här artikeln innehåller information om biblioteket som du kan använda för att hantera Azure Event Hubs namnområden och entiteter från .NET.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60746666"
---
# <a name="event-hubs-management-libraries"></a>Event Hubs-hanteringsbibliotek

Du kan använda Azure Event Hubs-hanteringsbiblioteken för att dynamiskt etablera eventhubbars namnområden och entiteter. Den här dynamiska naturen möjliggör komplexa distributioner och meddelandescenarier, så att du programmässigt kan avgöra vilka entiteter som ska etableras. Dessa bibliotek är för närvarande tillgängliga för .NET.

## <a name="supported-functionality"></a>Funktioner som stöds

* Skapa, uppdatera, ta bort namnområdet
* Skapa, uppdatera, ta bort händelsehubbar
* Skapande, uppdatering, radering av konsumentgrupper

## <a name="prerequisites"></a>Krav

Om du vill komma igång med hanteringsbiblioteken för händelsehubbar måste du autentisera med Azure Active Directory (AAD). AAD kräver att du autentiserar som ett tjänsthuvudnamn, vilket ger åtkomst till dina Azure-resurser. Information om hur du skapar ett huvudnamn för tjänsten finns i någon av följande artiklar:  

* [Använd Azure-portalen för att skapa Active Directory-program och tjänsthuvudnamn som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md)
* [Använd Azure PowerShell för att skapa ett huvudnamn för tjänsten för resursåtkomst](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Använd Azure CLI för att skapa tjänstens huvudnamn för att få åtkomst till resurser](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Dessa självstudier ger `AppId` dig ett (klient-ID), `TenantId`och `ClientSecret` (autentiseringsnyckel), som alla används för autentisering av hanteringsbiblioteken. Du måste ha **ägarbehörigheter** för den resursgrupp som du vill köra.

## <a name="programming-pattern"></a>Programmeringsmönster

Mönstret för att manipulera en Event Hubs-resurs följer ett gemensamt protokoll:

1. Hämta en token från `Microsoft.IdentityModel.Clients.ActiveDirectory` AAD med hjälp av biblioteket.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Skapa `EventHubManagementClient` objektet.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Ställ `CreateOrUpdate` in parametrarna på de angivna värdena.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Utför samtalet.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Nästa steg
* [Exempel på .NET-hantering](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Referens för Microsoft.Azure.Management.EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 

---
title: Bibliotek för Azure Event Hubs | Microsoft Docs
description: Hantera Händelsehubbar namnområden och entiteter från .NET
services: event-hubs
cloud: na
documentationcenter: na
author: sethmanheim
manager: timlt
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 2ae2f8f2006507284338fb4fa62e4942476cf2bc
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2017
ms.locfileid: "26780981"
---
# <a name="event-hubs-management-libraries"></a>Bibliotek för Event Hubs

Bibliotek för Händelsehubbar kan dynamiskt etablera Händelsehubbar namnområden och enheter. Den här dynamiska strukturen gör komplexa distributioner och meddelandescenarier, så att du kan bestämma vilka enheter att etablera programmässigt. Dessa bibliotek är tillgängliga för .NET.

## <a name="supported-functionality"></a>Funktioner som stöds

* Skapa en Namespace-, update-, borttagning
* Skapa en Event Hubs, uppdatering, borttagning
* Skapa en konsumentgrupp-, update-, borttagning

## <a name="prerequisites"></a>Förutsättningar

Om du vill komma igång med Händelsehubbar hantering av bibliotek, måste du autentisera med Azure Active Directory (AAD). AAD kräver att du autentisera som ett huvudnamn för tjänsten som ger åtkomst till resurserna i Azure. Information om hur du skapar en tjänst UPN finns i något av dessa artiklar:  

* [Använda Azure portal för att skapa Active Directory-program och tjänstens huvudnamn som har åtkomst till resurser](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [Använd Azure PowerShell för att skapa ett huvudnamn för tjänsten för resursåtkomst](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Använd Azure CLI för att skapa ett huvudnamn för tjänsten för resursåtkomst](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Dessa självstudiekurser ger dig en `AppId` (klient-ID), `TenantId`, och `ClientSecret` (autentiseringsnyckel) som används för autentisering av hantering av bibliotek. Du måste ha **ägare** behörigheter för resursgruppen som du vill köra.

## <a name="programming-pattern"></a>Mönster för programmering

Mönster för att ändra alla Händelsehubbar-resurser följer ett gemensamt protokoll:

1. Hämta en token från AAD som använder den `Microsoft.IdentityModel.Clients.ActiveDirectory` bibliotek.
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

1. Ange den `CreateOrUpdate` parametrar för att de angivna värdena.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Köra anropet.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Nästa steg
* [Hantering av .NET-exempel](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Microsoft.Azure.Management.EventHub referens](/dotnet/api/Microsoft.Azure.Management.EventHub) 

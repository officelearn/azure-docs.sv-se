---
title: Hanterings bibliotek – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om biblioteket som du kan använda för att hantera Azure-Event Hubs namn områden och entiteter från .NET.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: f3129ae5586a3096dda89eea3af21eefd1606f30
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85312979"
---
# <a name="event-hubs-management-libraries"></a>Event Hubs-hanteringsbibliotek

Du kan använda hanterings biblioteken för Azure-Event Hubs för att dynamiskt etablera Event Hubs namn områden och entiteter. Den här dynamiska typen möjliggör komplexa distributioner och meddelande scenarier, så att du kan avgöra vilka entiteter som ska etableras program mässigt. Dessa bibliotek är för närvarande tillgängliga för .NET.

## <a name="supported-functionality"></a>Funktioner som stöds

* Skapa namnrymd, uppdatera, ta bort
* Event Hubs att skapa, uppdatera och ta bort
* Skapa konsument grupp, uppdatera, ta bort

## <a name="prerequisites"></a>Krav

För att komma igång med Event Hubs hanterings bibliotek måste du autentisera med Azure Active Directory (AAD). AAD kräver att du autentiseras som tjänstens huvud namn, vilket ger åtkomst till dina Azure-resurser. Information om hur du skapar ett huvud namn för tjänsten finns i någon av följande artiklar:  

* [Använd Azure Portal för att skapa Active Directory program och tjänstens huvud namn som har åtkomst till resurser](../active-directory/develop/howto-create-service-principal-portal.md)
* [Använd Azure PowerShell för att skapa ett huvudnamn för tjänsten för resursåtkomst](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Använd Azure CLI för att skapa tjänstens huvudnamn för att få åtkomst till resurser](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

De här självstudierna ger dig ett `AppId` (klient-ID), `TenantId` och `ClientSecret` (autentiseringsnyckel) som används för autentisering av hanterings biblioteken. Du måste ha **ägar** behörigheter för den resurs grupp som du vill köra.

## <a name="programming-pattern"></a>Programmerings mönster

Mönstret för att manipulera eventuella Event Hubs resurser följer ett gemensamt protokoll:

1. Hämta en token från AAD med hjälp av `Microsoft.IdentityModel.Clients.ActiveDirectory` biblioteket.
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

1. Ange `CreateOrUpdate` parametrarna till dina angivna värden.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Kör anropet.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Nästa steg
* [Exempel på .NET-hantering](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Referens för Microsoft. Azure. Management. EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 

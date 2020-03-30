---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 7f7dc1483002c2bdfe3227a8aade8dbf2a8da417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70803014"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Skaffa en Azure Resource Manager-token
Azure Active Directory måste autentisera alla uppgifter som du utför på resurser med Hjälp av Azure Resource Manager. Exemplet som visas här använder lösenordsautentisering, för andra metoder se [Autentisering av Azure Resource Manager-begäranden][lnk-authenticate-arm].

1. Lägg till följande kod i **huvudmetoden** i Program.cs för att hämta en token från Azure AD med hjälp av program-ID och lösenord.
   
    ```csharp
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. Skapa ett **ResourceManagementClient-objekt** som använder token genom att lägga till följande kod i slutet av **huvudmetoden:**
   
    ```csharp
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Skapa eller hämta en referens till den resursgrupp du använder:
   
    ```csharp
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
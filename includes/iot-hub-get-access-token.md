---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: ab9d059ede2464318205a90b8ac738727e8d89a4
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166366"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Hämta en Azure Resource Manager-token
Azure Active Directory måste autentisera alla uppgifter som du kan utföra på resurser med hjälp av Azure Resource Manager. Det här exemplet använder lösenordsautentisering, andra metoder finns [autentisering av Azure Resource Manager begär][lnk-authenticate-arm].

1. Lägg till följande kod till den **Main** -metod i Program.cs att hämta en token från Azure AD med program-id och lösenord.
   
    ```
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
2. Skapa en **ResourceManagementClient** objekt som använder token genom att lägga till följande kod i slutet av den **Main** metoden:
   
    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Skapa eller hämta en referens till den resursgrupp som du använder:
   
    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
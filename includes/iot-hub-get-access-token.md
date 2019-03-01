---
author: robinsh
ms.author: robin.shahan
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: e2d6b6413d1e397eaa3c53f28394dcf321dac729
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011784"
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
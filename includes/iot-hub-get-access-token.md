---
ms.openlocfilehash: b6ea8c7b3a6374572c8bd31e3c62b788efbafcbc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65827480"
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

[lnk-authenticate-arm]: https://msdn.microsoft.com/zh-cn/library/azure/dn790557.aspx
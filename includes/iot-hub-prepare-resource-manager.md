---
author: robinsh
ms.author: robin.shahan
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: b7d043b15cad23c9c9eca60c9a24096fee12e14a
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319744"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Förbereda för att autentisera begäranden till Azure Resource Manager
Du måste autentisera alla åtgärder som du kan utföra på resurser med hjälp av den [Azure Resource Manager] [ lnk-authenticate-arm] med Azure Active Directory (AD). Det enklaste sättet att konfigurera detta är att använda PowerShell eller Azure CLI.

Installera den [Azure PowerShell-cmdlets] [ lnk-powershell-install] innan du fortsätter.

Följande steg visar hur du ställer in lösenordsautentisering för ett AD-program med hjälp av PowerShell. Du kan köra dessa kommandon i en PowerShell-session som standard.

1. Logga in på Azure-prenumerationen med följande kommando:

    ```powershell
    Connect-AzureRmAccount
    ```

1. Om du har flera Azure-prenumerationer kan får logga in på Azure du åtkomst till alla Azure-prenumerationer som är associerade med dina autentiseringsuppgifter. Använd följande kommando för att lista de Azure-prenumerationerna som du kan använda:

    ```powershell
    Get-AzureRMSubscription
    ```

    Använd följande kommando för att välja prenumeration som du vill använda för att köra kommandon för att hantera din IoT-hubb. Du kan antingen använda prenumerationsnamnet eller ID:t från utdata från föregående kommando:

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Anteckna din **TenantId** och **SubscriptionId**. Du behöver dem senare.
3. Skapa ett nytt Azure Active Directory-program som använder du följande kommando och ersätter platshållare:
   
   * **{Visningsnamn}:** ett visningsnamn för programmet som **MySampleApp**
   * **{URL-Adressen}:** URL: en för din app som startsida **http:\//mysampleapp/home**. Denna URL behöver inte pekar på ett riktigt program.
   * **{Program-ID}:** En unik identifierare som **http:\//mysampleapp**. Denna URL behöver inte pekar på ett riktigt program.
   * **{Password}:** Ett lösenord som du använder för att autentisera med din app.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Anteckna den **ApplicationId** för det program du skapade. Du behöver den senare.
5. Skapa ett nytt huvudnamn för tjänsten med följande kommando, ersätta **{MyApplicationId}** med den **ApplicationId** från föregående steg:
   
    ```powershell
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Konfigurera en rolltilldelning med följande kommando, ersätta **{MyApplicationId}** med din **ApplicationId**.
   
    ```powershell
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Du har nu skapat de Azure AD-program som gör det möjligt att autentisera från dina anpassade C# program. Senare i den här självstudien behöver du följande värden:

* TenantId
* SubscriptionId
* ApplicationId
* Lösenord

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps

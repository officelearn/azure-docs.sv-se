---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 4eb794fa35164e3f86a5e3d6f67d446321f91f0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67133038"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Förbereda för att autentisera Azure Resource Manager-begäranden
Du måste autentisera alla åtgärder som du utför på resurser med Hjälp av [Azure Resource Manager][lnk-authenticate-arm] med Azure Active Directory (AD). Det enklaste sättet att konfigurera detta är att använda PowerShell eller Azure CLI.

Installera [Azure PowerShell-cmdlets][lnk-powershell-install] innan du fortsätter.

Följande steg visar hur du konfigurerar lösenordsautentisering för ett AD-program med PowerShell. Du kan köra dessa kommandon i en vanlig PowerShell-session.

1. Logga in på din Azure-prenumeration med följande kommando:

    ```powershell
    Connect-AzAccount
    ```

1. Om du har flera Azure-prenumerationer ger inloggning till Azure dig åtkomst till alla Azure-prenumerationer som är associerade med dina autentiseringsuppgifter. Använd följande kommando för att lista de Azure-prenumerationer som är tillgängliga för dig att använda:

    ```powershell
    Get-AzSubscription
    ```

    Använd följande kommando för att välja prenumeration som du vill använda för att köra kommandona för att hantera din IoT-hubb. Du kan antingen använda prenumerationsnamnet eller ID:t från utdata från föregående kommando:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Anteckna din **TenantId** och **SubscriptionId**. Du behöver dem senare.
3. Skapa ett nytt Azure Active Directory-program med följande kommando och ersätt platsinnehavare:
   
   * **{Visningsnamn}:** ett visningsnamn för ditt program, till exempel **MySampleApp**
   * **{Url till startsidan}:** WEBBADRESSEN till startsidan för din app, till exempel **http:\//mysampleapp/home**. Den här WEBBADRESSen behöver inte peka på ett verkligt program.
   * **{Programidentifierare}:** En unik identifierare som **http:\//mysampleapp**. Den här WEBBADRESSen behöver inte peka på ett verkligt program.
   * **{Lösenord}:** Ett lösenord som du använder för att autentisera med din app.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Anteckna **ApplicationId** för det program som du skapade. Du behöver den här senare.
5. Skapa ett nytt tjänsthuvudnamn med följande kommando och ersätt **{MyApplicationId}** med **ApplicationId** från föregående steg:
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Konfigurera en rolltilldelning med följande kommando och ersätt **{MyApplicationId}** med **applicationid**.
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Du har nu skapat Azure AD-programmet som gör att du kan autentisera från ditt anpassade C#-program. Du behöver följande värden senare i den här självstudien:

* TenantId
* SubscriptionId
* ApplicationId
* lösenord

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: /powershell/azure/install-az-ps

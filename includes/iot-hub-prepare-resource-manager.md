## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Förbereda för att autentisera Azure Resource Manager-begäranden
Du måste autentisera alla åtgärder som du utför på resurser med hjälp av den [Azure Resource Manager] [ lnk-authenticate-arm] med Azure Active Directory (AD). Det enklaste sättet att konfigurera det här är att använda PowerShell eller Azure CLI.

Installera den [Azure PowerShell-cmdlets] [ lnk-powershell-install] innan du fortsätter.

Följande steg visar hur du ställer in en lösenordsautentisering för ett AD-program med hjälp av PowerShell. Du kan köra dessa kommandon i en standard PowerShell-session.

1. Logga in på Azure-prenumerationen med följande kommando:

    ```powershell
    Connect-AzureRmAccount
    ```

1. Om du har flera Azure-prenumerationer, ger logga in på Azure åtkomst till alla de Azure-prenumerationer som är kopplade till dina autentiseringsuppgifter. Använd följande kommando för att lista de Azure-prenumerationerna som du kan använda:

    ```powershell
    Get-AzureRMSubscription
    ```

    Använd följande kommando för att välja prenumeration som du vill använda för att köra kommandon för att hantera din IoT-hubb. Du kan antingen använda prenumerationsnamnet eller ID:t från utdata från föregående kommando:

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Anteckna din **TenantId** och **SubscriptionId**. Du behöver dem senare.
3. Skapa ett nytt Azure Active Directory-program som använder du följande kommando och ersätter innehavare plats:
   
   * **{Visningsnamn}:** ett visningsnamn för tillämpningsprogrammet som **MySampleApp**
   * **{URL-Adressen}:** URL-Adressen till startsidan för din app som **http://mysampleapp/home**. Denna URL behöver inte peka på ett riktigt program.
   * **{Programidentifierare}:** en unik identifierare som **http://mysampleapp**. Denna URL behöver inte peka på ett riktigt program.
   * **{Lösenord}:** ett lösenord som används för att autentisera med din app.
     
     ```powershell
     New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password {Password}
     ```
4. Anteckna den **ApplicationId** för det program som du skapade. Du behöver detta senare.
5. Skapa ett nytt huvudnamn för tjänsten med följande kommando, där du ersätter **{MyApplicationId}** med den **ApplicationId** från föregående steg:
   
    ```powershell
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Konfigurera en rolltilldelning med följande kommando, där du ersätter **{MyApplicationId}** med din **ApplicationId**.
   
    ```powershell
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Du är nu klar att skapa Azure AD-program som gör det möjligt att autentisera från din anpassade C#-program. Senare i den här kursen behöver du följande värden:

* Klient-ID
* SubscriptionId
* ApplicationId
* Lösenord

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps

## Vorbereiten der Authentifizierung von Anforderungen des Ressourcen-Managers

Sie müssen alle Vorgänge, die Sie ausführen, auf Ressourcen mit Authentifizieren der [Azure Resource Manager][lnk-authenticate-arm] mit Azure Active Directory (AD). Die einfachste Möglichkeit hierzu ist die Verwendung von PowerShell oder der Azure-Befehlszeilenschnittstelle.

Wenn Sie nicht installiert haben [Azure PowerShell 1.0][lnk-powershell-install], Sie können dazu die folgenden PowerShell-Befehle. Sie müssen PowerShell als Administrator ausführen.

```
# Install the Azure Resource Manager modules from PowerShell Gallery
Install-Module AzureRM
Install-AzureRM

# Install the Azure Service Management module from PowerShell Gallery
Install-Module Azure
```

Die folgenden Schritte zeigen, wie Sie die Kennwortauthentifizierung für eine AD-Anwendung mithilfe von PowerShell einrichten. Sie können diese Befehle in einer standardmäßigen PowerShell-Sitzung ausführen.

1. Melden Sie sich mithilfe des folgenden Befehls bei Ihrem Azure-Abonnement an:

    ```
    Login-AzureRmAccount
    ```

2. Notieren Sie sich Ihre **TenantId** und **SubscriptionId**. Diese werden später benötigt werden.

3. Erstellen Sie mit dem folgenden Befehl eine neue Azure Active Directory-Anwendung, und ersetzen Sie die Platzhalter:

    - **{Anzeigename}:** einen Anzeigenamen für Ihre Anwendung z. B. **MySampleApp**
    - **{URL der Homepage}:** die URL der Startseite der app wie z. B. **http://mysampleapp/home**. Diese URL muss nicht auf eine echte Anwendung verweisen.
    - **{ID}:** ein eindeutiger Bezeichner, wie z. B. **http://mysampleapp**. Diese URL muss nicht auf eine echte Anwendung verweisen.
    - **{Password}:** ein Kennwort, das Sie zur Authentifizierung mit Ihrer Anwendung verwenden werden.

    ```
    New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} IdentifierUris {Application identifier} -Password {Password}
    ```
    
4. Notieren Sie sich die **ApplicationId** der Anwendung, die Sie erstellt haben. Sie benötigen sie später.

5. Erstellen Sie einen neuen Dienstprinzipal mithilfe des folgenden Befehls, und Ersetzen Sie **{MyApplicationId}** mit der **ApplicationId** aus dem vorherigen Schritt:

    ```
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
    
6. Einrichten eine rollenzuweisung mithilfe des folgenden Befehls, und Ersetzen Sie **{MyApplicationId}** mit Ihrem **ApplicationId**.

    ```
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```
    
Sie haben jetzt eine Azure AD-Anwendung fertig erstellt, mit der die Authentifizierung aus einer benutzerdefinierten C#-Anwendung möglich ist. Die folgenden Werte werden Sie später in diesem Tutorial benötigen:

- TenantId
- SubscriptionId
- ApplicationId
- Kennwort

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://azure.microsoft.com/en-us/blog/azps-1-0-pre/



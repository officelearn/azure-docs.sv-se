## Ein Ressourcen-Manager-Token abrufen

Azure Active Directory muss alle Aufgaben authentifizieren, die Sie mithilfe des Azure-Ressourcen-Managers für die Ressourcen ausführen. Im hier gezeigte Beispiel Kennwortauthentifizierung verwendet, andere Ansätze finden Sie unter [Authenticating Azure Resource Manager-Anforderungen][lnk-authenticate-arm].

1. Fügen Sie den folgenden Code der **Main** -Methode in Program.cs zum Abrufen eines Tokens von Azure AD, die mit der Anwendung-Id und Kennwort.

    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.windows.net/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
    
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```

2. Erstellen einer **ResourceManagementClient** -Objekt, das das Token verwendet wird, indem Sie den folgenden Code am Ende hinzufügen der **Main** Methode:

    ```
    var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken);
    var client = new ResourceManagementClient(creds);
    ```

3. Erstellen Sie oder erhalten Sie einen Verweis auf die Ressourcengruppe, die Sie verwenden:

    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdateAsync(rgName,
        new ResourceGroup("East US")).Result;
    if (rgResponse.StatusCode != HttpStatusCode.Created
        && rgResponse.StatusCode != HttpStatusCode.OK)
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx



Im vorhergehenden Beispiel wurde eine Standardanmeldung gezeigt, bei der der Client bei jedem Starten der App sowohl den Identitätsanbieter als auch den mobilen Dienst kontaktieren muss. Diese Methode ist nicht nur ineffizient, sie kann auch zu nutzungsbezogenen Problemen führen, wenn eine große Anzahl von Kunden die App gleichzeitig starten sollte. Ein besserer Ansatz ist es daher, den von Mobile Services zurückgegebenen Authentifizierungstoken zwischenzuspeichern und vor einer anbieterbasierten Anmeldung zu verwenden. 

>[AZURE.NOTE]Sie können das unabhängig davon, ob Sie clientverwaltete oder dienstverwaltete Authentifizierung verwenden von Mobile Services ausgestellten Token Zwischenspeichern. In diesem Lernprogramm wird die dienstverwaltete Authentifizierung verwendet.

1. Fügen Sie in der Projektdatei "MainPage.Xaml.cs" die folgenden **mit** Anweisungen:

        using System.IO.IsolatedStorage;
        using System.Security.Cryptography;     

2. Ersetzen Sie die **AuthenticateAsync** -Methode durch den folgenden Code:

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            string message;
            // This sample uses the Facebook provider.
            var provider = "Facebook";

            // Provide some additional app-specific security for the encryption.
            byte [] entropy = { 1, 8, 3, 6, 5 };

            // Authorization credential.
            MobileServiceUser user = null;

            // Isolated storage for the app.
            IsolatedStorageSettings settings =
                IsolatedStorageSettings.ApplicationSettings;

            while (user == null)
            {
                // Try to get an existing encrypted credential from isolated storage.                    
                if (settings.Contains(provider))
                {
                    // Get the encrypted byte array, decrypt and deserialize the user.
                    var encryptedUser = settings[provider] as byte[];
                    var userBytes = ProtectedData.Unprotect(encryptedUser, entropy);
                    user = JsonConvert.DeserializeObject<MobileServiceUser>(
                        System.Text.Encoding.Unicode.GetString(userBytes, 0, userBytes.Length));
                }
                if (user != null)
                {
                    // Set the user from the stored credentials.
                    App.MobileService.CurrentUser = user;

                    try
                    {
                        // Try to return an item now to determine if the cached credential has expired.
                        await App.MobileService.GetTable<TodoItem>().Take(1).ToListAsync();
                    }
                    catch (MobileServiceInvalidOperationException ex)
                    {
                        if (ex.Response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
                        {
                            // Remove the credential with the expired token.
                            settings.Remove(provider);
                            user = null;
                            continue;
                        }
                    }
                }
                else
                {
                    try
                    {
                        // Login with the identity provider.
                        user = await App.MobileService
                            .LoginAsync(provider);

                        // Serialize the user into an array of bytes and encrypt with DPAPI.
                        var userBytes = System.Text.Encoding.Unicode
                            .GetBytes(JsonConvert.SerializeObject(user));
                        byte[] encryptedUser = ProtectedData.Protect(userBytes, entropy);

                        // Store the encrypted user credentials in local settings.
                        settings.Add(provider, encryptedUser);
                        settings.Save();
                    }
                    catch (MobileServiceInvalidOperationException ex)
                    {
                        message = "You must log in. Login Required";
                    }
                }
                message = string.Format("You are now logged in - {0}", user.UserId);
                MessageBox.Show(message);
            }
        }

    In dieser Version von **AuthenticateAsync**, versucht die app, die Anmeldeinformationen verschlüsselten im lokalen Speicher für den Zugriff auf den mobilen Dienst gespeichert. Über eine einfache Abfrage wird sichergestellt, dass das gespeicherte Token nicht abgelaufen ist. Bei Rückgabe eines 401-Fehlers wird ein normaler anbieterbasierter Anmeldungsversuch unternommen. Dies erfolgt auch dann, wenn keine gespeicherten Anmeldeinformationen vorhanden sind.    

    >[AZURE.NOTE]Diese app prüft während der Anmeldung abgelaufene Token, aber Tokengültigkeitsdauer kann nach der Authentifizierung auftreten, wenn die app verwendet wird. Für eine Lösung zur Behandlung von Autorisierungsfehlern im Zusammenhang mit abgelaufenen Token finden Sie im Beitrag [Zwischenspeichern und Behandeln von abgelaufenen Token in Azure Mobile Services managed SDK](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 
    
3. Starten Sie die App zweimal neu.

    Beachten Sie, dass beim ersten Start die Anmeldung beim Anbieter wieder erforderlich ist. Beim zweiten Neustart jedoch werden die zwischengespeicherten Anmeldeinformationen verwendet, und die Anmeldung wird umgangen. 


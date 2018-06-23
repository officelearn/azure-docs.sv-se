
1. Lägg till följande i filen MainPage.xaml.cs projekt **med** instruktioner:
   
        using System.Linq;        
        using Windows.Security.Credentials;
2. Ersätt den **AuthenticateAsync** metod med följande kod:
   
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
   
            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;
   
            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;
   
            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }
   
            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;
   
                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;
   
                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: http://aka.ms/jww5vp.
   
                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Sign in with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider, "{url_scheme_of_your_app}");
   
                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);
   
                    success = true;
                    message = string.Format("You are now signed in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must sign in. Sign-In Required";
                }
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
   
            return success;
        }
   
    I den här versionen av **AuthenticateAsync**, appen försöker använda autentiseringsuppgifter som lagras i den **PasswordVault** komma åt tjänsten. En vanlig inloggning också utförs när det finns inga lagrade autentiseringsuppgifter.
   
   > [!NOTE]
   > En cachelagrad token kan ha gått och token upphör att gälla kan även uppstå efter autentisering när appen används. Information om hur du avgör om en token har upphört att gälla finns [söka efter utgångna autentiseringstoken](http://aka.ms/jww5vp). En lösning för att hantera auktorisering fel som rör token upphör att gälla, finns i inlägg [cachelagring och hantering av utgångna token i Azure Mobile Services hanteras SDK](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 
   > 
   > 
3. Starta om appen.
   
    Observera att på första start, logga in med providern krävs igen. Dock på andra omstarten cachelagrade autentiseringsuppgifter används och logga in kringgås. 


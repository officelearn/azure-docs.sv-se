---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: d71d52257b6e8cfa243207c9bfdb5c7de7d3dd37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67188024"
---
1. Lägg till **följande** i MainPage.xaml.cs projektfilen:
   
        using System.Linq;        
        using Windows.Security.Credentials;
2. Ersätt **metoden AuthenticateAsync** med följande kod:
   
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
                // expired, as shown in this post: https://aka.ms/jww5vp.
   
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
   
    I den här versionen av **AuthenticateAsync**försöker appen använda autentiseringsuppgifter som lagras i **PasswordVault** för att komma åt tjänsten. En vanlig inloggning utförs också när det inte finns någon lagrad autentiseringsuppgifter.
   
   > [!NOTE]
   > En cachelagrad token kan ha upphört att gälla och tokens förfallodatum kan också uppstå efter autentisering när appen används. Mer information om hur du tar reda på om en token har upphört att gälla finns [i Sök efter utgångna autentiseringstoken](https://aka.ms/jww5vp). En lösning för hantering av auktoriseringsfel relaterade till förfallna token finns i post [Caching och hantering av utgångna token i Azure Mobile Services hanterade SDK](https://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 
   > 
   > 
3. Starta om appen två gånger.
   
    Observera att vid den första starten krävs inloggning hos leverantören igen. Vid den andra omstarten används dock cachelagrade autentiseringsuppgifter och inloggning kringgås. 


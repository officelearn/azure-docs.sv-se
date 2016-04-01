
1. Öffnen Sie die freigegebene Projektdatei "MainPage.cs", und fügen Sie der MainPage-Klasse den folgenden Codeausschnitt hinzu:
    
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now signed in - {0}", user.UserId);

                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }

    Dieser Code authentifiziert den Benutzer mit einer Facebook-Anmeldung. Wenn Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den Wert der **MobileServiceAuthenticationProvider** oben auf den Wert für den Anbieter.

3. Kommentar aus, oder löschen Sie den Aufruf der **RefreshTodoItems** in der vorhandenen **OnNavigatedTo** Methode außer Kraft setzen.

    Dadurch wird sichergestellt, dass die Daten erst geladen werden, nachdem der Benutzer authentifiziert wurde. Als Nächstes fügen Sie eine **Anmelden** Schaltfläche, um die app, die Authentifizierung auslöst.

4. Fügen Sie den folgenden Codeausschnitt zur MainPage-Klasse hinzu:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Hide the login button and load items from the mobile app.
                ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
        
5. Klicken Sie im Windows Store-app-Projekt öffnen Sie die Projektdatei "MainPage.xaml", und fügen Sie die folgenden **Schaltfläche** Element vor dem Element, definiert die **Speichern** Schaltfläche:

        <Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

6. Fügen Sie in der Windows Phone Store-app-Projekt die folgenden **Schaltfläche** Element in der **ContentPanel**, nach der **Textfeld** Element:

        <Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
            Margin="10, 0, 0, 0" Visibility="Visible">Sign in</Button>

8. Öffnen Sie die gemeinsam genutzte Projektdatei "App.xaml.cs", und fügen Sie folgenden Code hinzu:

        protected override void OnActivated(IActivatedEventArgs args)
        {
            // Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
                // Completes the sign-in process started by LoginAsync.
                // Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif

            base.OnActivated(args);
        }

    Wenn die **OnActivated** Methode bereits vorhanden ist, fügen Sie einfach die `#if...#endif` Codeblock.

9. Drücken Sie die F5-Taste zum Ausführen von Windows Store-app, klicken Sie auf die **Anmelden** gewählt haben, und melden Sie sich die app mit dem Identitätsanbieter Ihrer Wahl. 

    Wenn Sie erfolgreich angemeldet sind, sollte die App ohne Fehler ausgeführt werden, und Sie sollten in der Lage sein, Ihr Back-End abzufragen und Daten zu aktualisieren.

10. Mit der rechten Maustaste in des Windows Phone Store-app-Projekts, klicken Sie auf **Set as StartUp Project**, wiederholen Sie den vorherigen Schritt, um sicherzustellen, dass die Windows Phone Store-app ebenfalls ordnungsgemäß ausgeführt wird.  

 


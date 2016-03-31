
Als Nächstes müssen Sie die Art ändern, wie Pushbenachrichtigungen registriert werden, um sicherzustellen, dass der Benutzer vor dem Registrierungsversuch authentifiziert wird. 

1. Öffnen Sie in Visual Studio im Projektmappen-Explorer die Datei "App.Xaml.cs" und in der **Application_Launching** -Ereignishandler auskommentieren oder löschen Sie den Aufruf an die **AcquirePushChannel** Methode. 
 
2. Ändern Sie den Zugriff der **AcquirePushChannel** aus `private`  `public` und Hinzufügen der `static` Modifizierer. 

3. Öffnen Sie die Datei "MainPage.Xaml.cs", und Ersetzen Sie die **OnNavigatedTo** Überschreibungsmethode durch Folgendes:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            App.AcquirePushChannel();
            RefreshTodoItems();
        }


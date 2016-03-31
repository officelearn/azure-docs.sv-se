
##<a name="update-app"></a>Aktualisieren der App zum Aufruf der benutzerdefinierten API

1. Öffnen Sie in Visual Studio die Datei MainPage.xaml in Ihrem schnellstartprojekt, suchen Sie die **Schaltfläche** Element mit dem Namen `ButtonRefresh`, und Ersetzen Sie ihn durch den folgenden XAML-Code: 

        <StackPanel Orientation="Horizontal">
            <Button Margin="72,0,0,0" Name="ButtonRefresh" 
                    Click="ButtonRefresh_Click">Refresh</Button>
            <Button Margin="12,0,0,0" Name="ButtonCompleteAll" 
                    Click="ButtonCompleteAll_Click">Complete All</Button>
        </StackPanel>

    Auf diese Weise wird eine neue Schaltfläche zur Seite hinzugefügt. 

2. Öffnen Sie die Codedatei MainPage.xaml.cs, und fügen Sie den folgenden Klassendefinitionscode hinzu:

        public class MarkAllResult
        {
            public int Count { get; set; }
        }

    Diese Klasse dient zur Aufnahme des von der benutzerdefinierten API zurückgegebenen Zeilenanzahlwerts. 

3. Suchen Sie die **RefreshTodoItems** -Methode in der **MainPage** Klasse, und stellen Sie sicher, dass die `query` definiert ist, mithilfe der Folgendes **in denen** Methode:

        .Where(todoItem => todoItem.Complete == false)

    Hierdurch werden die Elemente so gefiltert, dass abgeschlossene Elemente von der Abfrage nicht zurückgegeben werden.

3. In der **MainPage** -Klasse verwenden, fügen Sie die folgende Methode:

        private async void ButtonCompleteAll_Click(object sender, RoutedEventArgs e)
        {
            string message;
            try
            {
                // Asynchronously call the custom API using the POST method. 
                var result = await App.MobileService
                    .InvokeApiAsync<MarkAllResult>("completeAll", 
                    System.Net.Http.HttpMethod.Post, null);
                message =  result.Count + " item(s) marked as complete.";
                RefreshTodoItems();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                message = ex.Message;                
            }
        
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    Diese Methode verarbeitet das **Klicken Sie auf** -Ereignis für die Schaltfläche "Neu". Die [InvokeApiAsync](http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx) Methode wird aufgerufen, auf dem Client, der eine POST-Anforderung an die neue benutzerdefinierte API sendet. Das von der benutzerdefinierten API zurückgegebene Ergebnis wird in einem Meldungsdialogfeld angezeigt, ebenso wie jegliche Fehler.

## <a name="test-app"></a>Testen der App

1. Drücken Sie in Visual Studio die **F5** um das Projekt neu erstellen und die app zu starten.

2. Geben Sie in der app Text in **Insert a TodoItem**, klicken Sie dann auf **Speichern**.

3. Wiederholen Sie den vorherigen Schritt, bis Sie der Liste mehrere todo-Einträge hinzugefügt haben.

4. Klicken Sie auf die **Complete All** Schaltfläche.

    ![](./media/mobile-services-windows-store-dotnet-call-custom-api/mobile-custom-api-windows-store-completed.png)

    Ein Meldungsdialogfeld wird angezeigt, das die Zahl der als abgeschlossen markierten Elemente angibt, und die gefilterte Abfrage wird erneut ausgeführt, um alle Elemente aus der Liste zu löschen.



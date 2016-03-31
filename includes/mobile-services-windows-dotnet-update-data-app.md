
2. Ersetzen Sie die TodoItem-Klassendefinition durch den folgenden Code: 

        public class TodoItem
        {
            public string Id { get; set; }
    
            [Newtonsoft.Json.JsonProperty(PropertyName = "text")]  
            public string Text { get; set; }
    
            [Newtonsoft.Json.JsonProperty(PropertyName = "complete")]  
            public bool Complete { get; set; }
        }
    
    Die **JsonPropertyAttribute** wird verwendet, um die Zuordnung von Eigenschaftsnamen im Clienttyp zu Spaltennamen in der zugrunde liegenden Datentabelle definiert.

    >[AZURE.NOTE] In einem universellen Windows-app-Projekt wird die TodoItem-Klasse in der separaten Codedatei im Ordner "dataModel" definiert.

1. Fügen Sie in der Datei "MainPage.cs" die folgenden Anweisungen hinzu, oder heben Sie deren Auskommentierung auf: 

        using Microsoft.WindowsAzure.MobileServices;


4. Kommentieren Sie oder löschen Sie die Zeile, die der vorhandenen Eintragssammlung und kommentieren oder fügen Sie die folgenden Zeilen, und Ersetzen _& Lt; YourClient & Gt;_ mit dem `MobileServiceClient` Feld in der Datei "App.Xaml.cs" hinzugefügt werden, wenn Sie das Projekt mit den mobilen Dienst verbunden: 

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.<yourClient>.GetTable<TodoItem>();
          
    Dieser Code erstellt eine mobile dienstunterstützende Bindungssammlung (items) und eine Proxyklasse für die Datenbanktabelle (todoTable). 


4. In der **InsertTodoItem** -Methode entfernen Sie die Codezeile, die festlegt der **TodoItem.Id** Eigenschaft hinzufügen der **Async** Modifizierer, und kommentieren Sie die folgende Codezeile: 

        await todoTable.InsertAsync(todoItem);


    Mit diesem Code wird ein neuer Eintrag in die Tabelle eingefügt. 

5. Ersetzen Sie die **RefreshTodoItems** -Methode durch den folgenden Code: 

        private async void RefreshTodoItems()
        {
            MobileServiceInvalidOperationException exception = null;
            try
            {
                // Query that returns all items.   
                items = await todoTable.ToCollectionAsync();             
            }
            catch (MobileServiceInvalidOperationException e)
            {
                exception = e;
            }
            if (exception != null)
            {
                await new MessageDialog(exception.Message, "Error loading items").ShowAsync();
            }
            else
            {
                ListItems.ItemsSource = items;
                this.ButtonSave.IsEnabled = true;
            }    
        }

    Dadurch wird die Bindung an die Auflistung von Elementen in `todoTable`, die alle enthält die **TodoItem** aus dem mobilen Dienst zurückgegebenen Objekte. Wenn beim Ausführen der Abfrage ein Problem auftritt, wird ein Meldungsfeld geöffnet, in dem die Fehler angezeigt werden. 

6. In der **UpdateCheckedTodoItem** -Methode hinzufügen der **Async** Modifizierer, und kommentieren Sie die folgende Codezeile: 

        await todoTable.UpdateAsync(item);

    Damit wird eine Eintragsaktualisierung an den mobilen Dienst gesendet. 

Nachdem nun die App für die Verwendung von Mobile Services als Back-End-Speicher aktualisiert ist, können Sie die App mit Mobile Services testen.


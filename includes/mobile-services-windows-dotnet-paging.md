

1. Öffnen Sie in Visual Studio das Projekt, das Sie geändert werden, wenn Sie das Lernprogramm **Erste Schritte mit Daten**.

2. Drücken Sie die **F5** Taste, um die Anwendung auszuführen, und geben Sie Text in **Insert a TodoItem** und klicken Sie auf **Speichern**.

3. Wiederholen Sie den vorherigen Schritt mindestens drei Mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält. 

2. Ersetzen Sie in der Datei "MainPage.Xaml.cs" die **RefreshTodoItems** -Methode durch den folgenden Code:

        private async void RefreshTodoItems()
        {
            // Define a filtered query that returns the top 3 items.
            IMobileServiceTableQuery<TodoItem> query = todoTable
                            .Where(todoItem => todoItem.Complete == false)
                           .Take(3);
            items = await query.ToCollectionAsync();
            ListItems.ItemsSource = items;
        }

    Diese Abfrage gibt bei Ausführung während der Datenbindung die ersten drei Elemente zurück, die nicht als abgeschlossen markiert sind.

3. Drücken Sie die **F5** um die Anwendung auszuführen.

    Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden. 

4. (Optional) Anzeigen des URI der Anfrage an den mobilen Dienst mithilfe der nachrichtenprüfsoftware, z. B. Browser-Entwicklertools oder [Fiddler]. 

    Beachten Sie, dass die `Take(3)`-Methode im Abfrage-URI in die Abfrageoption `$top=3` übersetzt wurde.

5. Update der **RefreshTodoItems** -Methode durch den folgenden Code:
            
        private async void RefreshTodoItems()
        {
            // Define a filtered query that skips the first 3 items and 
            // then returns the next 3 items.
            IMobileServiceTableQuery<TodoItem> query = todoTable
                           .Where(todoItem => todoItem.Complete == false)
                           .Skip(3)
                           .Take(3);
            items = await query.ToCollectionAsync();
            ListItems.ItemsSource = items;
        }

    Diese Abfrage überspringt die ersten drei Ergebnisse und gibt die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

    >[AZURE.NOTE]Dieses Lernprogramm verwendet eine vereinfachte Szenario übergibt fest codierte Werte an die <strong>nehmen</strong> und <strong>überspringen</strong> Methoden. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren. Sie können auch aufrufen, die  <strong>IncludeTotalCount</strong> Methode, um die Gesamtzahl der Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.

6. (Optional) Sehen Sie sich erneut den URI der Anfrage an den mobilen Service an. 

    Beachten Sie, dass die `Skip(3)`-Methode im Abfrage-URI in die Abfrageoption `$skip=3` übersetzt wurde.

<!-- URLs -->
[Fiddler]: http://go.microsoft.com/fwlink/?LinkID=262412


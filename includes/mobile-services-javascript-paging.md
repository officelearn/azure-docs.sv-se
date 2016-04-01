

1. Öffnen Sie in Visual Studio das Projekt, das Sie geändert werden, wenn Sie das Lernprogramm **Erste Schritte mit Daten**.

2. Drücken Sie die **F5** Taste, um die Anwendung auszuführen, und geben Sie Text in **Insert a TodoItem** und klicken Sie auf **Speichern**.

3. Wiederholen Sie den vorherigen Schritt mindestens drei Mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält. 

2. Ersetzen Sie in der Datei "default.js" die **RefreshTodoItems** -Methode durch den folgenden Code:

        var refreshTodoItems = function () {
            // Define a filtered query that returns the top 3 items.
            todoTable.where({ complete: false })
                .take(3)
                .read()
                .done(function (results) {
                    todoItems = new WinJS.Binding.List(results);
                    listItems.winControl.itemDataSource = todoItems.dataSource;
                });
        };

    Diese Abfrage gibt bei Ausführung während der Datenbindung die ersten drei Elemente zurück, die nicht als abgeschlossen markiert sind.

3. Drücken Sie die **F5** um die Anwendung auszuführen.

    Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden. 

4. (Optional) Anzeigen des URI der Anfrage an den mobilen Dienst mithilfe der nachrichtenprüfsoftware, z. B. Browser-Entwicklertools oder [Fiddler]. 

    Beachten Sie, dass die **take(3)** Methode wurde in die Abfrageoption übersetzt **$top = 3** im Abfrage-URI.

5. Update der **RefreshTodoItems** -Methode durch den folgenden Code:
            
        var refreshTodoItems = function () {
            // Define a filtered query that skips the first 3 items and 
            // then returns the next 3 items.
            todoTable.where({ complete: false })
                .skip(3)
                .take(3)
                .read()
                .done(function (results) {
                    todoItems = new WinJS.Binding.List(results);
                    listItems.winControl.itemDataSource = todoItems.dataSource;
                });
        };

    Diese Abfrage überspringt die ersten drei Ergebnisse und gibt die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

    > [AZURE.NOTE] Dieses Lernprogramm verwendet eine vereinfachte Szenario übergibt fest codierte Werte an die **nehmen** und **überspringen** Methoden. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren.  Sie können auch aufrufen, die  **IncludeTotalCount** Methode, um die Gesamtzahl der Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.

6. (Optional) Sehen Sie sich erneut den URI der Anfrage an den mobilen Service an. 

    Beachten Sie, dass die **skip(3)** Methode wurde in die Abfrageoption übersetzt **$skip = 3** im Abfrage-URI.

<!-- URLs -->
[Fiddler]: http://go.microsoft.com/fwlink/?LinkID=262412


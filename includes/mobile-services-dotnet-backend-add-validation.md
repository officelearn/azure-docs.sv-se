
Es empfiehlt sich, die Länge der Daten, die von Benutzern übermittelt werden, zu überprüfen. In diesem Abschnitt fügen Sie Code zum mobilen Service hinzu, der die Länge von Zeichenfolgendaten überprüft, die an den mobilen Service gesendet werden, und der Zeichenfolgen ablehnt, die zu lang sind, in diesem Fall länger als zehn Zeichen.

1. Starten Sie Visual Studio mit der **als Administrator ausführen** aus, und öffnen Sie die Projektmappe, das Projekt des mobilen Diensts, die enthält mit verwendet werden, in der [Getting Started] oder [Erste Schritte mit Daten](../articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md)  Lernprogramm.

2. Im Fenster Projektmappen-Explorer erweitern Sie die Aufgabenlisten-Serviceprojekt und **Contoller**. Öffnen Sie die Datei TodoItemController.cs, die Bestandteil des Projekts für den mobilen Service ist.  

    ![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-open-todoitemcontroller.png)

3. Ersetzen Sie die `PostTodoItem`-Methode durch die folgende Methode, mit der sichergestellt wird, dass die Textzeichenfolge nicht länger als 10 Zeichen ist. Für Elemente mit einer Textlänge von über 10 Zeichen gibt die Methode den HTTP-Statuscode 400 Bad Request inklusive einer Beschreibung zurück.


        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            if (item.Text.Length > 10)
            {
                return BadRequest("The Item's Text length must not be greater than 10.");
            }
            else
            {
                TodoItem current = await InsertAsync(item);
                return CreatedAtRoute("Tables", new { id = current.Id }, current);
            } 
        }



4. Klicken Sie mit der mit der rechten Maustaste auf das Dienstprojekt, und klicken Sie auf **Erstellen** Projekt des mobilen Diensts zu erstellen. Vergewissern Sie sich, dass keine Fehler aufgetreten sind.

    ![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-build-dotnet-service.png)

5. Klicken Sie mit der mit der rechten Maustaste auf das Dienstprojekt, und klicken Sie auf **Veröffentlichen**. Veröffentlichen des mobilen Diensts mit Ihrer Azure-Konto mit den veröffentlichungseinstellungen zuvor in verwendeten der [Getting Started] oder [Erste Schritte mit Daten](../articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md)  Lernprogramm.
 
     >[AZURE.NOTE] Alternativ können Sie mit dem in IIS Express lokal gehosteten Dienst testen. Weitere Informationen finden Sie unter der [Erste Schritte mit Daten](../articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md) Lernprogramm.

    ![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-publish-dotnet-service.png)





<!-- URLs. -->
[Getting Started]: ../articles/mobile-services/mobile-services-dotnet-backend-windows-store-dotnet-get-started.md



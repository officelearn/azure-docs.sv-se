
1. Erweitern Sie in Visual Studio Projektmappen-Explorer den **Controller** Ordner im Projekt mobilen Diensts. Öffnen Sie die Datei "TodoItemController.cs", und aktualisieren Sie die `PostTodoItem`-Methodendefinition mit dem folgenden Code:  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            // Create a WNS native toast.
            WindowsPushMessage message = new WindowsPushMessage();

            // Define the XML paylod for a WNS native toast notification 
            // that contains the text of the inserted item.
            message.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                                 @"<toast><visual><binding template=""ToastText01"">" +
                                 @"<text id=""1"">" + item.Text + @"</text>" +
                                 @"</binding></visual></toast>";
            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Dieser Code sendet eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags), nachdem ein todo-Eintrag eingefügt wurde. Im Falle eines Fehlers den Code ein Fehlerprotokoll Fehlerprotokolleintrag auf Hinzufügen wird der **Protokolle** Registerkarte des mobilen Diensts in der [klassischen Azure-Portal](https://manage.windowsazure.com/).

    >[AZURE.NOTE] Sie können vorlagenbenachrichtigungen verwenden, um eine einzelne Pushbenachrichtigung an Clients auf mehreren Plattformen zu senden. Weitere Informationen finden Sie unter [unterstützen mehrerer Geräteplattformen aus einem einzigen mobile Service](../articles/mobile-services-how-to-use-multiple-clients-single-service.md#push).

2. das Projekt für den mobilen Service erneut auf Azure veröffentlichen.





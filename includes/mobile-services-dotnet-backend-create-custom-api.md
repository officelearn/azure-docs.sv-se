

1. In Visual Studio Maustaste auf den Ordner Controller, und erweitern Sie **Hinzufügen**, klicken Sie dann auf **neu erstelltes Element**. Der Dialog "Add Scaffold" wird angezeigt.

2. Erweitern Sie **Azure Mobile Services**, klicken Sie auf **Azure Mobile Services benutzerdefinierten Controller**,  klicken Sie auf **Hinzufügen**, geben eine **Controllernamen** von `CompleteAllController`, und klicken Sie auf **Hinzufügen** erneut.

    ![Web-API-Dialogfeld "Add Scaffold"](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

    Dies erstellt eine neue leere Controllerklasse namens **CompleteAllController**.

    >[AZURE.NOTE]Wenn Ihr Dialogfeld keine Mobile Services-spezifischen Gerüste besitzt, erstellen Sie stattdessen einen neuen **Web API Controller - leer**. Fügen Sie in dieser neuen Controllerklasse eine öffentliche **Services** ab dem **ApiServices** Typ. Diese Eigenschaft wird zum Zugriff auf Server-spezifische Einstellungen vom Controller aus verwendet.

3. In **CompleteAllController.cs**, fügen Sie die folgenden **mit** Anweisungen.     Ersetzen Sie `todolistService` durch den Namespace Ihres Projekts für den mobilen Dienst, der aus dem Namen des mobilen Diensts und dem Präfix `Service` bestehen sollte.

        using System.Threading.Tasks;
        using todolistService.Models;

4. In **CompleteAllController.cs**, fügen Sie die folgende Klasse als Wrapper an den Client gesendete Antwort hinzu.

        // We use this class to keep parity with other Mobile Services
        // that use the JavaScript backend. This way the same client
        // code can call either type of Mobile Service backend.
        public class MarkAllResult
        {
            public int count;
        }

5. Fügen Sie folgenden Code zum neuen Controller hinzu. Ersetzen Sie `todolistContext` durch den Namen von DbContext für Ihr Datenmodell, der aus dem Namen des mobilen Diensts und dem Präfix `Context` bestehen sollte. Ersetzen Sie ebenso den Schemanamen in der UPDATE-Anweisung durch den Namen Ihres mobilen Diensts. Dieser Code verwendet die [Datenbankklasse](http://msdn.microsoft.com/library/system.data.entity.database.aspx) für den Zugriff auf die **TodoItems** Tabelle direkt für alle Elemente die erledigt-Kennzeichen zu setzen. Diese Methode unterstützt eine POST-Anfrage, und die Anzahl der geänderten Zeilen wird an den Client als Ganzzahl zurückgegeben.


        // POST api/completeall
        public async Task<MarkAllResult> Post()
        {
            using (todolistContext context = new todolistContext())
            {
                // Get the database from the context.
                var database = context.Database;

                // Create a SQL statement that sets all uncompleted items
                // to complete and execute the statement asynchronously.
                var sql = @"UPDATE todolist.TodoItems SET Complete = 1 " +
                            @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";

                var result = new MarkAllResult();
                result.count = await database.ExecuteSqlCommandAsync(sql);

                // Log the result.
                Services.Log.Info(string.Format("{0} items set to 'complete'.",
                    result.count.ToString()));

                return result;
            }
        }

    > [AZURE.TIP] With default permissions, anyone with the app key may call the custom API. However, the application key is not considered a secure credential because it may not be distributed or stored securely. Consider restricting access to only authenticated users for additional security.



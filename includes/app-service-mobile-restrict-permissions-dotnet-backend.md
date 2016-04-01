
Standardmäßig können APIs auf mobilen App-Back-Ends anonym aufgerufen werden. Als Nächstes müssen Sie den Zugriff auf authentifizierte Clients beschränken.  

+ **Node.js-Back-End (über das Portal)** :  
    
    In der Mobile App **Einstellungen**, klicken Sie auf **einfach Tabellen** und wählen Sie die Tabelle. Klicken Sie auf **Änderungsberechtigungen**, Option **Authentifizierter Zugriff nur** für alle Berechtigungen und klicken Sie dann auf **Speichern**. 

+ **.NET Backend (c#)**:  

    Navigieren Sie zu dem Serverprojekt **Controller** > **TodoItemController.cs**. Hinzufügen der `[Authorize]` -Attribut auf die **TodoItemController** -Klasse wie folgt. Um den Zugriff auf bestimmte Methoden zu beschränken, können Sie dieses Attribut auch nur auf diese Methoden anstelle der Klasse anwenden. Veröffentlichen Sie das Serverprojekt erneut.


        [Authorize]
        public class TodoItemController : TableController<TodoItem>

+ **Node.js-Back-End (über Node.js-Code)** :  
    
    Um für den Zugriff auf Tabellen eine Authentifizierung anzufordern, fügen Sie die folgende Zeile in das Node.js-Serverskript ein:


        table.access = 'authenticated';

    For more details, refer to [How to: Require authentication for access to tables](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). To learn how to download the quickstart code project from your site, see [How to: Download the Node.js backend quickstart code project using Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).




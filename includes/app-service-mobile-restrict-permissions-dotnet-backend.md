
Som standard anropas API: er i en Mobile Apps-serverdel anonymt. Därefter måste du begränsa åtkomsten till endast autentiserade klienter.  

* **Node.js tillbaka avslutas (via Azure portal)** :  

    I inställningarna för Mobilappar klickar du på **enkelt tabeller** och välj din tabell. Klicka på **ändra behörigheter**väljer **autentiserad åtkomst bara** för alla behörigheter och klicka sedan på **spara**.
* **.NET serverdel (C#)**:  

    I server-projekt går du till **domänkontrollanter** > **TodoItemController.cs**. Lägg till den `[Authorize]` attribut till den **TodoItemController** class, enligt följande. För att begränsa åtkomst till specifika metoder, kan du också använda det här attributet bara till de här metoderna i stället för klassen. Publicera om serverprojektet.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Node.js-serverdel (via Node.js-kod)** :  

    Lägg till följande rad Node.js server-skript för att kräva autentisering för tabellåtkomst:

        table.access = 'authenticated';

    Mer information finns i [så här: kräver autentisering för åtkomst till tabeller](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Information om hur du hämtar koden snabbstartsprojekt från webbplatsen finns [så här: ladda ned Node.js quickstart kod serverdelsprojektet med Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).

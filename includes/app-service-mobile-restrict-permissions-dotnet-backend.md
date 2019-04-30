---
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62113547"
---
Som standard kan API: er i en Mobile Apps-serverdelen anropas anonymt. Därefter måste du begränsa åtkomsten till endast autentiserade klienter.  

* **Node.js tillbaka sluta (via Azure portal)** :  

    I inställningarna för Mobilappar klickar du på **enkla tabeller** och välj din tabell. Klicka på **ändra behörigheter**väljer **autentiserad åtkomst endast** alla behörigheter och klicka sedan på **spara**.
* **.NET-serverdel (C#)**:  

    I serverprojektet, går du till **domänkontrollanter** > **TodoItemController.cs**. Lägg till den `[Authorize]` attributet den **TodoItemController** klassen enligt följande. Om du vill begränsa åtkomst till specifika metoder, kan du också använda det här attributet till dessa metoder i stället för klassen. Publicera om serverprojektet.

    ```
    [Authorize]
    public class TodoItemController : TableController<TodoItem>
    ```

* **Node.js-serverdel (via Node.js-kod)** :  

    Lägg till följande rad Node.js server-skript för att kräva autentisering för tabellåtkomst:

    ```
    table.access = 'authenticated';
    ```

    Mer information finns i [så här: Kräver autentisering för åtkomst till tabeller](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Läs hur du laddar ned snabbstartsprojekt för koden från din plats i [så här: Ladda ned den Node.js-serverdel kod snabbstartsprojektet med hjälp av Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).
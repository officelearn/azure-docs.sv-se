---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "67187836"
---
Som standard kan API: er i en Mobile Apps Server del anropas anonymt. Sedan måste du begränsa åtkomsten till endast autentiserade klienter.  

* **Node. js-Server delen (via Azure Portal)** :  

    I Mobile Apps inställningar klickar du på **enkla tabeller** och väljer din tabell. Klicka på **ändra behörigheter**, Välj **autentiserad åtkomst endast** för alla behörigheter och klicka sedan på **Spara**.
* **.NET-Server delC#()** :  

    I Server projektet navigerar du till **controllers** > **TodoItemController.cs**. Lägg till attributet i TodoItemController-klassen enligt följande. `[Authorize]` Om du bara vill begränsa åtkomsten till vissa metoder kan du även använda det här attributet precis för dessa metoder i stället för klassen. Publicera om Server projektet.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Node. js-backend (via Node. js-kod)** :  

    Om du vill kräva autentisering för tabell åtkomst lägger du till följande rad i Node. js-Server skriptet:

        table.access = 'authenticated';

    Mer information finns i [How to: Kräv autentisering för åtkomst till tabeller](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Information om hur du laddar ned snabb starts kod projektet från din webbplats [finns i How to: Hämta ett kod projekt för Node. js-Server delen](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)med git.

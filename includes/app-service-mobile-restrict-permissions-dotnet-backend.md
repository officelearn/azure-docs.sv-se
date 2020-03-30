---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187836"
---
Som standard kan API:er i en backdel för mobilappar anropas anonymt. Därefter måste du begränsa åtkomsten till endast autentiserade klienter.  

* **Nod.js-backend (via Azure-portalen):**  

    Klicka på **Enkla tabeller i** inställningarna för Mobilappar och välj tabellen. Klicka på **Ändra behörigheter,** välj **Autentiserat åtkomst endast** för alla behörigheter och klicka sedan på **Spara**.
* **.NET-backend (C#):**  

    I serverprojektet navigerar du till **Controllers** > **TodoItemController.cs**. Lägg `[Authorize]` till attributet i klassen **TodoItemController** enligt följande. Om du bara vill begränsa åtkomsten till specifika metoder kan du också använda det här attributet bara på dessa metoder i stället för klassen. Publicera om serverprojektet.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Nod.js backend (via Node.js-kod):**  

    Om du vill kräva autentisering för tabellåtkomst lägger du till följande rad i nod.js-serverskriptet:

        table.access = 'authenticated';

    Mer information finns i [Så här: Kräv autentisering för åtkomst till tabeller](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Mer information om hur du hämtar snabbstartskodprojektet från webbplatsen finns i Så här hämtar du [snabbstartskodprojektet Node.js-säkerhetskopiering med Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).

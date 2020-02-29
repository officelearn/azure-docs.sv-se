---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 71a6654acd436c27bd2370646dede81778113860
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186157"
---
#### <a name="applications"></a>[Program](#tab/applications/)

1. Välj **program**och välj sedan det webb program som ska ha åtkomst till API: et. Till exempel *webapp1*.
1. Välj **API-åtkomst** och sedan **Lägg till**.
1. I list rutan **Välj API** väljer du det API som webb programmet ska beviljas åtkomst till. Till exempel *webapi1*.
1. I list rutan **Välj omfång** väljer du de omfattningar som du definierade tidigare. Till exempel *demo. Read* och *demo. Write*.
1. Välj **OK**.

#### <a name="app-registrations-preview"></a>[Appregistreringar (för hands version)](#tab/app-reg-preview/)

1. Välj **Appregistreringar (för hands version)** och välj sedan det webb program som ska ha åtkomst till API: et. Till exempel *webapp1*.
1. Under **Hantera**, Välj **API-behörigheter**.
1. Under **konfigurerade behörigheter**väljer du **Lägg till en behörighet**.
1. Välj fliken **Mina API: er** .
1. Välj det API som webb programmet ska beviljas åtkomst till. Till exempel *webapi1*.
1. Under **behörighet**expanderar du **demo**och väljer sedan de omfattningar som du definierade tidigare. Till exempel *demo. Read* och *demo. Write*.
1. Välj **Lägg till behörigheter**. Vänta några minuter innan du fortsätter till nästa steg.
1. Välj **bevilja administrativt godkännande för (ditt klient namn)** .
1. Välj ditt inloggade administratörs konto eller logga in med ett konto i Azure AD B2C-klienten som har tilldelats minst administratörs rollen för *moln program* .
1. Välj **Acceptera**.
1. Välj **Uppdatera**och verifiera sedan att "beviljat..." visas under **status** för båda omfattningarna. Det kan ta några minuter innan behörigheterna har spridits.
---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: ee63c1c0913baf3700d77e6fba1582fe7027ead7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80875709"
---
#### <a name="applications"></a>[Program](#tab/applications/)

1. Välj **program**och välj sedan det webb program som ska ha åtkomst till API: et. Till exempel *webapp1*.
1. Välj **API-åtkomst** och därefter **Lägg till**.
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
1. Välj **Lägg till behörigheter**.
1. Välj **bevilja administrativt godkännande för (ditt klient namn)**.
1. Om du uppmanas att välja ett konto väljer du ditt inloggade administratörs konto eller loggar in med ett konto i Azure AD B2C-klienten som har tilldelats minst administratörs rollen för *moln program* .
1. Välj **Ja**.
1. Välj **Uppdatera**och verifiera sedan att "beviljat..." visas under **status** för båda omfattningarna.

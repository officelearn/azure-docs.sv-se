---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: ce49e9e26160017d64d745c9c7ad5402bc3ae2ca
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641564"
---
#### <a name="applicationstabapplications"></a>[Program](#tab/applications/)

1. Välj **program**och välj sedan det webb program som ska ha åtkomst till API: et. Till exempel *webapp1*.
1. Välj **API-åtkomst** och därefter **Lägg till**.
1. I list rutan **Välj API** väljer du det API som webb programmet ska beviljas åtkomst till. Till exempel *webapi1*.
1. I list rutan **Välj omfång** väljer du de omfattningar som du definierade tidigare. Till exempel *demo. Read* och *demo. Write*.
1. Välj **OK**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Appregistreringar (för hands version)](#tab/app-reg-preview/)

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
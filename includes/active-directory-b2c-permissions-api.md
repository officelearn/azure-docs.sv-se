---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: e37419b5d568b0f60e4c6a0c8fed26eb418f501e
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84297502"
---
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

#### <a name="applications"></a>[Program](#tab/applications/)

1. Välj **program**och välj sedan det webb program som ska ha åtkomst till API: et. Till exempel *webapp1*.
1. Välj **API-åtkomst** och därefter **Lägg till**.
1. I list rutan **Välj API** väljer du det API som webb programmet ska beviljas åtkomst till. Till exempel *webapi1*.
1. I list rutan **Välj omfång** väljer du de omfattningar som du definierade tidigare. Till exempel *demo. Read* och *demo. Write*.
1. Välj **OK**.

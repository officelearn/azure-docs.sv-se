---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 71a6654acd436c27bd2370646dede81778113860
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186157"
---
#### <a name="applications"></a>[Program](#tab/applications/)

1. Välj **Program**och välj sedan det webbprogram som ska ha åtkomst till API:et. Till exempel *webapp1*.
1. Välj **API-åtkomst** och därefter **Lägg till**.
1. I listrutan **Välj API** väljer du det API som webbprogrammet ska beviljas åtkomst till. Till exempel *webapi1*.
1. I listrutan **Välj scope** väljer du de scope som du definierade tidigare. Till exempel *demo.read* och *demo.write*.
1. Välj **OK**.

#### <a name="app-registrations-preview"></a>[Appregistreringar (förhandsversion)](#tab/app-reg-preview/)

1. Välj **Appregistreringar (förhandsversion)** och välj sedan det webbprogram som ska ha åtkomst till API:et. Till exempel *webapp1*.
1. Under **Hantera**väljer du **API-behörigheter**.
1. Under **Konfigurerade behörigheter**väljer du **Lägg till en behörighet**.
1. Välj fliken **Mina API:er.**
1. Välj det API som webbprogrammet ska beviljas åtkomst till. Till exempel *webapi1*.
1. Expandera **demo**under **Behörighet**och välj sedan de scope som du definierade tidigare. Till exempel *demo.read* och *demo.write*.
1. Välj **Lägg till behörigheter**. Enligt anvisningarna väntar du några minuter innan du fortsätter till nästa steg.
1. Välj **Bevilja administratörsmedgivande för (ditt klientnamn)**.
1. Välj ditt inloggade administratörskonto eller logga in med ett konto i din Azure AD B2C-klient som har tilldelats åtminstone *rollen cloud application administrator.*
1. Välj **Acceptera**.
1. Välj **Uppdatera**och kontrollera sedan att "Beviljad för ..." visas under **Status** för båda scopea. Det kan ta några minuter innan behörigheterna sprids.
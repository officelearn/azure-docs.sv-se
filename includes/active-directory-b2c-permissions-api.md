---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: ee63c1c0913baf3700d77e6fba1582fe7027ead7
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875709"
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
1. Välj **Lägg till behörigheter**.
1. Välj **Bevilja administratörsmedgivande för (ditt klientnamn)**.
1. Om du uppmanas att välja ett konto väljer du ditt inloggade administratörskonto eller loggar in med ett konto i din Azure AD B2C-klient som har tilldelats åtminstone *rollen cloud-programadministratör.*
1. Välj **Ja**.
1. Välj **Uppdatera**och kontrollera sedan att "Beviljad för ..." visas under **Status** för båda scopea.

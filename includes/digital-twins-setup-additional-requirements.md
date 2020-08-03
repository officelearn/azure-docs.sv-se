---
author: baanders
description: ta med fil för möjliga ytterligare krav i Azure Digitals dubbla installations program
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: 230304677b78f00b2d1288c846f8bf704cd8a497
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407574"
---
Det är möjligt att organisationen kräver ytterligare åtgärder från prenumerations ägare för att kunna konfigurera en registrering av appen (och därmed slutföra konfigurationen av en användbar Azure Digital-instans). De steg som krävs kan variera beroende på organisationens aktuella inställningar.

Här följer några vanliga potentiella aktiviteter som en ägare kan behöva utföra. Dessa och andra åtgärder kan utföras från sidan [*Azure AD App registreringar*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) i Azure Portal.
* Bevilja administrativt medgivande för appens registrering. Din organisation kan ha ett *administrativt medgivande som måste* aktive ras globalt i Azure AD för alla app-registreringar i din prenumeration. I så fall måste ägaren välja den här knappen för ditt företag på registrerings sidan för appens *API-behörigheter* för att appen ska vara giltig:

    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/grant-admin-consent.png" alt-text="Portal visning av knappen bevilja administrativt medgivande under API-behörigheter":::
  - Om medgivande har beviljats, bör posten för Azure Digital-dubbla, Visa ett *status* värde för _beviljat **(ditt företag)** _
   
    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/granted-admin-consent-done.png" alt-text="Portal visning av administrativt medgivande som beviljats för företaget under API-behörigheter":::
* Aktivera offentlig klient åtkomst
* Ange vissa svars-URL: er för webb-och skriv bords åtkomst
* Tillåt för implicita OAuth2-autentiserings flöden

Mer information om registrering av appar och dess olika installations alternativ finns i [*Registrera ett program med Microsoft Identity Platform*](https://docs.microsoft.com/graph/auth-register-app-v2).

Nu har du en Azure Digital-instansen som är redo att sätta igång, har tilldelat behörigheter för att hantera den och har angett behörighet för en klient app för att få åtkomst till den.
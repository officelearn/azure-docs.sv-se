---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: bf8b9370abc58902d9c751505f633dee852210a6
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184372"
---
#### <a name="applications"></a>[Program](#tab/applications/)

1. På sidan **registrerad app** -översikt väljer du **Inställningar**.
1. Under **API-åtkomst**väljer du **nödvändiga behörigheter**.
1. Välj **Microsoft Graph**.
1. Under **program behörigheter**markerar du kryss rutan för behörigheten att bevilja ditt hanterings program. Några exempel:
    * **Läs alla Gransknings logg data**: Välj den här behörigheten för att läsa katalogens gransknings loggar.
    * **Läsa och skriva katalog data**: Välj den här behörigheten för användar migrering eller användar hanterings scenarier.
    * **Läs och skriv organisationens förtroende Ramverks principer**: Välj den här behörigheten för scenarier för kontinuerlig integrering/kontinuerlig leverans (CI/CD). Till exempel anpassad princip distribution med Azure-pipelines.
1. Välj **Spara**.
1. Välj **bevilja behörigheter**och välj sedan **Ja**. Det kan ta några minuter innan behörigheterna är fullständigt spridda.

#### <a name="app-registrations-preview"></a>[Appregistreringar (för hands version)](#tab/app-reg-preview/)

1. Under **Hantera**, Välj **API-behörigheter**.
1. Under **konfigurerade behörigheter**väljer du **Lägg till en behörighet**.
1. Välj fliken **Microsoft API: er** och välj sedan **Microsoft Graph**.
1. Välj **Programbehörigheter**.
1. Expandera rätt behörighets grupp och markera kryss rutan för behörighet att bevilja ditt hanterings program. Några exempel:
    * **AuditLog** > **AuditLog. Read. all**: för att läsa katalogens gransknings loggar.
    * **Katalog** > **Directory. readwrite. all**: för användar migrering eller användar hanterings scenarier.
    * **Princip** > **policy. readwrite. TrustFramework**: för scenarier med kontinuerlig integrering/kontinuerlig leverans (CI/CD). Till exempel anpassad princip distribution med Azure-pipelines.
1. Välj **Lägg till behörigheter**. Vänta några minuter innan du fortsätter till nästa steg.
1. Välj **bevilja administrativt godkännande för (ditt klient namn)** .
1. Välj ditt inloggade administratörs konto eller logga in med ett konto i Azure AD B2C-klienten som har tilldelats minst administratörs rollen för *moln program* .
1. Välj **Acceptera**.
1. Välj **Uppdatera**och verifiera sedan att "beviljat..." visas under **status**. Det kan ta några minuter innan behörigheterna har spridits.

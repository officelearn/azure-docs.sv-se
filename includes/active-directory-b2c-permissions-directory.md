---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: bf8b9370abc58902d9c751505f633dee852210a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184372"
---
#### <a name="applications"></a>[Program](#tab/applications/)

1. På sidan **Listad app** översikt väljer du **Inställningar**.
1. Under **API Access**väljer du Krävs **behörigheter**.
1. Välj **Microsoft Graph**.
1. Markera kryssrutan för behörigheten att bevilja ditt hanteringsprogram under **Programbehörigheter.** Ett exempel:
    * **Läs alla granskningsloggdata**: Välj den här behörigheten för att läsa katalogens granskningsloggar.
    * **Läsa och skriva katalogdata**: Välj den här behörigheten för användarmigrering eller användarhanteringsscenarier.
    * **Läs och skriv organisationens förtroenderamsprinciper**: Välj den här behörigheten för ci/cd-scenarier (continuous integration/continuous delivery). Till exempel anpassad principdistribution med Azure Pipelines.
1. Välj **Spara**.
1. Välj **Bevilja behörigheter**och välj sedan **Ja**. Det kan ta några minuter innan behörigheterna sprids helt.

#### <a name="app-registrations-preview"></a>[Appregistreringar (förhandsversion)](#tab/app-reg-preview/)

1. Under **Hantera**väljer du **API-behörigheter**.
1. Under **Konfigurerade behörigheter**väljer du **Lägg till en behörighet**.
1. Välj fliken **Microsoft API:er** och välj sedan **Microsoft Graph**.
1. Välj **Programbehörigheter**.
1. Expandera lämplig behörighetsgrupp och markera kryssrutan för behörigheten att bevilja till ditt hanteringsprogram. Ett exempel:
    * **AuditLog** > **AuditLog.Read.All**: För att läsa katalogens granskningsloggar.
    * **Directory** > **Directory.ReadWrite.All**: För scenarier för användarmigrering eller användarhantering.
    * **Policy** > **Policy.ReadWrite.TrustFramework**: För kontinuerlig integrering/kontinuerlig leverans (CI/CD) scenarier. Till exempel anpassad principdistribution med Azure Pipelines.
1. Välj **Lägg till behörigheter**. Enligt anvisningarna väntar du några minuter innan du fortsätter till nästa steg.
1. Välj **Bevilja administratörsmedgivande för (ditt klientnamn)**.
1. Välj ditt inloggade administratörskonto eller logga in med ett konto i din Azure AD B2C-klient som har tilldelats åtminstone *rollen cloud application administrator.*
1. Välj **Acceptera**.
1. Välj **Uppdatera**och kontrollera sedan att "Beviljad för ..." visas under **Status**. Det kan ta några minuter innan behörigheterna sprids.

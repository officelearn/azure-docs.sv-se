---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: mimart
ms.openlocfilehash: f4944881e6eff3e9a56a74bc997c280f9fff6be8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187019"
---
## <a name="ropc-flow-notes"></a>ROPC-flödesanteckningar
I Azure Active Directory B2C (Azure AD B2C) stöds följande alternativ:

- **Inbyggd klient:** Användarinteraktion under autentisering sker när koden körs på en enhet på användarsidan. Enheten kan vara ett mobilprogram som körs i ett inbyggt operativsystem, till exempel Android och iOS.
- **Offentligt klientflöde**: Endast användaruppgifter, som samlas in av ett program, skickas i API-anropet. Autentiseringsuppgifterna för programmet skickas inte.
- **Lägg till nya anspråk**: ID-tokeninnehållet kan ändras för att lägga till nya anspråk.

Följande flöden stöds inte:

- **Server-till-server**: Identitetsskyddssystemet behöver en tillförlitlig IP-adress som samlats in från anroparen (den inbyggda klienten) som en del av interaktionen. I ett API-anrop på serversidan används endast serverns IP-adress. Om ett dynamiskt tröskelvärde för misslyckade autentiseringar överskrids kan identitetsskyddssystemet identifiera en upprepad IP-adress som angripare.
- **Konfidentiellt klientflöde**: Programklient-ID:t har validerats, men programhemligheten har inte validerats.

När du använder ROPC-flödet bör du tänka på följande:

- ROPC fungerar inte när det finns något avbrott i autentiseringsflödet som kräver användarinteraktion. Till exempel när ett lösenord har upphört att gälla eller behöver ändras krävs multifaktorautentisering eller när mer information måste samlas in under inloggningen (till exempel användarens medgivande).
- ROPC stöder endast lokala konton. Användare kan inte logga in med federerade identitetsleverantörer som Microsoft, Google+, Twitter, AD-FS eller Facebook.
- Sessionshantering, inklusive håll mig inloggad (KMSI), är inte tillämplig.

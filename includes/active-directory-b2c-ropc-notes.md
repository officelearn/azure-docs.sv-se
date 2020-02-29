---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: marsma
ms.openlocfilehash: fc70fb163ae1f6198f66743a739a0d9720f764f1
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912416"
---
## <a name="ropc-flow-notes"></a>ROPC Flow-anteckningar
I Azure Active Directory B2C (Azure AD B2C) stöds följande alternativ:

- **Inbyggd klient**: användar interaktion under autentisering sker när kod körs på en enhet på användar sidan. Enheten kan vara ett mobilt program som körs i ett inbyggt operativ system, till exempel Android och iOS.
- **Offentligt klient flöde**: endast användarautentiseringsuppgifter som samlats in av ett program skickas i API-anropet. Autentiseringsuppgifterna för programmet skickas inte.
- **Lägg till nya anspråk**: innehållet i ID-token kan ändras för att lägga till nya anspråk.

Följande flöden stöds inte:

- **Server-till-Server**: identitets skydds systemet behöver en tillförlitlig IP-adress som samlats in från anroparen (den interna klienten) som en del av interaktionen. I ett API-anrop på Server sidan används bara serverns IP-adress. Om ett dynamiskt tröskelvärde för misslyckade autentiseringar överskrids, kan identitets skydds systemet identifiera en upprepad IP-adress som en angripare.
- **Konfidentiellt klient flöde**: programmets klient-ID är verifierat, men program hemligheten är inte verifierad.

Tänk på följande när du använder ROPC-flödet:

- ROPC fungerar inte om det sker ett avbrott i det autentiseringsschema som kräver användar interaktion. Till exempel, när ett lösen ord har upphört att gälla eller behöver ändras, krävs Multi-Factor Authentication eller när mer information måste samlas in under inloggningen (till exempel användar medgivande).
- ROPC stöder endast lokala konton. Användare kan inte logga in med federerade identitets leverantörer som Microsoft, Google +, Twitter, AD-FS eller Facebook.
- Hantering av sessioner, inklusive Håll mig inloggad (KMSI avgör), är inte tillämpligt.

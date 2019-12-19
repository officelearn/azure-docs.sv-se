---
title: Välj en sidlayout – Azure Active Directory B2C
description: Lär dig mer om hur du väljer en sidlayout i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 108d86e35422e1dc1d10aeb6b2c9488f5067232e
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389675"
---
# <a name="select-a-page-layout-in-azure-active-directory-b2c-using-custom-policies"></a>Välj en sidlayout i Azure Active Directory B2C att använda anpassade principer

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Du kan aktivera JavaScript-kod på klient sidan i din Azure Active Directory B2C (Azure AD B2C) principer oavsett om du använder användar flöden eller anpassade principer. Om du vill aktivera Java Script för dina program måste du lägga till ett element i din [anpassade princip](active-directory-b2c-overview-custom.md), välja en sidlayout och använda [b2clogin.com](b2clogin.md) i dina begär Anden.

En sidlayout är en associering av element som Azure AD B2C tillhandahåller och det innehåll som du anger.

Den här artikeln beskriver hur du väljer en sidlayout i Azure AD B2C genom att konfigurera den i en anpassad princip.

> [!NOTE]
> Om du vill aktivera Java Script för användar flöden, se [Java Script och versioner av sidlayout i Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="replace-datauri-values"></a>Ersätt DataUri-värden

I dina anpassade principer kan du ha [ContentDefinitions](contentdefinitions.md) som definierar de HTML-mallar som används i användar resan. **ContentDefinition** innehåller en **DataUri** som refererar till de sid element som tillhandahålls av Azure AD B2C. **LoadUri** är den relativa sökvägen till HTML-och CSS-innehåll som du anger.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

Om du vill välja en sidlayout ändrar du **DataUri** -värdena i dina [ContentDefinitions](contentdefinitions.md) i dina principer. Genom att växla från de gamla **DataUri** -värdena till de nya värdena väljer du ett oföränderligt paket. Fördelen med att använda det här paketet är att du vet att det inte ändras och orsakar oväntad beteende på din sida.

Om du vill konfigurera en sidlayout använder du följande tabell för att hitta **DataUri** -värden.

| Gammalt DataUri-värde | Nytt DataUri-värde |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0` |

## <a name="version-change-log"></a>Versions ändrings logg

Sid inlayout-paket uppdateras regelbundet för att inkludera korrigeringar och förbättringar i sina sid element. Följande ändrings logg anger de ändringar som införs i varje version.

### <a name="120"></a>1.2.0 
- Alla sidor
  - Hjälpmedels korrigeringar
  - Nu kan du lägga till attributet `data-preload="true"` i HTML-taggarna för att kontrol lera inläsnings ordningen för CSS och Java Script. Scenarierna är:
      - Använd det här på CSS-länken för att läsa in CSS samtidigt som HTML så att det inte flimrar mellan att läsa in filerna
      - Med det här attributet kan du styra i vilken ordning dina skript taggar ska hämtas och köras innan sid inläsningen
  - E-postfältet är nu `type=email` och mobila tangent bord kommer att ange rätt förslag
  - Stöd för Chrome-Översättning
- Enhetlig och egen-kontrollerad sida
  - Fälten användar namn/e-post och lösen ord använder nu HTML-elementet form.  Nu kan du använda Microsoft Edge och IE för att spara den här informationen korrekt
  
### <a name="110"></a>1.1.0

- Undantags sida (globalexception)
  - Tillgänglighets korrigering
  - Tog bort standard meddelandet när det inte finns någon kontakt från principen
  - Standard-CSS borttagen
- MFA-sida (multifaktor)
  - Knappen Bekräfta kod har tagits bort
  - Inmatade fält för koden tar nu bara upp till sex tecken (6)
  - Sidan försöker automatiskt verifiera den kod som angavs när en 6-siffrig kod anges, utan att någon knapp behöver klicka
  - Om koden är fel rensas inmatad fält automatiskt
  - Efter tre (3) försök med en felaktig kod skickar B2C ett fel tillbaka till den förlitande parten
  - Hjälpmedels korrigeringar
  - Standard-CSS borttagen
- Själv kontrollerad sida (selfasserted)
  - Avbrotts aviseringen har tagits bort
  - CSS-klass för fel element
  - Visa/Dölj fel logiken har förbättrats
  - Standard-CSS borttagen
- Unified SSP (unifiedssp)
  - Lade till Behåll mig inloggad (KMSI avgör) kontroll

### <a name="100"></a>1.0.0

- Första utgåvan

## <a name="next-steps"></a>Nästa steg

Hitta mer information om hur du kan anpassa användar gränssnittet för dina program i [Anpassa användar gränssnittet för ditt program med hjälp av en anpassad princip i Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).

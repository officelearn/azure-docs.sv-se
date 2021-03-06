---
title: Sidlayoutversioner
titleSuffix: Azure AD B2C
description: Versions historik för sidlayouten för UI-anpassning i anpassade principer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 868d99a82009dc8545fc24ad1cfa1da3959da131
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88852077"
---
# <a name="page-layout-versions"></a>Sidlayoutversioner

Sid inlayout-paket uppdateras regelbundet för att inkludera korrigeringar och förbättringar i sina sid element. Följande ändrings logg anger de ändringar som införs i varje version.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="self-asserted-page-selfasserted"></a>Själv kontrollerad sida (selfasserted)

**2.1.0**

- Lokaliserings-och tillgänglighets korrigeringar.

**2.0.0**

- Stöd har lagts till för [visnings kontroller](display-controls.md) i anpassade principer.

**1.2.0**

- Fälten användar namn/e-post och lösen ord använder nu `form` HTML-elementet för att tillåta att Edge och Internet Explorer (IE) sparar informationen på rätt sätt.
- Har lagt till en konfigurerbar verifierings fördröjning för användarindata för förbättrad användar upplevelse.
- Hjälpmedels korrigeringar
- Nu kan du lägga till `data-preload="true"` attributet [i dina HTML-taggar](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) för att kontrol lera inläsnings ordningen för CSS och Java Script.
  - Läs in länkade CSS-filer samtidigt som din HTML-mall så att den inte flimrar mellan att läsa in filerna.
  - Styra i vilken ordning `script` taggarna ska hämtas och köras före sid inläsningen.
- E-postfältet är nu `type=email` och mobila tangent bord ger rätt förslag
- Stöd för Chrome-Översättning

**1.1.0**

- Avbrotts aviseringen har tagits bort
- CSS-klass för fel element
- Visa/Dölj fel logiken har förbättrats
- Standard-CSS borttagen

**1.0.0**

- Första utgåvan

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>Sidan enhetlig inloggning med lösen ords återställning (unifiedssp)

**2.1.0**

- Stöd har lagts till för flera registrerings länkar.
- Stöd har lagts till för verifiering av användarindata enligt de predikat som definierats i principen.

**1.2.0**

- Fälten användar namn/e-post och lösen ord använder nu `form` HTML-elementet för att tillåta att Edge och Internet Explorer (IE) sparar informationen på rätt sätt.
- Hjälpmedels korrigeringar
- Nu kan du lägga till `data-preload="true"` attributet [i dina HTML-taggar](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) för att kontrol lera inläsnings ordningen för CSS och Java Script.
  - Läs in länkade CSS-filer samtidigt som din HTML-mall så att den inte flimrar mellan att läsa in filerna.
  - Styra i vilken ordning `script` taggarna ska hämtas och köras före sid inläsningen.
- E-postfältet är nu `type=email` och mobila tangent bord ger rätt förslag
- Stöd för Chrome-Översättning

**1.1.0**

- Lade till Behåll mig inloggad (KMSI avgör) kontroll

**1.0.0**

- Första utgåvan

## <a name="mfa-page-multifactor"></a>MFA-sida (multifaktor)

**1.2.1**

- Tillgänglighets korrigeringar för standardmallar

**1.2.0**

- Hjälpmedels korrigeringar
- Nu kan du lägga till `data-preload="true"` attributet [i dina HTML-taggar](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) för att kontrol lera inläsnings ordningen för CSS och Java Script.
  - Läs in länkade CSS-filer samtidigt som din HTML-mall så att den inte flimrar mellan att läsa in filerna.
  - Styra i vilken ordning `script` taggarna ska hämtas och köras före sid inläsningen.
- E-postfältet är nu `type=email` och mobila tangent bord ger rätt förslag
- Stöd för Chrome-Översättning

**1.1.0**

- Knappen Bekräfta kod har tagits bort
- Inmatade fält för koden tar nu bara upp till sex tecken (6)
- Sidan försöker automatiskt verifiera den kod som angavs när en 6-siffrig kod anges, utan att någon knapp behöver klicka
- Om koden är fel rensas inmatat fält automatiskt
- Efter tre (3) försök med en felaktig kod skickar B2C ett fel tillbaka till den förlitande parten
- Hjälpmedels korrigeringar
- Standard-CSS borttagen

**1.0.0**

- Första utgåvan

## <a name="exception-page-globalexception"></a>Undantags sida (globalexception)

**1.2.0**

- Hjälpmedels korrigeringar
- Nu kan du lägga till `data-preload="true"` attributet [i dina HTML-taggar](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) för att kontrol lera inläsnings ordningen för CSS och Java Script.
  - Läs in länkade CSS-filer samtidigt som din HTML-mall så att den inte flimrar mellan att läsa in filerna.
  - Styra i vilken ordning `script` taggarna ska hämtas och köras före sid inläsningen.
- E-postfältet är nu `type=email` och mobila tangent bord ger rätt förslag
- Stöd för Chrome-Översättning

**1.1.0**

- Tillgänglighets korrigering
- Tog bort standard meddelandet när det inte finns någon kontakt från principen
- Standard-CSS borttagen

**1.0.0**

- Första utgåvan

## <a name="other-pages-providerselection-claimsconsent-unifiedssd"></a>Andra sidor (ProviderSelection, ClaimsConsent, UnifiedSSD)

**1.2.0**

- Hjälpmedels korrigeringar
- Nu kan du lägga till `data-preload="true"` attributet [i dina HTML-taggar](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) för att kontrol lera inläsnings ordningen för CSS och Java Script.
  - Läs in länkade CSS-filer samtidigt som din HTML-mall så att den inte flimrar mellan att läsa in filerna.
  - Styra i vilken ordning `script` taggarna ska hämtas och köras före sid inläsningen.
- E-postfältet är nu `type=email` och mobila tangent bord ger rätt förslag
- Stöd för Chrome-Översättning

**1.0.0**

- Första utgåvan

## <a name="next-steps"></a>Nästa steg

Mer information om hur du anpassar användar gränssnittet för dina program i anpassade principer finns i [Anpassa användar gränssnittet för ditt program med hjälp av en anpassad princip](custom-policy-ui-customization.md).

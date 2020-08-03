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
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4548b50e4168f260cb401c40dd4e61192cea1015
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87489545"
---
# <a name="page-layout-versions"></a>Sidlayoutversioner

Sid inlayout-paket uppdateras regelbundet för att inkludera korrigeringar och förbättringar i sina sid element. Följande ändrings logg anger de ändringar som införs i varje version.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="210"></a>2.1.0

- Själv kontrollerad sida ( `selfasserted` )
  - Lokaliserings-och tillgänglighets korrigeringar.
- Enhetlig SSP-sida ( `unifiedssp` )
  - Stöd har lagts till för flera registrerings länkar.
  - Stöd har lagts till för verifiering av användarindata enligt de predikat som definierats i principen.

## <a name="200"></a>2.0.0

- Själv kontrollerad sida ( `selfasserted` )
  - Stöd har lagts till för [visnings kontroller](display-controls.md) i anpassade principer.

## <a name="120"></a>1.2.0

- Alla sidor
  - Hjälpmedels korrigeringar
  - Nu kan du lägga till `data-preload="true"` attributet [i dina HTML-taggar](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) för att kontrol lera inläsnings ordningen för CSS och Java Script.
    - Läs in länkade CSS-filer samtidigt som din HTML-mall så att den inte flimrar mellan att läsa in filerna.
    - Styra i vilken ordning `script` taggarna ska hämtas och köras före sid inläsningen.
  - E-postfältet är nu `type=email` och mobila tangent bord ger rätt förslag
  - Stöd för Chrome-Översättning
- Enhetliga och självkontrollerade sidor
  - Fälten användar namn/e-post och lösen ord använder nu `form` HTML-elementet för att tillåta att Edge och Internet Explorer (IE) sparar informationen på rätt sätt.
- Själv kontrollerad sida
  - Har lagt till en konfigurerbar verifierings fördröjning för användarindata för förbättrad användar upplevelse.

## <a name="110"></a>1.1.0

- Undantags sida (globalexception)
  - Tillgänglighets korrigering
  - Tog bort standard meddelandet när det inte finns någon kontakt från principen
  - Standard-CSS borttagen
- MFA-sida (multifaktor)
  - Knappen Bekräfta kod har tagits bort
  - Inmatade fält för koden tar nu bara upp till sex tecken (6)
  - Sidan försöker automatiskt verifiera den kod som angavs när en 6-siffrig kod anges, utan att någon knapp behöver klicka
  - Om koden är fel rensas inmatat fält automatiskt
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

## <a name="100"></a>1.0.0

- Första utgåvan

## <a name="next-steps"></a>Nästa steg

Mer information om hur du anpassar användar gränssnittet för dina program i anpassade principer finns i [Anpassa användar gränssnittet för ditt program med hjälp av en anpassad princip](custom-policy-ui-customization.md).

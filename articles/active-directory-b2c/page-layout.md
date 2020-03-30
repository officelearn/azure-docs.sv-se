---
title: Sidlayoutversioner
titleSuffix: Azure AD B2C
description: Versionshistorik för sidlayout för anpassning av användargränssnittet i anpassade principer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3d0cb06f84fdd96d099e05f55ba62c37cb1192c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183983"
---
# <a name="page-layout-versions"></a>Sidlayoutversioner

Sidlayoutpaket uppdateras regelbundet så att de innehåller korrigeringar och förbättringar i sidelementen. Följande ändringslogg anger de ändringar som introduceras i varje version.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- Självförsäkrad sida`selfasserted`( )
  - Lade till stöd för [visningskontroller](display-controls.md) i anpassade principer.

## <a name="120"></a>1.2.0

- Alla sidor
  - Korrigeringar av hjälpmedel
  - Du kan nu `data-preload="true"` lägga till attributet [i HTML-taggarna](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) för att styra inläsningsordningen för CSS och JavaScript.
    - Ladda länkade CSS-filer samtidigt som html-mallen så att den inte "flimrar" mellan att läsa in filerna.
    - Styr i vilken `script` ordning taggarna hämtas och körs innan sidan läses in.
  - E-postfältet `type=email` är nu och mobila tangentbord kommer att ge rätt förslag
  - Stöd för Chrome-översätter
- Enhetliga och självsäkra sidor
  - Fälten användarnamn/e-post och `form` lösenord använder nu HTML-elementet för att låta Edge och Internet Explorer (IE) spara den här informationen på rätt sätt.

## <a name="110"></a>1.1.0

- Undantagssida (globalexception)
  - Åtgärda hjälpmedel
  - Tog bort standardmeddelandet när det inte finns någon kontakt från principen
  - Standard-CSS har tagits bort
- MFA-sida (multifaktor)
  - Knappen "Bekräfta kod" har tagits bort
  - Indatafältet för koden tar nu bara indata upp till sex (6) tecken
  - Sidan försöker automatiskt verifiera koden som anges när en 6-siffrig kod anges, utan att någon knapp behöver klickas på
  - Om koden är fel rensas indatafältet automatiskt
  - Efter tre (3) försök med en felaktig kod skickar B2C ett fel tillbaka till den förlitande parten
  - Korrigeringar av hjälpmedel
  - Standard-CSS har tagits bort
- Självförsäkrad sida (självsäkrad)
  - Återkallad avisering av avbryt
  - CSS-klass för felelement
  - Visa/dölja fellogiken förbättrad
  - Standard-CSS har tagits bort
- Enhetlig SSP (enadssp)
  - Lade till håll mig inloggad (KMSI) kontroll

## <a name="100"></a>1.0.0

- Första utgåvan

## <a name="next-steps"></a>Nästa steg

Mer information om hur du anpassar användargränssnittet för dina program i anpassade principer finns i [Anpassa användargränssnittet för ditt program med hjälp av en anpassad princip](custom-policy-ui-customization.md).

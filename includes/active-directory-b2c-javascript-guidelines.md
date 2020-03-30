---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: mimart
ms.openlocfilehash: ef08f1adc15475cd5dd38548ed39dc57532c0ef5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185851"
---
## <a name="guidelines-for-using-javascript"></a>Riktlinjer för användning av JavaScript

Följ dessa riktlinjer när du anpassar gränssnittet för ditt program med JavaScript:

- Bind inte en klickhändelse `<a>` på HTML-element.
- Ta inte ett beroende av Azure AD B2C-kod eller kommentarer.
- Ändra inte ordningen eller hierarkin för Azure AD B2C HTML-element. Använd en Azure AD B2C-princip för att styra ordningen på gränssnittselementen.
- Du kan ringa alla RESTful-tjänster med dessa överväganden:
    - Du kan behöva ställa in din RESTful-tjänst CORS så att HTTP-anrop på klientsidan tillåts.
    - Kontrollera att din RESTful-tjänst är säker och använder endast HTTPS-protokollet.
    - Använd inte JavaScript direkt för att anropa Azure AD B2C-slutpunkter.
- Du kan bädda in ditt JavaScript eller länka till externa JavaScript-filer. När du använder en extern JavaScript-fil ska du se till att använda den absoluta WEBBADRESSEN och inte en relativ URL.
- JavaScript-ramverk:
    - Azure AD B2C använder en specifik version av jQuery. Ta inte med en annan version av jQuery. Om du använder mer än en version på samma sida kan det orsaka problem.
    - Att använda RequireJS stöds inte.
    - De flesta JavaScript-ramverk stöds inte av Azure AD B2C.
- Azure AD B2C-inställningar kan `window.SETTINGS` `window.CONTENT` läsas genom att anropa , objekt, till exempel det aktuella användargränssnittsspråket. Ändra inte värdet på dessa objekt.
- Om du vill anpassa felmeddelandet Azure AD B2C använder du lokalisering i en princip.
- Om något kan uppnås med hjälp av en princip, i allmänhet är det det rekommenderade sättet.

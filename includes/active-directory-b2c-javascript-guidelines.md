---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: mimart
ms.openlocfilehash: ef08f1adc15475cd5dd38548ed39dc57532c0ef5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "78185851"
---
## <a name="guidelines-for-using-javascript"></a>Rikt linjer för att använda Java Script

Följ dessa rikt linjer när du anpassar gränssnittet för ditt program med hjälp av Java Script:

- Bind inte en klick händelse på `<a>` HTML-element.
- Ta inte ett beroende på Azure AD B2C kod eller kommentarer.
- Ändra inte ordningen eller hierarkin för Azure AD B2C HTML-element. Använd en Azure AD B2C-princip för att kontrol lera ordningen på GRÄNSSNITTs elementen.
- Du kan anropa en RESTful-tjänst med följande överväganden:
    - Du kan behöva ange din RESTful-tjänst-CORS för att tillåta HTTP-anrop på klient sidan.
    - Kontrol lera att din RESTful-tjänst är säker och använder endast HTTPS-protokollet.
    - Använd inte Java Script direkt för att anropa Azure AD B2C slut punkter.
- Du kan bädda in Java Script eller länka till externa JavaScript-filer. När du använder en extern JavaScript-fil ska du se till att använda den absoluta URL: en och inte en relativ URL.
- JavaScript-ramverk:
    - Azure AD B2C använder en angiven version av jQuery. Ta inte med en annan version av jQuery. Om du använder mer än en version på samma sida uppstår problem.
    - Användning av RequireJS stöds inte.
    - De flesta JavaScript-ramverk stöds inte av Azure AD B2C.
- Azure AD B2C inställningar kan läsas genom att anropa `window.SETTINGS` , `window.CONTENT` objekt, till exempel det aktuella gränssnitts språket. Ändra inte värdet för dessa objekt.
- Om du vill anpassa Azure AD B2C fel meddelandet använder du lokalisering i en princip.
- Om något kan uppnås med hjälp av en princip är det vanligt vis det rekommenderade sättet.

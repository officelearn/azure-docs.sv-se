---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: mimart
ms.openlocfilehash: ef08f1adc15475cd5dd38548ed39dc57532c0ef5
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78185851"
---
## <a name="guidelines-for-using-javascript"></a>Riktlinjer för att använda JavaScript

Följ dessa riktlinjer när du anpassar gränssnittet för ditt program med hjälp av JavaScript:

- Bind inte en klicknings händelse på `<a>` HTML-element.
- Om du bara får du ett beroende på Azure AD B2C-kod eller kommentarer.
- Inte ändra ordningen eller hierarki med Azure AD B2C HTML-element. Använda en Azure AD B2C-princip för att styra ordningen för UI-element.
- Du kan anropa valfri RESTful-tjänst med detta:
    - Du kan behöva ange ditt RESTful-tjänst CORS så att klientsidan HTTP-anrop.
    - Kontrollera att dina RESTful-tjänst är säker och använder endast HTTPS-protokollet.
    - Använd inte JavaScript direkt för att anropa Azure AD B2C-slutpunkter.
- Du kan bädda in din JavaScript eller du kan länka till externa JavaScript-filer. När du använder en extern JavaScript-fil, se till att använda absolut URL och inte en relativ URL.
- JavaScript-ramverk:
    - Azure AD B2C använder en specifik version av jQuery. Omfattar inte en annan version av jQuery. Med hjälp av mer än en version på samma sida orsakar problem.
    - Med hjälp av RequireJS stöds inte.
    - De flesta JavaScript-ramverk stöds inte av Azure AD B2C.
- Azure AD B2C inställningar kan läsas genom att anropa `window.SETTINGS`, `window.CONTENT` objekt, till exempel det aktuella GRÄNSSNITTs språket. Ändra inte värdet för dessa objekt.
- Använda lokalisering i en princip för att anpassa felmeddelandet för Azure AD B2C.
- Om något kan uppnås med hjälp av en Grupprincip, är det vanligtvis det rekommenderade sättet.

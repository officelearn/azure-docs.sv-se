---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: marsma
ms.openlocfilehash: 687853720e8f963d5a58093b824bb36e8063bcf7
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149088"
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

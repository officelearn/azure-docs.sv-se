---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 9c972508c98e87771154bd26cc166c6bea4201ee
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279381"
---
## <a name="test"></a>Verifiera funktionen i Azure

Du kan använda en webbläsare för att verifiera den distribuerade funktionen.  Kopiera webb adressen, inklusive funktions nyckeln, till adress fältet i webbläsaren. Lägg till frågesträngen `&name=<yourname>` till URL: en innan du kör begäran.

![Använda en webbläsare för att anropa-funktionen.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  

Du kan också använda sväng för att verifiera den distribuerade funktionen. Använd URL: en, inklusive funktions nyckeln, som du kopierade från föregående steg, Lägg till frågesträngen `&name=<yourname>` till URL: en.

![Använd sväng för att anropa funktionen i Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 


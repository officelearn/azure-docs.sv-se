---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 44eb25be12b908c8f951cb20948068da3bfc2928
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203126"
---
## <a name="test"></a>Verifiera funktionen i Azure

Använd sväng för att verifiera den distribuerade funktionen. Använd URL: en, inklusive funktions nyckeln, som du kopierade från föregående steg, Lägg till frågesträngen `&name=<yourname>` i URL: en.

![Använd sväng för att anropa funktionen i Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

Du kan också klistra in den kopierade webb adressen, inklusive funktions nyckeln, i adress fältet i webbläsaren. Lägg återigen till frågesträngen `&name=<yourname>` i URL: en innan du kör begäran.

![Använda en webbläsare för att anropa-funktionen.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  

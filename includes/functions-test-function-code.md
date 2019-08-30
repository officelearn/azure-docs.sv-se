---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 9381f436aaeedb31732f853a6c4765ac43c6a752
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70170902"
---
## <a name="test"></a>Testa funktionen i Azure

Använd sväng för att testa den distribuerade funktionen. Använd URL: en, inklusive funktions nyckeln, som du kopierade från föregående steg, Lägg till frågesträngen `&name=<yourname>` i URL: en.

![Använd sväng för att anropa funktionen i Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

Du kan också klistra in den kopierade URL-adressen, inklusive funktions nyckeln, i webb adressen till webbläsaren. Lägg återigen till frågesträngen `&name=<yourname>` i URL: en innan du kör begäran.

![Använda en webbläsare för att anropa-funktionen.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  

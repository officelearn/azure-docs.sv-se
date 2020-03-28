---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 2bfa356deeede1c16bd5a464ea7081132a67faf6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67187859"
---
## <a name="append-other-apis"></a>Lägg till andra API:er

Ett API kan bestå av API:er som exponeras av olika tjänster, bland annat OpenAPI-specifikation, SOAP-API, API-appfunktionen i Azure App Service, Azure-funktionsappen, Azure Logic Apps och Azure Service Fabric.

![Importera ett API](./media/api-management-append-apis/import.png)

För att lägga till ett annat API till ditt befintliga API utför du följande steg. När du importerar ett annat API läggs åtgärderna i ditt aktuella API.

1. Öppna Azure API Management-instansen i Azure-portalen.
2. Välj **API:er** i menyn till vänster.
3. Klicka på **...** intill det API som du vill lägga till ett annat API till.
4. Välj **Import** från den nedrullningsbara menyn.
5. Välj en tjänst som ett API ska importeras från.
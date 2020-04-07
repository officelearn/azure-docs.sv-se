---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: f4f109a6da0cb75555f9aadfa2fff31b8aa3107d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673480"
---
Nu kan du använda `msg` den nya parametern för att skriva till utdatabindningen från funktionskoden. Lägg till följande kodrad innan det lyckade `name` svaret `msg` för att lägga till värdet för utdatabindningen.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33":::

När du använder en utdatabindning behöver du inte använda Azure Storage SDK-koden för autentisering, hämta en köreferens eller skriva data. Funktionskörnings- och köutdatabindningen utför dessa uppgifter åt dig.

Din `run` metod bör nu se ut som följande exempel:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-37":::
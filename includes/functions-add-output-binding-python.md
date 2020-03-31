---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191083"
---
Uppdatera *HttpExample\\\_\_\_\_init .py* för att `msg` matcha följande kod, lägga till parametern i funktionsdefinitionen och `msg.set(name)` under uttrycket. `if name:`

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Parametern `msg` är en [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest)instans av . Metoden `set` skriver ett strängmeddelande till kön, i det här fallet det namn som skickas till funktionen i URL-frågesträngen.

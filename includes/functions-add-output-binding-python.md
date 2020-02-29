---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191083"
---
Uppdatera *HttpExample\\\_\_init\_\_. py* för att matcha följande kod, lägga till `msg`-parametern i funktions definitionen och `msg.set(name)` under `if name:`-instruktionen.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Parametern `msg` är en instans av [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest). Dess `set` metod skriver ett sträng meddelande till kön, i det här fallet det namn som skickades till funktionen i URL-frågesträngen.

---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 6a359cdd44cc0c0cfbd93bd23b69a67a641c7fbb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673291"
---
## <a name="update-the-tests"></a>Uppdatera testerna

Eftersom archetype också skapar en uppsättning tester måste du uppdatera de här testerna för att hantera den nya `msg` parametern i `run` Metodsignaturen.  

Bläddra till platsen för test koden under _src/test/java_, öppna filen *Function. java* -projekt och ersätt kodraden under `//Invoke` med följande kod.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
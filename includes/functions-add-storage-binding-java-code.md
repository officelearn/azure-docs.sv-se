---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: f4f109a6da0cb75555f9aadfa2fff31b8aa3107d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673480"
---
Nu kan du använda den nya `msg` parametern för att skriva till utgående bindning från din funktions kod. Lägg till följande kodrad innan du lyckas med att lägga till värdet för `name` i `msg` utmatnings bindningen.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33":::

När du använder en utgående bindning behöver du inte använda Azure Storage SDK-koden för autentisering, hämta en referens till kön eller skriva data. Bindningarna Functions Runtime och Queue output utför dessa uppgifter åt dig.

`run`Metoden bör nu se ut som i följande exempel:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-37":::
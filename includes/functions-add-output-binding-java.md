---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: c27f3fea6d2dd8b891220ba06b375b33e6cd950b
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673496"
---
I ett Java-projekt definieras bindningarna som bindande anteckningar på funktionsmetoden. *Filen function.json* skapas sedan automatiskt baserat på dessa anteckningar.

Bläddra till platsen för din _funktionskod under src/main/java_, öppna *function.java-projektfilen* och lägg till följande parameter i metoddefinitionen: `run`

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

Parametern `msg` är [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) en typ som representerar en samling strängar som skrivs som meddelanden till en utdatabindning när funktionen är klar. I det här fallet är utdata en lagringskö med namnet `outqueue`. Anslutningssträngen för lagringskontot `connection` anges med metoden. I stället för själva anslutningssträngen skickar du programinställningen som innehåller anslutningssträngen för lagringskonto.

Metoddefinitionen `run` ska nu se ut som följande exempel:  

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-22":::
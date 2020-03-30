---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 5e1a2622df0038141dd5cb05237f93d5e33e0bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190931"
---
I ett C#-klassbiblioteksprojekt definieras bindningarna som bindningsattribut på funktionsmetoden. *Den function.json-fil som* krävs av Functions genereras sedan automatiskt baserat på dessa attribut.

Öppna *HttpExample.cs* projektfilen och lägg till följande `Run` parameter i metoddefinitionen:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

Parametern `msg` är `ICollector<T>` en typ som representerar en samling meddelanden som skrivs till en utdatabindning när funktionen är klar. I det här fallet är utdata en lagringskö med namnet `outqueue`. Anslutningssträngen för lagringskontot `StorageAccountAttribute`anges av . Det här attributet anger den inställning som innehåller anslutningssträngen för lagringskonto och kan användas på klass-, metod- eller parameternivå. I det här fallet `StorageAccountAttribute` kan du utelämna eftersom du redan använder standardlagringskontot.

Definitionen av körningsmetod ska nu se ut så här:  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::

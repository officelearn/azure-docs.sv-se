---
author: baanders
description: ta med fil som beskriver hur du verifierar Azure Digitals dubbla modeller
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 0146660fa50f9a136cd82483200ca2e6c6cc0b89
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87985892"
---
> [!TIP]
> När du har skapat en modell rekommenderar vi att du validerar dina modeller offline innan du laddar upp dem till din Azure Digital-instansen.

Det finns ett språk oberoende exempel som kan användas för att validera modell dokument för att kontrol lera att DTDL är korrekt. Den finns här: [**DTDL validator-exempel**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

DTDL validator-exemplet bygger på ett .NET DTDL parser-bibliotek, som är tillgängligt på NuGet som ett bibliotek på klient sidan: [**Microsoft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). Du kan också använda biblioteket direkt för att skapa en egen verifierings lösning. När du använder parser-biblioteket ska du se till att använda en version som är kompatibel med den version som Azure Digital är igång. Under för hands versionen är detta versions *3.7.0*.

Du kan lära dig mer om verifierings exemplet och tolknings biblioteket, inklusive användnings exempel, i [*How-to: parsa och validera modeller*](../articles/digital-twins/how-to-parse-models.md).
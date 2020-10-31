---
author: baanders
description: ta med fil som beskriver hur du verifierar Azure Digitals dubbla modeller
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: bccf92c6c912747e28b3075f12ac1558cced30c9
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130389"
---
> [!TIP]
> När du har skapat en modell rekommenderar vi att du validerar dina modeller offline innan du laddar upp dem till din Azure Digital-instansen.

Det finns ett oberoende exempel som kan användas för att validera modell dokument för att kontrol lera att DTDL är rätt innan du laddar upp den till din instans. Den finns här: [**DTDL validator-exempel**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

* DTDL validator-exemplet bygger på ett .NET DTDL parser-bibliotek, som är tillgängligt på NuGet som ett bibliotek på klient sidan: [**Microsoft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). Du kan också använda biblioteket direkt för att skapa en egen verifierings lösning. När du använder parser-biblioteket ska du se till att använda en version som är kompatibel med den version som Azure Digital är igång. Detta är för närvarande version *3.12.4* .

Du kan lära dig mer om verifierings exemplet och tolknings biblioteket, inklusive användnings exempel, i [*How-to: parsa och validera modeller*](../articles/digital-twins/how-to-parse-models.md).
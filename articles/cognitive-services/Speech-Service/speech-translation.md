---
title: Om Talöversättning | Microsoft Docs
description: En översikt över funktionerna i Talöversättning
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: a569c968d444c36ceb3bce4779d2eca39c21f9bc
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069211"
---
# <a name="about-the-speech-translation-api"></a>Om API för Talöversättning

Microsoft Speech API kan du lägga till slutpunkt till slutpunkt, i realtid, flera språk översättning av tal till dina program, verktyg och enheter. Samma API kan användas för både tal-till-tal- och tal till text.

Med Microsoft Translator Speech API klientprogram strömma tal ljud till tjänsten och få tillbaka en dataström med resultat. Den tolkade texten ingår i en källspråket originaltexten och översättningen i målspråket som i resultaten. Tillfälliga översättningar anges tills ett uttryck är klar, då en slutlig översättning har angetts.

Du kan också kan ett syntetiskt ljudversion av den slutliga translation förberedas, aktiverar SANT tal-till-talöversättning.

Speech Translation-API använder WebSockets-protokollet för att tillhandahålla en full duplex kommunikationskanalen mellan klienten och servern. Men du behöver inte behöver bry dig om WebSockets; Tal-SDK hanterar det åt dig.

Speech Translation-API använder samma teknik som driver olika Microsoft-produkter och tjänster. Den här tjänsten används redan av tusentals företag över hela världen i sina program och arbetsflöden.

## <a name="about-the-technology"></a>Om tekniken

Underliggande motor för översättning av Microsofts finns två olika sätt: statistiska maskinöversättning (SMT) och neural maskinöversättning (NMT). Det senare, en metod för artificiell intelligens använda neurala nätverk, är mer modern lösning för maskinöversättning. NMT ger bättre översättningar – inte bara mer korrekt, men även fler fluent och naturlig. Viktiga orsaken till den här flytbarhet är att NMT använder fullständig kontexten för en mening för att översätta ord.

Idag, har Microsoft migrerats till NMT för de mest populära språk, använda SMT endast för mindre ofta använda språk. Alla [språk som är tillgängliga för tal till talöversättning](supported-languages.md#speech-translation) drivs av NMT. Tal till text translation kan använda SMT eller NMT beroende på språkparet. Om målspråket som stöds av NMT, är hela översättningen NMT-baserade. Om målspråket inte stöds av NMT, är översättningen en kombination av NMT och SMT, använda engelska som en ”pivot” mellan de två språk.

Skillnaderna mellan modeller är interna för översättning-motorn. Slutanvändare ser endast förbättrad översättningen, särskilt för kinesiska, japanska och arabiska.

> [!NOTE]
> Vill du veta mer om tekniken bakom Microsofts translation engine? Se [maskinöversättning](https://www.microsoft.com/en-us/translator/mt.aspx).

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du kan känna igen tal i C#](quickstart-csharp-dotnet-windows.md)

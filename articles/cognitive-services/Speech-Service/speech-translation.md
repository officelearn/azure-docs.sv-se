---
title: Om tal översättning | Microsoft Docs
description: En översikt över funktionerna för översättning av tal
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 43fcde887c21794989aa43540a214ef34893a630
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355617"
---
# <a name="about-the-speech-translation-api"></a>Om tal översättning API

Microsoft tal-API kan du lägga till slutpunkt till slutpunkt, realtid, flerspråkig översättning av tal program, verktyg och enheter. Samma API kan användas för både tal till tal- och tal till text översättning.

Med Microsoft översättare tal-API klientprogram strömma tal ljud till tjänsten och få tillbaka en dataström med resultat. De här resultaten returneras innehåller tolkade i käll-språk och dess översättning på språket som mål. Mellanliggande översättningar lämnas tills en utterance har slutförts, som en slutlig översättning tillhandahålls.

Du kan också ett syntetiskt ljud version slutliga översättningens kan skapas, aktivera true tal till tal-översättning.

Tal översättning API använder ett WebSockets protokoll för att ge en full duplex kommunikationskanalen mellan klienten och servern. Men du behöver inte hantera WebSockets; Tal SDK hanterar som du.

Tal översättning API använder samma teknik som power olika Microsoft-produkter och tjänster. Den här tjänsten används redan av tusentals företag över hela världen i sina program och arbetsflöden.

## <a name="about-the-technology"></a>Om tekniken

Underliggande Microsofts översättning motorn finns två olika sätt: statistiska maskinöversättning (SMT) och neural maskinöversättning (NMT). Den senare, en artificiell intelligence-metod som använder neurala nätverk, är det mer modern tillvägagångssättet för maskinöversättning. NMT ger bättre översättningar – inte bara mer korrekt, men också fler löpande och fysiska. Viktiga orsaken till det här flytbarhet är att NMT använder fullständig kontexten för en mening för att översätta ord.

Idag, har Microsoft migrerats till NMT för de vanligaste språken använda SMT endast för mindre ofta använda språk. Alla [språk som är tillgängliga för tal-till-tal översättning](supported-languages.md#speech-translation) drivs av NMT. Översättning till text till tal kan använda SMT eller NMT beroende på språkparet. Om mål-språk stöds av NMT är fullständig översättningen NMT påslagen. Om mål-språk inte stöds av NMT, är översättningen en kombination av NMT och SMT, använda engelska som ”pivot” mellan de två språk.

Skillnader mellan modeller är interna för översättning-motorn. Endast förbättrad översättningen, särskilt för kinesiska, japanska och arabiska Lägg märke till slutanvändare.

> [!NOTE]
> Vill du veta mer om tekniken bakom Microsofts översättning motorn? Se [maskinöversättning](https://www.microsoft.com/en-us/translator/mt.aspx).

## <a name="next-steps"></a>Nästa steg

* [Hämta din utvärderingsprenumeration tal](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du identifierar tal i C#](quickstart-csharp-windows.md)

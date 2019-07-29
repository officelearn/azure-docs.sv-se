---
title: Vad är en ord lista? – Anpassade Translator
titleSuffix: Azure Cognitive Services
description: En ord lista är ett justerat dokument som anger en lista med fraser eller meningar (och deras översättningar) som du alltid vill att Microsoft Translator ska översätta på samma sätt. Ord listor kallas ibland även Glossaries-eller term Bases.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 5103526956b5041771a1d8e4abb5e8800b971059
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595383"
---
# <a name="what-is-a-dictionary"></a>Vad är en ord lista?

En ord lista är ett justerat par med dokument som anger en lista med fraser eller meningar och deras motsvarande översättningar. Använd en ord lista i din utbildning, om du vill att Microsoft Translator alltid ska översätta alla instanser av käll frasen eller meningen med den översättning som du har angett i ord listan. Ord listor kallas ibland Glossaries-eller term Bases. Du kan tänka på att ord listan är bruten "kopiera och Ersätt" för alla de termer som du har angett.

Ord listor fungerar bara för projekt i språk par som har ett fullständigt NMT-system (Microsoft neurala Machine Translation) bakom dem. [Visa den fullständiga listan med språk](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Fras ord lista
När du inkluderar en fras ord lista i träna din modell, översätts alla ord eller fraser som visas på det sätt som du har angett. Resten av meningen översätts som vanligt. Du kan använda en fras ord lista för att ange fraser som inte ska översättas genom att tillhandahålla samma översättnings fras i käll-och mål filen i ord listan.

## <a name="sentence-dictionary"></a>Ord lista för mening
Med ord listan för mening kan du ange en exakt mål översättning för en käll mening. För att en menings ord lista ska matchas måste hela den mening som skickats matcha käll ord listans post.  Om endast en del av meningen matchar, kommer posten inte att matcha.  När en matchning identifieras returneras mål posten för ord listan.

## <a name="dictionary-only-trainings"></a>Endast lexikon-utbildningar
Du kan träna en modell med endast data från en ord lista. Om du vill göra det väljer du bara lexikon dokumentet (eller flera ord listor) som du vill ta med och trycker på Skapa modell. Eftersom det här är en utbildning endast för ord lista finns det inget minsta antal utbildnings meningar som krävs. Din modell kommer normalt att slutföra utbildningen mycket snabbare än en standard utbildning.  De resulterande modellerna använder Microsofts bas linje modeller för översättning med tillägg av de ord listor som du har lagt till.  Du får ingen test rapport.

>[!Note]
>Den anpassade översättaren får inte menings justering, så det är viktigt att det är lika många käll-och mål fraser/meningar i dina ord listor och att de är exakt justerade.

## <a name="recommendations"></a>Rekommendationer

- Ord listor är inte en ersättning för en tränad modell med tränings data.  Ord listor som huvudsakligen söker efter och ersätter ord eller meningar.  Att låta systemet lära sig av utbildnings materialet i fullständiga meningar är vanligt vis ett bättre alternativ än att använda en ord lista.
- Fras ord listan bör användas sparsamt. När en fras i en mening ersätts går kontexten i den meningen förlorad eller begränsad för översättning av resten av meningen. Resultatet är att när frasen eller ordet i meningen översätts enligt fras ord listan kommer den övergripande översättnings kvaliteten för meningen ofta att bli lidande.
- Fras ord listan fungerar bra för sammansatta substantiv som produkt namn ("Microsoft SQL Server"), rätt namn ("stad") eller funktioner i produkten ("Pivot Table"). Det fungerar inte lika bra för verb eller adjektiv eftersom dessa vanligt vis är mycket inflected i källan eller på mål språket. Undvik ord listors poster för allt utom sammansatta substantiv.
- När du använder en ord lista visar versaler och interpunktion i dina översättningar de versaler och skiljetecken som finns i målfilen. Versaler och skiljetecken ignoreras när du försöker identifiera matchningar mellan din inmatade mening och käll meningarna i din Dictionary-fil. Anta till exempel att vi har tränat ett engelskt till spanska-system som använde en ord lista som angavs "City" i käll filen och "Ciudad de L'" i mål filen. Om jag har begärt översättning av en mening som innehöll frasen "stad, stad" skulle "staden," "stad" matcha till min ordlistefil-fil för posten "City," och mappar till "Ciudad de L'" i min slutliga översättning.
- Om ett ord förekommer mer än en gång i en ordlistefil, kommer systemet alltid att använda den senast angivna posten. Ord listan får inte innehålla flera översättningar av samma ord.

## <a name="next-steps"></a>Nästa steg

- Läs om [rikt linjer för dokument format](document-formats-naming-convention.md).

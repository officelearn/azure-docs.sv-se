---
title: Vad är en ordlista? – Anpassade Translator
titleSuffix: Azure Cognitive Services
description: En ordlista är ett justerade dokument som anger en lista med fraser eller meningar (och deras översättningar) som du vill använda Microsoft Translator att översätta på samma sätt. Ordlistor kallas ibland också ordlistorna eller termen baser.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: bfefb1fe44959bc7e5186a0f14813f41256cf2d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583357"
---
# <a name="what-is-a-dictionary"></a>Vad är en ordlista?

En ordlista är en justerade par dokument som specificerar en lista med fraser eller meningar och deras motsvarande översättningar. Skapa en ordlista i din utbildning, när du vill att Microsoft Translator att alltid översätta alla instanser av den källa fras eller en mening, med hjälp av översättning som du har angett i ordlistan. Ordlistor kallas ibland ordlistorna eller termen baser. Du kan se ordlistan som en brute force ”kopia och Ersätt” för alla villkor du lista.

Ordlistor fungerar endast för projekt i språkpar som har ett fullständigt stöd Microsoft neural maskinöversättning (NMT) system bakom dem. [Visa en fullständig lista över språk som](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Fras ordlista
När du inkluderar en fras ordlista i träna din modell, översätts ord eller fraser som visas på det sätt som du har angett. Resten av meningen översätts som vanligt. Du kan använda en fras ordlista för att ange fraser som inte ska översättas genom att tillhandahålla samma Oöversatt fras i filen källa och mål i ordlistan.

## <a name="sentence-dictionary"></a>Mening ordlista
Mening-ordlista kan du ange en översättning för exakta mål för en mening i källan. Hela skickade meningen måste matcha posten ordlistan källa för en mening ordlista matchning ska lyckas.  Om det bara en del av meningen matchar matcha inte posten.  När en matchning hittas returneras mål-inmatning av mening ordlistan.

## <a name="dictionary-only-trainings"></a>Endast ordlista utbildningar
Du kan träna en modell med hjälp av endast ordlista data. Om du vill göra detta, Välj ordlista dokumentet (eller flera ordlista dokument) som du vill inkludera och tryck på modell. Eftersom det här är en ordlista endast utbildning, finns inga minsta antal utbildning meningar som krävs. Din modell slutförs vanligtvis utbildning som är mycket snabbare än en standard-utbildning.  De resulterande modellerna använder Microsoft baseline modeller för översättning med hjälp av ordlistor som du har lagt till.  Du kommer inte att hämta en testrapport.

>[!Note]
>Anpassade Translator inte meningen justera ordlistefiler, så det är viktigt att det inte finns lika många av käll- och fraser / meningar i din ordlista dokument och att de exakt är justerade.

## <a name="recommendations"></a>Rekommendationer

- Ordlistor är inte en ersättning för en tränad modell med träningsdata.  Ordlistor i stort sett Sök och Ersätt ord eller meningar.  Att låta systemet Lär dig av din utbildningsmaterial i fullständiga meningar är vanligtvis ett bättre alternativ än att använda en ordlista.
- Fras ordlistan bör användas sparsamt. När en fras i en mening ersätts kontexten i den meningen tappas bort eller begränsad översätta resten av meningen. Resultatet är att när frasen eller ord i meningen översätter enligt frasen ordlista, övergripande översättningen i meningen ofta blir lidande.
- Fras ordlistan fungerar bra för sammansatt substantiv som produktnamn (”Microsoft SQL Server”), egennamn (”stad av Hamburg”) eller funktioner i produkten (”pivottabell”). Det fungerar inte bra både för verb eller adjektiv eftersom dessa är vanligtvis mycket böjda i källan eller på språket som mål. Undvik att frasen dictionary-posterna för något annat än sammansatt substantiv.
- När du använder en ordlista, avspeglar versaler och skiljetecken i dina översättningar versaler och skiljetecken i din målfilen. Versaler och skiljetecken ignoreras när du försöker identifiera matchningar mellan dina indata mening och källa meningar i ordlistefilen. Anta exempelvis att vi tränas en engelska till spanska system som används för en ordlista som den angivna ”stad av Hamburg” i källfilen och ”Gasledning Ciudad de hamburg” i målfilen. Om jag har begärt översättning av en mening som ingår frasen ”stad av Hamburg” sedan ”stad av Hamburg” matchar till ordlistefilen för posten ”stad av Hamburg” och vill mappa till ”Gasledning Ciudad de hamburg” i Min sista översättning.
- Om ett ord visas mer än en gång i en ordlistefil, använder alltid den sista posten som tillhandahålls av systemet. Din ordlista får inte innehålla flera översättningar av samma ord.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [riktlinjer för dokumentformat](document-formats-naming-convention.md).

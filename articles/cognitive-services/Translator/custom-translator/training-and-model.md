---
title: Vad är utbildning och modell? - Anpassad översättare
titleSuffix: Azure Cognitive Services
description: En modell är systemet, som tillhandahåller översättning för ett visst språkpar. Resultatet av en framgångsrik utbildning är en modell. När du tränar en modell krävs tre ömsesidigt uteslutande datauppsättningar för utbildningsdatauppsättning, justeringsdatauppsättning och testningsdatauppsättning.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: 71f1e3f460fa58b999af0a60c8cffa90c8ac8cd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219460"
---
# <a name="what-are-trainings-and-models"></a>Vad är utbildningar och modeller?

En modell är systemet, som tillhandahåller översättning för ett visst språkpar.
Resultatet av en framgångsrik utbildning är en modell. När du tränar en modell krävs tre ömsesidigt uteslutande dokumenttyper: utbildning, justering och testning. Dokumenttypen Ordlista kan också tillhandahållas. Se [meningsjustering](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment#suggested-minimum-number-of-sentences).

Om endast träningsdata tillhandahålls när du köar en utbildning, kommer Custom Translator automatiskt att montera justerings- och testdata. Den kommer att använda en slumpmässig delmängd av meningar från dina träningsdokument och utesluta dessa meningar från själva träningsdata.

## <a name="training-document-type-for-custom-translator"></a>Utbildningsdokumenttyp för anpassad översättare

Dokument som ingår i utbildningsuppsättningen används av Custom Translator som grund för att skapa din modell. Under utbildningskörningen justeras meningar som finns i dessa dokument (eller paras ihop). Du kan ta dig friheter när du skriver dina träningsdokument. Du kan inkludera dokument som du tror är av tangentiell relevans i en modell. Återigen utesluta dem i en annan för att se effekterna i [BLEU (Tvåspråkig utvärdering Inhoppare) poäng](what-is-bleu-score.md). Så länge du håller tuning set och test set konstant, gärna experimentera med sammansättningen av utbildningen set. Detta tillvägagångssätt är ett effektivt sätt att ändra kvaliteten på ditt översättningssystem.

Du kan köra flera utbildningar inom ett projekt och jämföra [BLEU-poängen](what-is-bleu-score.md) över alla utbildningskörningar. När du kör flera utbildningar för jämförelse, se till att samma justerings-/testdata anges varje gång. Se också till att också inspektera resultaten manuellt på fliken ["Testning".](how-to-view-system-test-results.md)

## <a name="tuning-document-type-for-custom-translator"></a>Justera dokumenttyp för anpassad översättare

Parallella dokument som ingår i denna uppsättning används av Custom Translator för att ställa in översättningssystemet för optimalt resultat.

Justeringsdata används under träning för att justera översättningssystemets alla parametrar och vikter till optimala värden. Välj dina justeringsdata noggrant: justeringsdata ska vara representativa för innehållet i de dokument som du tänker översätta i framtiden. Trimdata har en stor inverkan på kvaliteten på de producerade översättningarna. Justering gör det möjligt för översättningssystemet att tillhandahålla översättningar som ligger närmast de exempel du tillhandahåller i justeringsdata. Du behöver inte mer än 2500 meningar i dina justeringsdata. För optimal översättningskvalitet rekommenderas att du väljer justeringsuppsättningen manuellt genom att välja det mest representativa urvalet av meningar.

När du skapar justeringsuppsättningen väljer du meningar som är en meningsfull och representativ längd på de framtida meningar som du förväntar dig att översätta. Du bör också välja meningar som har ord och fraser som du tänker översätta i den ungefärliga distribution som du förväntar dig i dina framtida översättningar. I praktiken kommer en meningslängd på 7 till 10 ord att ge de bästa resultaten, eftersom dessa meningar innehåller tillräckligt med sammanhang för att visa böjningar och ge en fraslängd som är betydande, utan att vara alltför komplex.

En bra beskrivning av vilken typ av meningar som ska användas i justeringsuppsättningen är prosa: faktiska flytande meningar. Inte tabellceller, inte dikter, inte listor över saker, inte bara skiljetecken, eller siffror i en mening - vanligt språk.

Om du manuellt väljer dina justeringsdata bör de inte ha någon av samma meningar som dina tränings- och testdata. Trimdata har en betydande inverkan på kvaliteten på översättningarna - välj meningar noggrant.

Om du inte är säker på vad du ska välja för dina justeringsdata väljer du bara träningsdata och låter Custom Translator välja justeringsdata åt dig. När du låter custom translator välja justeringsdata automatiskt, kommer den att använda en slumpmässig delmängd av meningar från dina tvåspråkiga träningsdokument och utesluta dessa meningar från själva utbildningsmaterialet.

## <a name="testing-dataset-for-custom-translator"></a>Testa datauppsättning för anpassad översättare

Parallella dokument som ingår i testuppsättningen används för att beräkna BLEU-poängen (Tvåspråkig utvärdering) . Den här poängen anger kvaliteten på ditt översättningssystem. Den här poängen talar faktiskt om hur nära översättningarna som görs av översättningssystemet till följd av den här träningen matchar referensmeningarna i testdatauppsättningen.

BLEU-poängen är ett mått på deltat mellan den automatiska översättningen och referensöversättningen. Dess värde varierar från 0 till 100. Poängen 0 anger att inte ett enda ord i referensen visas i översättningen. Poängen 100 anger att den automatiska översättningen exakt matchar referensen: samma ord är i exakt samma position. Poängen du får är BLEU-poänggenomsnittet för alla meningar i testdata.

Testdata bör innehålla parallella dokument där målspråksmeningar är de mest önskvärda översättningarna av motsvarande källspråkmeningar i källmålparet. Du kanske vill använda samma villkor som du använde för att skriva justeringsdata. Testdata har dock inget inflytande över översättningssystemets kvalitet. Det används uteslutande för att generera BLEU-poängen för dig.

Du behöver inte mer än 2 500 meningar som testdata. När du låter systemet välja testuppsättningen automatiskt, kommer det att använda en slumpmässig delmängd av meningar från din tvåspråkiga träningsdokument, och utesluta dessa meningar från själva utbildningsmaterialet.

Du kan visa de anpassade översättningarna av testuppsättningen och jämföra dem med översättningarna i testuppsättningen genom att navigera till testfliken i en modell.

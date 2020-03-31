---
title: Djupinlärning kontra maskininlärning
titleSuffix: Azure
description: Lär dig hur djupinlärning relaterar till maskininlärning och artificiell intelligens. Djupinlärning används i scenarier som bedrägeriidentifiering, röst & ansiktsigenkänning, sentimentanalys och tidsserieprognoser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 03/05/2020
ms.openlocfilehash: bcacf4ff66e114f65ac75f0aadd1564875f15f62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501365"
---
# <a name="deep-learning-vs-machine-learning"></a>Djupinlärning kontra maskininlärning

Den här artikeln hjälper dig att jämföra djupinlärning jämfört med maskininlärning. Du får lära dig hur de två begreppen jämför och hur de passar in i den bredare kategorin artificiell intelligens. Artikeln beskriver också hur djupinlärning kan tillämpas på verkliga scenarier som bedrägeriidentifiering, röst- och ansiktsigenkänning, sentimentanalys och tidsserieprognoser.

## <a name="deep-learning-machine-learning-and-ai"></a>Djupinlärning, maskininlärning och AI

![Relationsdiagram: AI kontra maskininlärning kontra djupinlärning](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Tänk på följande definitioner för att förstå djupinlärning kontra maskininlärning jämfört med AI:

- **Djupinlärning** är en delmängd av maskininlärning som är baserad på artificiella neurala nätverk. _Inlärningsprocessen_ är _djup_ eftersom strukturen av artificiella neurala nätverk består av flera indata, utgångar och dolda lager. Varje lager innehåller enheter som omvandlar indata till information som nästa lager kan använda för en viss prediktiv aktivitet. Tack vare denna struktur kan en maskin lära sig genom sin egen databehandling.

- **Maskininlärning** är en delmängd av artificiell intelligens som använder tekniker (t.ex. djupinlärning) som gör det möjligt för datorer att använda erfarenhet för att förbättra vid uppgifter. _Inlärningsprocessen_ bygger på följande steg:

   1. Mata in data i en algoritm. (I det här steget kan du ge ytterligare information till modellen, till exempel genom att utföra funktionsextrahering.)
   1. Använd dessa data för att träna en modell.
   1. Testa och distribuera modellen.
   1. Förbruka den distribuerade modellen för att göra en automatiserad prediktiv uppgift. (Med andra ord anropar och använder den distribuerade modellen för att ta emot förutsägelserna som returneras av modellen.)

- **Artificiell intelligens (AI)** är en teknik som gör det möjligt för datorer att efterlikna mänsklig intelligens. Det inkluderar maskininlärning. 
 
Det är viktigt att förstå förhållandet mellan AI, maskininlärning och djupinlärning. Maskininlärning är ett sätt att uppnå artificiell intelligens. Genom att använda maskininlärning och djupinlärningstekniker kan du skapa datorsystem och program som utför uppgifter som ofta är associerade med mänsklig intelligens. Dessa uppgifter omfattar bildigenkänning, taligenkänning och språköversättning.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Tekniker för djupinlärning kontra maskininlärning 

Nu när du har översikten över maskininlärning kontra djupinlärning, låt oss jämföra de två teknikerna. I maskininlärning måste algoritmen informeras om hur man gör en korrekt förutsägelse genom att konsumera mer information (till exempel genom att utföra funktionsextrahering). I djupinlärning kan algoritmen lära sig att göra en korrekt förutsägelse genom sin egen databehandling, tack vare den artificiella neurala nätverksstrukturen.

I följande tabell jämförs de två teknikerna mer i detalj:

| |All maskininlärning |Endast djupinlärning|
|---|---|---|
|  **Antal datapunkter** | Kan använda små mängder data för att göra förutsägelser. | Måste använda stora mängder träningsdata för att göra förutsägelser. |
|  **Maskinvaruberoenden** | Kan arbeta på low-end maskiner. Den behöver inte en stor mängd beräkningskraft. | Beror på avancerade maskiner. Det gör i sig ett stort antal matris multiplikation operationer. En GPU kan effektivt optimera dessa operationer. |
|  **Mediseringsprocess** | Kräver att funktioner identifieras och skapas korrekt av användare. | Lär sig funktioner på hög nivå från data och skapar nya funktioner av sig själv. |
|  **Inlärningsmetod** | Delar upp inlärningsprocessen i mindre steg. Den kombinerar sedan resultaten från varje steg till en utgång. | Rör dig genom inlärningsprocessen genom att lösa problemet från slutna till. |
|  **Körningstid** | Tar jämförelsevis lite tid att träna, allt från några sekunder till några timmar. | Vanligtvis tar lång tid att träna eftersom en djupinlärningsalgoritm involverar många lager. |
|  **Produktionen** | Utdata är vanligtvis ett numeriskt värde, som en poäng eller en klassificering. | Utdata kan ha flera format, till exempel en text, en poäng eller ett ljud. |

## <a name="deep-learning-use-cases"></a>Användningsfall med djupinlärning

På grund av den artificiella neurala nätverksstrukturen utmärker sig djupinlärning på att identifiera mönster i ostrukturerade data som bilder, ljud, video och text. Av denna anledning är djupinlärning snabbt omvandla många branscher, inklusive hälso-, energi, finans och transport. Dessa branscher omprövar nu traditionella affärsprocesser. 

Några av de vanligaste programmen för djupinlärning beskrivs i följande stycken.

### <a name="named-entity-recognition"></a>Namngiven enhetsigenkänning

Namngiven entitetsigenkänning är en djupinlärningsmetod som tar en text som indata och omvandlar den till en förspecificerad klass. Den här nya informationen kan vara ett postnummer, ett datum, ett produkt-ID. Informationen kan sedan lagras i ett strukturerat schema för att skapa en lista över adresser eller fungera som ett riktmärke för en identitetsvalideringsmotor.

### <a name="object-detection"></a>Objektidentifiering

Djupinlärning har tillämpats i många objektidentifieringsfall. Objektidentifiering består av två delar: bildklassificering och sedan bildlokalisering. _Bildklassificering_ identifierar bildens objekt, till exempel bilar eller personer. _Bildlokalisering_ ger den specifika platsen för dessa objekt. 

Objektdetektering används redan i branscher som spel, detaljhandel, turism och självkörande bilar.

### <a name="image-caption-generation"></a>Generering av bildtext

Precis som bildigenkänning, i bildtexter, för en viss bild, måste systemet generera en bildtext som beskriver innehållet i bilden. När du kan identifiera och märka objekt i fotografier är nästa steg att omvandla dessa etiketter till beskrivande meningar. 

Vanligtvis använder bildtexter applikationer convolutional neurala nätverk för att identifiera objekt i en bild och sedan använda ett återkommande neuralt nätverk för att förvandla etiketterna till konsekventa meningar.

### <a name="machine-translation"></a>Maskinöversättning

Maskinöversättning tar ord eller meningar från ett språk och översätter dem automatiskt till ett annat språk. Maskinöversättning har funnits länge, men djupinlärning ger imponerande resultat inom två specifika områden: automatisk översättning av text (och översättning av tal till text) och automatisk översättning av bilder.

Med lämplig dataomvandling kan ett neuralt nätverk förstå text-, ljud- och visuella signaler. Maskinöversättning kan användas för att identifiera ljudutdrag i större ljudfiler och transkribera det talade ordet eller bilden som text.

### <a name="text-analytics"></a>Textanalys

Textanalys baserad på djupinlärningsmetoder innebär att analysera stora mängder textdata (till exempel medicinska dokument eller utgiftskvitton), känna igen mönster och skapa organiserad och koncis information av den.

Företag använder djupinlärning för att utföra textanalys för att upptäcka insiderhandel och efterlevnad av statliga bestämmelser. Ett annat vanligt exempel är försäkringsbedrägeri: textanalys har ofta använts för att analysera stora mängder dokument för att känna igen chanserna för att ett försäkringsanspråk är bedrägeri. 

## <a name="artificial-neural-networks"></a>Artificiella neurala nätverk

Artificiella neurala nätverk bildas av lager av anslutna noder. Deep learning-modeller använder neurala nätverk som har ett stort antal lager. 

Följande avsnitt utforskar mest populära artificiella neurala nätverk typologier.

### <a name="feedforward-neural-network"></a>Feedforward neurala nätverk

Feedforward neurala nätverk är den mest grundläggande typen av artificiellt neuralt nätverk. I ett feedforward-nätverk flyttas informationen bara i en riktning från indatalager till utdatalager. Feedforward neurala nätverk omvandla en ingång genom att sätta den genom en serie dolda lager. Varje lager består av en uppsättning nervceller, och varje lager är helt ansluten till alla nervceller i lagret innan. Det sista helt anslutna lagret (utdatalagret) representerar de genererade förutsägelserna.

### <a name="recurrent-neural-network"></a>Återkommande neuralt nätverk

Återkommande neurala nätverk är ett allmänt använt artificiellt neuralt nätverk. Dessa nätverk sparar utdata från ett lager och matar tillbaka det till indatalagret för att förutsäga lagrets resultat. Återkommande neurala nätverk har bra inlärningsförmåga. De används ofta för komplexa uppgifter som tidsserieprognoser, inlärning av handskrift och igenkännande språk.

### <a name="convolutional-neural-networks"></a>Faltning neurala nätverk

Ett faltet neuralt nätverk är ett särskilt effektivt artificiellt neuralt nätverk, och det presenterar en unik arkitektur. Lagren är ordnade i tre dimensioner: bredd, höjd och djup. Nervcellerna i ett lager ansluter inte till alla nervceller i nästa lager, men bara till en liten region av lagrets nervceller. Den slutliga utdata reduceras till en enda vektor av sannolikhetspoäng, organiserade längs djupdimensionen. 

Faltiva neurala nätverk har använts inom områden som videoigenkänning, bildigenkänning och rekommenderare system.

## <a name="next-steps"></a>Nästa steg

Följande artiklar visar hur du använder djupinlärningsteknik i [Azure Machine Learning:](https://docs.microsoft.com/azure/machine-learning/?WT.mc_id=docs-article-lazzeri)

- [Klassificera handskrivna siffror med hjälp av en TensorFlow-modell](https://docs.microsoft.com/azure/machine-learning/how-to-train-tensorflow?WT.mc_id=docs-article-lazzeri)

- [Klassificera handskrivna siffror med hjälp av en TensorFlow-skattningsman och Keras](https://docs.microsoft.com/azure/machine-learning/how-to-train-keras?WT.mc_id=docs-article-lazzeri)

- [Klassificera bilder med hjälp av en Pytorch-modell](https://docs.microsoft.com/azure/machine-learning/how-to-train-pytorch?WT.mc_id=docs-article-lazzeri)

- [Klassificera handskrivna siffror med hjälp av en Chainer-modell](https://docs.microsoft.com/azure/machine-learning/how-to-train-chainer?WT.mc_id=docs-article-lazzeri)

Använd också [machine learning-algoritmens fuskblad](../synapse-analytics/sql-data-warehouse/cheat-sheet.md) för att välja algoritmer för din modell.

---
title: Djup inlärning eller Machine Learning
titleSuffix: Azure Machine Learning service
description: Lär dig mer om djup inlärning jämfört med Machine Learning och hur båda begreppen relaterar till artificiell intelligens. Djup inlärning kan användas i scenarier som bedrägeri identifiering, röst-och ansikts igenkänning, sentiment analys och tids serie prognoser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 08/07/2019
ms.openlocfilehash: 3b0546c42ddd5252b35d1a77d7b152733beec233
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240946"
---
# <a name="deep-learning-vs-machine-learning"></a>Djup inlärning eller Machine Learning

Den här artikeln hjälper dig att jämföra djup inlärningen jämfört med Machine Learning. Du lär dig hur de två begreppen jämför och hur de passar in i den bredare kategorin av artificiell intelligens. Artikeln beskriver också hur djup inlärning kan tillämpas på verkliga scenarier, till exempel bedrägeri identifiering, röst-och ansikts igenkänning, sentiment analys och tids serie prognoser.

## <a name="deep-learning-machine-learning-and-ai"></a>Djup inlärning, maskin inlärning och AI

![Relations diagram: AI vs. Machine Learning jämfört med djup inlärning](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Överväg följande definitioner för att förstå djup inlärningen jämfört med Machine Learning jämfört med AI

- **Djup inlärning** är en del av maskin inlärning som baseras på artificiella neurala nätverk. _Inlärnings processen_ är _djupgående_ eftersom strukturen hos artificiellt neurala-nätverk består av flera indata, utdata och dolda lager. Varje lager innehåller enheter som omvandlar indata till information som nästa lager kan använda för en viss förutsägelse aktivitet. Tack vare den här strukturen kan en dator lära sig genom sin egen data bearbetning.

- **Machine Learning** är en del av artificiell intelligens som använder tekniker (till exempel djup inlärning) som gör att datorer kan förbättra sina uppgifter. _Inlärnings processen_ baseras på följande steg:

   1. Mata in data i en algoritm. (I det här steget kan du ange ytterligare information till modellen, till exempel genom att utföra funktions extrahering.)
   1. Använd dessa data för att träna en modell.
   1. Testa och distribuera modellen.
   1. Använd den distribuerade modellen för att göra en automatiserad förutsägande uppgift. (Du kan med andra ord anropa och använda den distribuerade modellen för att ta emot förutsägelserna som returneras av modellen.)

- **Artificiell intelligens (AI)** är en teknik som gör det möjligt för datorer att efterlikna mänsklig intelligens. Den innehåller Machine Learning. 
 
Det är viktigt att förstå förhållandet mellan AI, maskin inlärning och djup inlärning. Machine Learning är ett sätt att få artificiell intelligens. Genom att använda maskin inlärnings-och djup inlärnings tekniker kan du skapa dator system och program som utför uppgifter som ofta är associerade med mänsklig intelligens. De här uppgifterna omfattar visuell uppfattning, tal igenkänning, besluts fattande och språk översättning.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Tekniker för djup inlärning jämfört med Machine Learning 

Nu när du har en översikt över Machine Learning jämfört med djup inlärning ska vi jämföra de två teknikerna. I Machine Learning måste algoritmen uppmanas att göra en korrekt förutsägelse genom att använda mer information (till exempel genom att utföra funktions extrahering). I djup inlärning kan algoritmen lära sig att göra en korrekt förutsägelse genom sin egen data bearbetning, tack vare den artificiella neurala nätverks strukturen.

I följande tabell jämförs de två metoderna i detalj:

| |Alla Machine Learning |Endast djup inlärning|
|---|---|---|
|  **Antal data punkter** | Kan använda små data mängder från användare. | Kräver en stor mängd utbildnings data för att göra kortfattade slut satser. |
|  **Maskin varu beroenden** | Kan arbeta på datorer med låg slut. Det behöver inte vara en stor mängd beräknings kraft. | Är beroende av avancerade datorer. Det finns ett stort antal åtgärder för mat ris multiplikation. En GPU kan effektivt optimera dessa åtgärder. |
|  **Funktionalisering process** | Kräver att funktioner identifieras korrekt och skapas av användare. | Lär dig mer om hög nivå funktioner från data och skapar nya funktioner själva. |
|  **Inlärnings metod** | Delar upp uppgifter i små delar och kombinerar sedan mottagna resultat till en slutsats. | Löser problemet från slut punkt till slut punkt. |
|  **Körnings tid** | Tar relativt lite tid att träna, från några sekunder till några timmar. | Tar en ovanligt lång tid att träna eftersom en djup inlärnings algoritm omfattar många parametrar. |
|  **Resultat** | Utdata är vanligt vis ett numeriskt värde, till exempel en poäng eller en klassificering. | Utdata kan vara text, en poäng, ett element eller ett ljud. |

## <a name="deep-learning-use-cases"></a>Användnings fall för djup inlärning

På grund av den artificiella neurala nätverks strukturen, är djup inlärningen perfekt vid identifiering av mönster i ostrukturerade data som bilder, ljud, video och text. Av den anledningen kan djup inlärningen snabbt omvandla många branscher, inklusive hälso vård, energi, ekonomi och transport. Dessa branscher håller nu på att se till att traditionella affärs processer bevaras. 

Några av de vanligaste programmen för djup inlärning beskrivs i följande stycken.

### <a name="named-entity-recognition"></a>Identifiering av namngivna enheter

En användning av djup inlärnings nätverk heter-enhets igenkänning, vilket är ett sätt att extrahera vissa typer av information från ostrukturerade och omärkta data. Den här informationen kan vara personer, platser, företag eller saker. Informationen kan sedan lagras i ett strukturerat schema för att bygga en lista över adresser eller fungera som ett riktmärke för en identitets validerings motor.

### <a name="object-detection"></a>Objektidentifiering

Djup inlärning har tillämpats i många olika användnings fall för objekt identifiering. Objekt identifiering består av två delar: bild klassificering och avbildnings lokalisering. Bild _klassificeringen_ identifierar avbildningens objekt, till exempel bilar eller personer. Avbildnings _lokalisering_ tillhandahåller den speciella platsen för dessa objekt. 

Objekt identifiering används redan i branscher som spel, detalj handel, turism och självdrivande bilar.

### <a name="image-caption-generation"></a>Generering av bild text

Som bild igenkänning, i bild texter, för en bild måste systemet generera en beskrivning som beskriver innehållet i bilden. När du kan identifiera och märka objekt i fotografier är nästa steg att omvandla dessa etiketter till beskrivande, sammanhängande meningar. I allmänhet använder avbildnings under texter mycket stora (convolutional neurala-nätverk för att identifiera objekt i fotografierna och sedan använda ett omaktuellt neurala-nätverk (RNN) för att omvandla etiketterna till sammanhängande meningar.

### <a name="machine-translation"></a>Maskinöversättning

Maskin översättningen tar ord, fraser eller meningar från ett språk och automatiskt översätter dem till ett annat språk. Automatisk maskin översättning har varit runt under en längre tid, men djup inlärningen uppnår imponerande resultat i två olika områden: automatisk översättning av text (och översättning av tal till text) och automatisk översättning av bilder. 

Med rätt data omvandling kan ett djup nätverk förstå text-, ljud-och visuella signaler. Maskin översättning kan användas för att identifiera kodfragment av ljud i större ljudfiler och skriva över det talade ordet eller bilden som text.

### <a name="text-analytics"></a>Textanalys

En viktig uppgift för djup inlärning är e-identifiering. Företag använder text analys som baseras på djup inlärning för att upptäcka insider handel och efterlevnad med myndighets bestämmelser. Riskminimeringsfonder-fonder använder text analys för att öka detalj nivån i enorma dokument databaser för att få insikter om framtida investerings prestanda och marknads sentiment. Användnings fallet för text analys som bygger på djup inlärning kretsar runt dess förmåga att parsa enorma mängder text data och utföra analys-eller avkastnings agg regeringar.

## <a name="artificial-neural-networks"></a>Artificiell neurala-nätverk

Artificiell neurala-nätverk bildas av lager av anslutna noder. I djup inlärnings modeller används neurala-nätverk som har ett stort antal lager. 

I följande avsnitt lär du de flesta populära artificiell neurala-nätverkstopologier.

### <a name="feedforward-neural-network"></a>Feedforward neurala-nätverk

Feedforward neurala Network är den mest grundläggande typen av artificiellt neurala-nätverk. I ett feedforward nätverk överförs information endast i en riktning från indatanivå till output-lagret. Feedforward neurala Networks transformerar inmatningar genom att placera det genom en serie med dolda lager. Varje lager består av en uppsättning neurons och varje lager är fullständigt anslutet till alla neurons i lagret. Det senast fullständigt anslutna lagret (utmatnings lagret) representerar de genererade förutsägelserna.

### <a name="recurrent-neural-network"></a>Aktuellt neurala-nätverk

Befintliga neurala-nätverk är ett mycket vanligt artificiellt neurala nätverk. Dessa nätverk sparar utdata för ett lager och matar tillbaka det till inmatnings lagret för att förutsäga lagrets resultat. Befintliga neurala-nätverk har fantastiska inlärnings möjligheter. De används ofta för komplexa uppgifter, till exempel inlärning av hand skrift och tolkning av språk.

### <a name="convolutional-neural-networks"></a>(Convolutional neurala-nätverk

Ett (convolutional neurala-nätverk är ett särskilt effektivt artificiellt neurala-nätverk och det presenterar en unik arkitektur. Lager ordnas i tre dimensioner: bredd, höjd och djup. Neurons i ett lager ansluter inte till alla neurons i nästa skikt, men endast till en liten region i lagrets neurons. Den slutliga utmatningen reduceras till en enda kombination av sannolikhets poäng, ordnat längs djup dimensionen. 

(Convolutional neurala-nätverk har använts i områden som bild igenkänning och klassificering.

## <a name="next-steps"></a>Nästa steg

I följande artiklar visas hur du använder djup inlärnings teknik i [Azure Machine learnings tjänsten](https://docs.microsoft.com/azure/machine-learning/service/?WT.mc_id=docs-article-lazzeri):

- [Klassificera handskrivna siffror med hjälp av en TensorFlow-modell](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow?WT.mc_id=docs-article-lazzeri)
- [Klassificera handskrivna siffror med en TensorFlow-uppskattning och keras](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-keras?WT.mc_id=docs-article-lazzeri)
- [Klassificera bilder med hjälp av en Pytorch-modell](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-pytorch?WT.mc_id=docs-article-lazzeri)
- [Klassificera handskrivna siffror med hjälp av en kedje modell](https://docs.microsoft.com/en-us/azure/machine-learning/service/how-to-train-chainer?WT.mc_id=docs-article-lazzeri)

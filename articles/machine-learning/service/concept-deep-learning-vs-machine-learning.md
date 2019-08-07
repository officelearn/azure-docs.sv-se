---
title: Introduktion till djup inlärning eller Machine Learning
titleSuffix: Azure Machine Learning service
description: Lär dig mer om relationen mellan djup inlärning och Machine Learning och hur de passar in i kategorin artificiell intelligens. Den här artikeln beskriver hur djup inlärningen löser scenarier, till exempel bedrägeri identifiering, röst-och ansikts igenkänning, sentiment analys och tids serie prognoser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 08/07/2019
ms.openlocfilehash: 4b66d015e8a182e305a36dacaa2e082288a3e19e
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840535"
---
# <a name="introduction-to-deep-learning-vs-machine-learning"></a>Introduktion till djup inlärning eller Machine Learning

Den här artikeln hjälper dig att förstå förhållandet mellan djup inlärning och Machine Learning. Du lär dig hur de två begreppen jämför och hur de passar in i den bredare kategorin av artificiell intelligens. Slutligen beskriver artikeln Hur djup inlärning kan tillämpas på verkliga scenarier, till exempel bedrägeri identifiering, röst-och ansikts igenkänning, sentiment analys och tids serie prognoser.

## <a name="how-do-deep-learning-machine-learning-and-ai-relate-to-one-another"></a>Hur relaterar djup inlärning, maskin inlärning och AI till varandra?

![Relations diagram: AI vs. Machine Learning jämfört med djup inlärning](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

För att förstå skillnaden mellan djup inlärning jämfört med Machine Learning jämfört med AI, lär dig mer om var och en av dessa fält:

- **Djup inlärning** är en del av Machine Learning baserat på artificiell neurala-nätverk som gör det möjligt för en dator att träna sig själv. I det här fallet kallas _inlärnings processen_ _djup_, eftersom strukturen hos artificiellt neurala-nätverk består av flera indata, utdata och dolda lager. Varje lager innehåller enheter som omvandlar indata till information som nästa lager kan använda för att utföra en viss förutsägelse aktivitet. Tack vare den här strukturen kan en dator lära sig genom sin egen data bearbetning.

- **Machine Learning** är en del av artificiell intelligens som innehåller tekniker (till exempel djup inlärning) som gör det möjligt för datorer att förbättra i aktiviteter med erfarenhet. I det här fallet baseras _inlärnings processen_ på följande steg:

    1. Mata in en algoritm med data genom att tillhandahålla mer information (till exempel genom att utföra funktions extrahering).
    2. Använd dessa data för att träna en modell.
    3. Testa och distribuera modellen.
    4. Använd den distribuerade modellen för att utföra en viss automatiserad förutsägande uppgift.

- **Artificiell intelligens (AI)** är en teknik som gör det möjligt för datorer att efterlikna mänsklig intelligens. Den innehåller Machine Learning. Det är viktigt att förstå relationen mellan _AI jämfört med Machine Learning jämfört med djup inlärning_: Machine Learning är ett sätt att få artificiell intelligens, vilket innebär att du kan skapa dator system och program som kan utföra uppgifter som ofta är kopplade till mänsklig intelligens, som t. ex. visuell uppfattning, tal igenkänning, besluts fattande och översättning mellan språk.

## <a name="comparison-of-deep-learning-vs-machine-learning"></a>Jämförelse av djup inlärning eller Machine Learning

Nu när du har en översikt över Machine Learning eller djup inlärning kan du jämföra de två teknikerna. När det gäller Machine Learning måste algoritmen tillfrågas om hur man gör en korrekt förutsägelse genom att tillhandahålla mer information. i händelse av djup inlärning kan algoritmen lära sig genom sin egen data bearbetning tack vare den artificiella neurala nätverks strukturen.

I tabellen nedan jämförs de två metoderna i detalj:

| |Alla Machine Learning |Endast djup inlärning|
|---|---|---|
|  **Antal data punkter** | Kan använda små data mängder från användare | Kräver en stor mängd tränings data för att göra kortfattade slut satser |
|  **Maskin varu beroenden** | Kan arbeta på datorer med låg slut. Det behövs ingen stor mängd beräknings kraft | Är beroende av avancerade datorer. Det finns ett stort antal åtgärder för mat ris multiplikation. De här åtgärderna kan optimeras effektivt med en GPU |
|  **Funktionalisering process** | Kräver att funktioner identifieras korrekt och skapas av användare | Den lär sig mer om hög nivå funktioner från data och skapar nya funktioner själva |
|  **Inlärnings metod** | Delar upp uppgifter i små delar och kombinera sedan mottagna resultat till en slutsats | Löser problemet från slut punkt till slut punkt |
|  **Körnings tid** | Jämförelsen tar ofta mindre tid att träna, från några sekunder till några timmar | Tar en ovanligt lång tid att träna, eftersom det finns många parametrar i en djup inlärnings algoritm |
|  **Resultat** | Utdata är vanligt vis ett numeriskt värde, till exempel en poäng eller en klassificering | Utdata kan vara allt från en poäng, en text, ett element eller ett ljud |

## <a name="deep-learning-use-cases-what-problems-does-it-solve"></a>Användnings fall för djup inlärning: Vilka problem löser det?

På grund av den artificiella neurala nätverks strukturen, är djup inlärningen perfekt vid identifiering av mönster i ostrukturerade data, till exempel bilder, ljud, video och text. Av den anledningen är det snabbt att omvandla många branscher, inklusive hälso vård, energi, FinTech, transport och andra, för att kunna tänka på traditionella affärs processer. Några av de vanligaste programmen för djup inlärning beskrivs i följande stycken.

### <a name="named-entity-recognition"></a>Identifiering av namngivna enheter

En användning av djup inlärnings nätverk har namnet-enhets igenkänning, vilket är ett sätt att extrahera från ostrukturerade data, till exempel information som personer, platser, företag eller saker. Informationen kan sedan lagras i ett strukturerat schema för att bygga en lista över adresser eller fungera som ett riktmärke för en identitets validerings motor.

### <a name="object-detection"></a>Objektidentifiering

Djup inlärning har tillämpats i många olika användnings fall för objekt identifiering. Objekt identifiering är i själva verket en process i två delar: bild klassificering och avbildnings lokalisering. Bild klassificeringen avgör vad objekten i bilden är, t. ex. en bil eller en person, medan bild lokalisering tillhandahåller den speciella platsen för dessa objekt. Objekt identifiering används redan i spel, detaljist-, turist-, turist-och bil bilars branscher.

### <a name="image-caption-generation"></a>Generering av bild text

På samma sätt som bild igenkännings aktiviteten, är bild under texter aktiviteten där, för en bild, måste systemet generera en bildtext som beskriver innehållet i bilden. När du kan identifiera objekt i fotografier och generera etiketter för dessa objekt kan du se att nästa steg är att omvandla dessa etiketter till en sammanhängande menings beskrivning. I allmänhet omfattar systemen användningen av mycket stora (convolutional neurala-nätverk för objekt identifiering i fotografierna och sedan ett omaktuellt neurala-nätverk (RNN) för att omvandla etiketterna till en enhetlig mening.

### <a name="machine-translation"></a>Maskinöversättning

Maskin översättningen tar ord, fraser eller meningar från ett språk och automatiskt översätter dem till ett annat språk. Automatisk maskin översättning har varit runt under en längre tid, men djup inlärningen uppnår det främsta resultatet i två olika områden: automatisk översättning av text (och tal till text) och automatisk översättning av bilder. Med rätt data omvandling kan ett djup nätverk förstå text-, ljud-och visuella signaler. Maskin översättning kan användas för att identifiera kodfragment av ljud i större ljudfiler och skriva över det talade ordet eller bilden som text.

### <a name="text-analytics"></a>Textanalys

En viktig uppgift som djup inlärning kan utföra är e-identifiering. Företag använder djup inlärnings-baserade text analyser för Insider handels identifiering och myndighets efterlevnad. Riskminimeringsfonder-fonder använder text analys för att öka detalj nivån i enorma dokument databaser för att få insikter om framtida investerings prestanda och marknads sentiment. Användnings fallet för djup inlärning av text analys kretsar runt dess förmåga att parsa enorma mängder text data för att utföra analys-eller avkastnings agg regeringar.

## <a name="what-are-artificial-neural-networks"></a>Vad är artificiell neurala-nätverk?

Ett artificiellt neurala-nätverk är en djup inlärnings modell som inspireras av det sätt biologiska neurala-nätverken i den mänskliga hjärna-processens information. Det finns flera typer av artificiella neurala-nätverk som används för närvarande. De mest populära artificiella neurala-nätverks-typologina beskrivs nedan.

### <a name="feedforward-neural-network"></a>Feedforward neurala-nätverk

Feedforward neurala Network är den mest grundläggande typen av artificiellt neurala-nätverk, där informationen endast färdas i en riktning från indatanivå till output-lagret. Feedforward neurala Networks transformerar inmatningar genom att placera det genom en serie med dolda lager. Varje lager består av en uppsättning neurons, där varje lager är fullständigt anslutet till alla neurons i lagret. Slutligen finns det ett senast fullständigt anslutet lager – utmatnings skiktet, som representerar förutsägelserna som genereras.

### <a name="recurrent-neural-network"></a>Aktuellt neurala-nätverk

Befintliga neurala-nätverk är en mer mycket Använd typ av artificiellt neurala-nätverk, som fungerar på principen att spara utdata från ett lager och mata in det på inmatnings lagret för att förutsäga resultatet av lagret. Dessa neurala-nätverk har större inlärnings möjligheter och används ofta för mer komplexa uppgifter, till exempel inlärning av hand skrift eller språk igenkänning.

### <a name="convolutional-neural-networks"></a>(Convolutional neurala-nätverk

Ett (convolutional neurala-nätverk är en särskilt effektiv typ av artificiellt neurala-nätverk, vilket innebär en unik arkitektur. Först ordnas lagren i tre dimensioner: bredd, höjd och djup. Dessutom ansluter neurons i ett lager inte till alla neurons i nästa lager, men endast till en liten region. Slutligen kommer den slutliga utmatningen att minskas till en enda vektor av sannolikhets poäng, ordnat längs djup dimensionen. Dessa neurala-nätverk har använts i områden som bild igenkänning och klassificering.

## <a name="next-steps"></a>Nästa steg

I följande artiklar visas hur du använder djup inlärnings teknik i [Azure Machine learnings tjänsten](/azure/machine-learning/service/):

- [Klassificera handskrivna siffror med en TensorFlow-modell](how-to-train-tensorflow.md)
- [Klassificera handskrivna siffror med en TensorFlow-uppskattning och keras](how-to-train-keras.md)
- [Klassificera bilder med en Pytorch-modell](how-to-train-pytorch.md)
- [Klassificera handskrivna siffror med en kedje modell](how-to-train-pytorch.md)

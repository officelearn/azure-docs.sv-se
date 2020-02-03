---
title: 'Designer: klassificering av bok gransknings exempel'
titleSuffix: Azure Machine Learning
description: Bygg en logistik Regressions klassificerare i multiklass för att förutse företags kategorin med Wikipedia SP 500-datauppsättning med Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 4d22fd39eae5d5cf207d6d44819f0ce7ab2eceb5
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963249"
---
# <a name="build-a-classifier-to-predict-company-category-using-azure-machine-learning-designer"></a>Bygg en klassificerare för att förutse företags kategori med Azure Machine Learning designer.

**Designer (för hands version) exempel 7**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Det här exemplet visar hur du använder text analys moduler för att bygga en pipeline för text klassificering i Azure Machine Learning designer (för hands version).

Målet med text klassificering är att tilldela vissa text delar till en eller flera fördefinierade klasser eller kategorier. Texten kan vara ett dokument, nyhets artikel, Sök fråga, e-post, Tweet, support biljetter, kundfeedback, användar produkt granskning osv. Program med text klassificering inkluderar kategorisering av tidnings artiklar och nyhets brev innehåll i ämnen, organisering av webb sidor i hierarkiska kategorier, filtrering av skräp post, sentiment analys, förutsägelse av användar avsikt från Sök frågor, routning Support biljetter och analys av kundfeedback. 

Den här pipelinen är en **multiklass logistik Regressions klassificerare** för att förutse företags kategorin med **Wikipedia SP 500-datauppsättning som härletts från Wikipedia**.  

De grundläggande stegen för en utbildnings maskin inlärnings modell med text data är:

1. Hämta data

1. I förväg bearbeta text data

1. Funktions teknik

   Funktionen omvandla text till den numeriska funktionen med funktionen för att extrahera funktioner, till exempel funktion-hash, extrahera n-gram-funktionen från text data.

1. Träna modellen

1. Poäng data uppsättning

1. Utvärdera modellen

Här är det sista, färdiga diagrammet för den pipeline vi kommer att arbeta med. Vi ger dig en motivering för alla moduler så att du kan fatta liknande beslut på egen hand.

[![diagram över pipelinen](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png)](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>Data

I den här pipelinen använder vi data uppsättningen **Wikipedia SP 500** . Data uppsättningen härleds från Wikipedia (https://www.wikipedia.org/) baserat på artiklarna i varje S & P 500-företag. Innan du överför till Azure Machine Learning designer bearbetades data uppsättningen enligt följande:

- Extrahera text innehåll för varje enskilt företag
- Ta bort wiki-formatering
- Ta bort icke-alfanumeriska tecken
- Konvertera all text till gemener
- Kända företags kategorier har lagts till

Det gick inte att hitta artiklar för vissa företag, så antalet poster är mindre än 500.

## <a name="pre-process-the-text-data"></a>I förväg bearbeta text data

Vi använder modulen för **Förbearbetad text** för att Förbearbeta text data, inklusive identifiera meningar, Tokenize och så vidare. Du har hittat alla alternativ som stöds i artikeln [**Förbearbeta text**](algorithm-module-reference/preprocess-text.md) . Efter bearbetning av tex-data använder vi modulen **dela data** för att slumpmässigt dela in indata så att träning-datauppsättningen innehåller 50% av de ursprungliga data och test data uppsättningen innehåller 50% av den ursprungliga informationen.

## <a name="feature-engineering"></a>Funktions teknik
I det här exemplet ska vi använda två metoder för att utföra funktions teknik.

### <a name="feature-hashing"></a>Funktions-hash
Vi använde modulen [**funktion-hash**](algorithm-module-reference/feature-hashing.md) för att konvertera den oformaterade texten för artiklarna till heltal och använde heltals värden som inmatade funktioner i modellen. 

Modulens **hashing** -modul kan användas för att konvertera text dokument med text längd till en sträng med längden på numeriska funktioner med hjälp av den 32-bitars murmurhash v3-hashing-metoden som tillhandahålls av Vowpal Wabbit-biblioteket. Syftet med att använda funktionen hash är en minskning av dimensionalitet. hashing av funktioner gör också att funktions viktningen blir snabbare vid klassificerings tiden eftersom den använder värde jämförelse för hash i stället för sträng jämförelse.

I exempel pipeline ställer vi in antalet hash-bitar till 14 och anger antalet n-g till 2. Med de här inställningarna kan hash-tabellen innehålla 2 ^ 14 poster där varje hash-funktion representerar en eller flera n-gram-funktioner och dess värde representerar förekomst frekvensen för den n-gram i text instansen. För många problem är en hash-tabell av den här storleken mer än lämplig, men i vissa fall kan mer utrymme behövas för att undvika kollisioner. Utvärdera prestanda för Machine Learning-lösningen med ett annat antal bitar. 

### <a name="extract-n-gram-feature-from-text"></a>Extrahera N-g-funktion från text

En n-gram är en sammanhängande sekvens av n-termer från en specifik följd av text. En n-gram av storlek 1 kallas unigram. en n-gram av storlek 2 är en dubbel gram. en n-gram av storlek 3 är en trigram. N-gram av större storlekar kallas ibland för värdet n, t. ex. "fyra-gram", "fem-gram" och så vidare.

Vi har använt [**funktionen extrahera N-g från text**](algorithm-module-reference/extract-n-gram-features-from-text.md)-modulen som en annan lösning för funktions teknik. I den här modulen extraheras först uppsättningen av n-gram, förutom n-gram, antalet dokument där varje n-gram visas i texten räknas (DF). I det här exemplet används TF-IDF-mått för att beräkna funktions värden. Sedan konverteras ostrukturerade text data till alfanumeriska funktions vektorer där varje funktion representerar TF-IDF av en n-gram i en text instans.

När du har konverterat text data till numeriska funktions vektorer används en **Välj kolumn** -modul för att ta bort text data från data uppsättningen. 

## <a name="train-the-model"></a>Träna modellen

Valet av algoritm beror ofta på kraven i användnings fallet. Eftersom målet för den här pipelinen är att förutse företags kategorin är en klassificerings modell med flera klasser ett bra val. Med tanke på att antalet funktioner är stort och dessa funktioner är glesa använder vi **multiklass logistik Regressions** modell för den här pipelinen.

## <a name="test-evaluate-and-compare"></a>Testa, utvärdera och jämför

 Vi delar data uppsättningen och använder olika data uppsättningar för att träna och testa modellen för att göra utvärderingen av modellen mer mål.

När modellen har tränats använder vi **Poäng modellen** och **utvärderar modell** moduler för att generera förutsägande resultat och utvärdera modellerna. Men innan du använder modulen **Poäng modell** kan du utföra funktions konstruktioner som det som vi har gjort under utbildningen. 

För modulen för **funktions-hash** är det enkelt att utföra funktions teknikern på Poäng flöde som utbildnings flöde. Använd modulen för modul- **hash** direkt för att bearbeta inmatnings text data.

För att **extrahera N-g-funktionen från** en-modul skulle vi kunna ansluta **resultatet** av den här ord listan från inlärnings data flödet till **Indatamängden** i resultat data flödet och ange parametern för **ord listans läge** som **skrivskyddad**.
[![diagram över poängen i n-gram](./media/how-to-designer-sample-text-classification/n-gram.png)](./media/how-to-designer-sample-text-classification/n-gram.png)

När du har slutfört konstruktions steget kan **Poäng modellen** användas för att generera förutsägelser för test data uppsättningen med hjälp av den tränade modellen. Kontrol lera resultatet genom att välja utdataporten för **Poäng modellen** och sedan välja **visualisera**.

Vi skickar sedan poängen till modulen **utvärdera modell** för att generera utvärderings mått. **Utvärdera modellen** har två indataportar, så att vi kan utvärdera och jämföra poäng data uppsättningar som genereras med olika metoder. I det här exemplet jämför vi prestanda för resultatet som genererats med funktionen hashing och metoden n-gram.
Kontrol lera resultatet genom att välja utdataporten för **utvärdera modellen** och sedan välja **visualisera**.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för designern:
- [Exempel 1 – regression: förutsäga ett bils pris](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exempel 2-regression: jämför algoritmer för bil förutsägelse av bilar](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exempel 3 – klassificering med funktions val: inkomst förutsägelse](how-to-designer-sample-classification-predict-income.md)
- [Exempel 4 – klassificering: förutsägelse kredit risk (kostnads känsligt)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exempel 5 – klassificering: förutsägelse omsättning](how-to-designer-sample-classification-churn.md)
- [Exempel 6 – klassificering: förutsäga flyg fördröjningar](how-to-designer-sample-classification-flight-delay.md)

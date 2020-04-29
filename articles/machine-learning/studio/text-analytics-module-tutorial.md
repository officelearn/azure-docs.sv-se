---
title: Användnings fall för sentiment-analys
titleSuffix: ML Studio (classic) - Azure
description: Så här skapar du text analys modeller i Azure Machine Learning Studio (klassisk) med hjälp av moduler för förbehandling av text, N-g-eller funktions-hash
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 89fc5196977f53e040e1a6553b46ca57f39b18b4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79217887"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio-classic"></a>Skapa en sentiment analys modell i Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Du kan använda Azure Machine Learning Studio (klassisk) för att bygga och operationalisera-modeller för text analys. Dessa modeller kan hjälpa dig att lösa, till exempel dokument klassificering eller sentiment analys problem.

I ett text analys experiment skulle du normalt:

1. Rensa och Förbearbeta text data uppsättning
2. Extrahera numeriska funktions vektorer från förbehandlad text
3. Träna klassificering eller Regressions modell
4. Poäng och validera modellen
5. Distribuera modellen till produktion

I den här självstudien får du lära dig de här stegen när vi går igenom en sentiment analys modell med hjälp av Amazon Book Review-datauppsättning (se detta forsknings dokument "Biographies, Bollywood, sådär-rutor och-blandade": domän anpassning för sentiment klassificering "av John Blitzer, mark Dredze och Fernando Pereira; Associering av beräknings språk (ACL), 2007.) Den här data uppsättningen består av gransknings resultat (1-2 eller 4-5) och en text med fritext. Målet är att förutsäga gransknings poängen: låg (1-2) eller hög (4-5).

Du kan hitta experiment som beskrivs i den här självstudien på Azure AI Gallery:

[Förutsägelse granskning av bok](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Förutsägelse granskning av bok – förutsägande experiment](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Steg 1: Rensa och Förbearbeta text data uppsättning
Vi påbörjar experimentet genom att dela upp gransknings resultatet i kategoriska låga och höga buckets för att formulera problemet som en klassificering i två klasser. Vi använder modulerna [Redigera metadata](https://msdn.microsoft.com/library/azure/dn905986.aspx) och [grupp kategoriska-värden](https://msdn.microsoft.com/library/azure/dn906014.aspx) .

![Skapa etikett](./media/text-analytics-module-tutorial/create-label.png)

Sedan rensar vi texten med hjälp av modulen för [Förbearbetad text](https://msdn.microsoft.com/library/azure/mt762915.aspx) . Rengöringen minskar bruset i data uppsättningen, hjälper dig att hitta de viktigaste funktionerna och förbättra den slutliga modellens exakthet. Vi tar bort stoppord-vanliga ord som "a" eller "a"-och siffror, specialtecken, duplicerade tecken, e-postadresser och URL: er. Vi konverterar även texten till gemener, lemmatize orden och identifierar menings gränser som sedan anges av symbolen "| | |" i förbehandlad text.

![Förbearbeta text](./media/text-analytics-module-tutorial/preprocess-text.png)

Vad händer om du vill använda en anpassad lista med stoppord? Du kan skicka den som valfri indatamängd. Du kan också använda det reguljära uttrycket i C#-syntaxen för att ersätta del strängar och ta bort ord från en del av tal: substantiv, verb eller adjektiv.

När förbearbetningen är klar delar vi upp data i träna-och test uppsättningar.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Steg 2: extrahera numeriska funktions vektorer från Förbearbetad text
Om du vill skapa en modell för text data behöver du vanligt vis konvertera fritext text till numeriska funktions vektorer. I det här exemplet använder vi [extrahera N-gram-funktioner från text](https://msdn.microsoft.com/library/azure/mt762916.aspx) -modulen för att transformera text data till sådant format. Den här modulen tar en kolumn med blank stegs-separerade ord och beräknar en ord lista med ord eller N-gram ord som visas i din data uppsättning. Sedan räknar det hur många gånger varje ord eller N-gram visas i varje post och skapar funktions vektorer från dessa antal. I den här självstudien ställer vi in N-gram-storlek på 2, så våra funktions vektorer innehåller enkla ord och kombinationer av två efterföljande ord.

![Extrahera N-gram](./media/text-analytics-module-tutorial/extract-ngrams.png)

Vi använder TF * IDF (frekvens för omvänd dokument frekvens) viktning till antalet N-gram. Den här metoden lägger till vikten av ord som förekommer ofta i en enskild post, men som är sällsynt över hela data uppsättningen. Andra alternativ är binär, TF och kurv vägning.

Sådana text funktioner har ofta hög dimensionalitet. Om till exempel din sökkorpus har 100 000 unika ord, skulle funktions utrymmet ha 100 000 dimensioner eller mer om N-g används. Med modulen extrahera N-gram funktioner får du en uppsättning alternativ för att minska dimensionalheten. Du kan välja att utelämna ord som är korta eller långa eller för ovanliga eller för frekventa för att få ett betydande förutsägande värde. I den här självstudien exkluderar vi N-gram som förekommer i färre än 5 poster eller mer än 80% av posterna.

Du kan också använda val av funktioner för att välja de funktioner som är mest korrelerade med ditt förutsägelse mål. Vi använder funktionen i chi2-kvadrat för att välja 1000-funktioner. Du kan visa ord listan för markerade ord eller N-gram genom att klicka på höger utdata från Extract N-gram-modulen.

Som en alternativ metod för att använda extrahera N-gram-funktioner kan du använda modulen för funktions-hashing. Observera att om [funktionen hash](https://msdn.microsoft.com/library/azure/dn906018.aspx) inte har funktioner för att välja funktioner, eller TF * IDF-vägning.

## <a name="step-3-train-classification-or-regression-model"></a>Steg 3: träna klassificering eller Regressions modell
Nu har texten omvandlats till numeriska funktions kolumner. Data uppsättningen innehåller fortfarande sträng kolumner från föregående steg, så vi använder Välj kolumner i data uppsättningen för att undanta dem.

Vi använder sedan en [logistik regression i två klasser](https://msdn.microsoft.com/library/azure/dn905994.aspx) för att förutsäga vårt mål: hög eller låg gransknings poäng. I det här läget har text analys problemet omvandlats till ett vanligt klassificerings problem. Du kan använda verktygen som finns i Azure Machine Learning Studio (klassisk) för att förbättra modellen. Du kan till exempel experimentera med olika klassificerare för att ta reda på hur korrekta resultat de ger, eller Använd inställningen för att använda en inställning för att förbättra noggrannheten.

![Träna och Poäng](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Steg 4: Poäng och validera modellen
Hur validerar du den tränade modellen? Vi räknar med det mot test data uppsättningen och utvärderar noggrannheten. Modellen har dock lärt sin vokabulär i N-gram och deras vikter från data uppsättningen för träning. Därför bör vi använda denna vokabulär och dessa vikter när du extraherar funktioner från test data, i stället för att skapa en vokabulär på nytt. Därför lägger vi till utdrag i modulen för N-gram-funktioner i test delen av experimentet, ansluter den resulterande ord listan från Training-grenen och ställer in ord uppsättnings läget på skrivskyddat. Vi inaktiverar även filtreringen av N-gram med frekvens genom att ange minst 1 instans och högst 100% och inaktivera funktions valet.

När text kolumnen i test data har omvandlats till numeriska funktions kolumner utesluter vi sträng kolumnerna från föregående steg som i utbildnings grenen. Vi använder sedan modulen Poäng modell för att göra förutsägelser och utvärdera modell-modulen för att utvärdera noggrannheten.

## <a name="step-5-deploy-the-model-to-production"></a>Steg 5: Distribuera modellen till produktion
Modellen är nästan klar att distribueras till produktion. När den distribueras som webb tjänst tar det text strängen i fritext som inmatad och returnerar en förutsägelse "hög" eller "låg". Den har lärt sig den inlärda N-gram-vokabulären för att omvandla texten till funktioner och tränad logistik Regressions modell för att göra en förutsägelse från dessa funktioner. 

För att skapa ett förutsägande experiment sparar vi först N-gram-vokabulären som data uppsättning och den tränade logistik Regressions modellen från övnings grenen i experimentet. Sedan sparar vi experimentet med "Spara som" för att skapa ett experiment diagram för förutsägelse experiment. Vi tar bort modulen dela data och utbildnings grenen från experimentet. Sedan ansluter vi den tidigare sparade N-gram-vokabulären och modellen för att extrahera N-gram-funktioner och Poäng modellens moduler. Vi tar också bort modulen utvärdera modell.

Vi infogar en Välj kolumner i data uppsättnings modul innan du förbearbetar textmodulen för att ta bort kolumnen etikett och avmarkera alternativet Lägg till Poäng kolumn till data uppsättning i score-modulen. På så sätt begär webb tjänsten inte den etikett som försöker förutsäga och visar inte ingångs funktionerna som svar.

![Förutsägande experiment](./media/text-analytics-module-tutorial/predictive-text.png)

Nu har vi ett experiment som kan publiceras som en webb tjänst och anropas med hjälp av API: er för Request-respons eller batch-körning.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om text analys moduler från [MSDN-dokumentationen](https://msdn.microsoft.com/library/azure/dn905886.aspx).


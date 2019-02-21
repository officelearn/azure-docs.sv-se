---
title: Skapa en modell för analys av sentiment
titleSuffix: Azure Machine Learning Studio
description: Hur du kan skapa textanalysmodeller i Azure Machine Learning Studio med hjälp av moduler för förbearbetning av text, N-gram eller funktions-hashning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: e8e50aa49595addeab3369f74b618c31b7cf1625
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56454386"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio"></a>Skapa en sentiment analysmodell i Azure Machine Learning Studio

Du kan använda Azure Machine Learning Studio för att skapa och operationalisera modeller för textanalys. Dessa modeller kan hjälpa dig att lösa, exempelvis dokument klassificerings- eller sentiment analysis problem.

I en text analytics experiment skulle du normalt:

1. Rensa och Förbearbeta text datauppsättning
2. Extrahera numeriska funktionen vektorer från förbearbetade text
3. Träna klassificerings- eller regressionsmodell modell
4. Poängsätta och verifiera modellen
5. Distribuera modellen till produktion

I den här självstudien får du lära dig de här stegen som vi går igenom en sentiment analysmodell använder Amazon boken granskningar datauppsättning (finns i det här dokumentet för forskning ”biografier, Bollywood, sådär rutorna och blandningsföretag: Domän anpassning om segmentklassificering ”av John Blitzer markerar Dredze och Fernando Pereira; Associationen mellan databaserad lingvistik (ACL) 2007.) Den här datauppsättningen består av granska poängen (1 – 2 eller 4-5) och en egen text. Målet är att förutsäga att granska resultatet: låg (1 - 2) eller hög (4-5).

Du kan hitta experiment som beskrivs i den här självstudien i Azure AI-galleriet:

[Förutsäga boken granskningar](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Förutsäga boken granskningar - förutsägbart Experiment](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Steg 1: Rensa och Förbearbeta text datauppsättning
Vi börjar experimentet genom att dela granska poängen i kategoriska låga och höga behållare för att formulera problemet på två klassificering. Vi använder [redigera Metadata](https://msdn.microsoft.com/library/azure/dn905986.aspx) och [grupp Kategoriska värden](https://msdn.microsoft.com/library/azure/dn906014.aspx) moduler.

![Skapa etikett](./media/text-analytics-module-tutorial/create-label.png)

Sedan kan vi Rensa text med [Förbearbeta Text](https://msdn.microsoft.com/library/azure/mt762915.aspx) modulen. Rensning minskar bruset i datauppsättningen, kan du hitta de viktigaste funktionerna och förbättra den slutliga modellen. När du tar vi bort stoppord - vanliga ord som ”” eller ”a” - och siffror, specialtecken, duplicerade tecken, e-postadresser och URL: er. Vi också konvertera text till gemener, lemmatize orden och identifiera mening gränser som sedan visas som ”|||” symbolen i förväg bearbetade text.

![Förbearbeta Text](./media/text-analytics-module-tutorial/preprocess-text.png)

Vad händer om du vill använda en anpassad lista över stoppord? Du kan skicka den i som valfri inmatning. Du kan också använda anpassade C# syntax reguljärt uttryck för att ersätta delsträngar och ta bort ord som en del av tal: substantiv, verb eller adjektiv.

När den Förbearbeta är klar kan vi dela upp data i träna och testa uppsättningar.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Steg 2: Extrahera numeriska funktionen vektorer från förbearbetade text
Om du vill skapa en modell för textdata behöva du normalt konvertera Fritextfält till numeriska funktionen vektorer. I det här exemplet använder vi [N-Gram extrahera funktioner från Text](https://msdn.microsoft.com/library/azure/mt762916.aspx) modul för att omvandla textdata till sådana format. Den här modulen tar en kolumn med blanksteg kommaavgränsade ord och beräknar en ordlista med ord eller N-gram av ord, som visas i din datauppsättning. Sedan räknar den hur många gånger varje ord eller N-gram, visas i varje post och skapar funktionen vektorer från de som räknas. I den här självstudien anger vi N-gram storlek till 2, så våra funktionen vektorer inkludera enstaka ord och kombinationer av två efterföljande ord.

![Extrahera N-gram](./media/text-analytics-module-tutorial/extract-ngrams.png)

Vi tillämpar TF * IDF (termen frekvens inverterade dokumentet frekvens) fråga N-gram räknas. Den här metoden ökar vikten av ord som visas ofta i en enda post men är ovanliga i hela datamängden. Andra alternativ inkluderar binary, TF, och graph vägas mot varandra.

Sådana textfunktioner har ofta högt dimensionalitet. Till exempel om din Kristi har 100 000 unika ord, skulle funktionen-utrymme ha 100 000 dimensioner eller mer om N-gram används. Extrahera funktioner för N-Gram-modulen får du en uppsättning med alternativ för att minska dimensionaliteten. Du kan välja att exkludera ord som är kort eller long, eller för ovanliga eller för ofta har betydande värde. I de här självstudierna utesluter vi N-gram som visas i färre än 5 posterna eller i mer än 80% av posterna.

Du kan också använda val av funktioner för att välja endast de funktioner som är mest till korrelerade med mål-förutsägelse. Vi använder chi2 Funktionsurval för att välja 1000 funktioner. Du kan visa vokabulär markerade ord eller N-gram genom att klicka på rätt utdata från extrahera N-gram-modul.

Du kan använda funktions-hashning modulen som en annan metod för med hjälp av extrahera N-Gram-funktioner. Observera dock att [funktions-hashning](https://msdn.microsoft.com/library/azure/dn906018.aspx) har inte inbyggda val av funktioner eller TF * IDF vägas mot varandra.

## <a name="step-3-train-classification-or-regression-model"></a>Steg 3: Träna klassificerings- eller regressionsmodell modell
Texten har nu tagits omvandlas till funktionen för numeriska kolumner. Datauppsättningen innehåller fortfarande strängkolumner från föregående steg, så vi använder Välj kolumner i datauppsättning så de utesluts.

Sedan använder vi [Tvåklassförhöjt Logistic Regression](https://msdn.microsoft.com/library/azure/dn905994.aspx) att förutsäga våra mål: granska för hög eller låg poäng. Nu har text analytics problemet omvandlats till en vanlig klassificeringsproblem. Du kan använda verktygen i Azure Machine Learning Studio för att förbättra modellen. Du kan till exempel experimentera med olika klassificerare att ta reda på hur korrekta resultat som de ger eller använda finjustering justering för att förbättra noggrannheten.

![Träna upp och poäng](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Steg 4: Poängsätta och verifiera modellen
Hur skulle du verifierar den tränade modellen? Vi poäng mot testdatauppsättningen och utvärdera det arbete du utfört. Dock modellen lärt dig vokabulär N-gram och deras vikt från datauppsättning för träning. Vi bör därför använda den ordförråd och dessa vikter vid extrahering av funktioner från testdata, till skillnad från skapar vokabulär på nytt. Därför vi Lägg till funktioner för Extrahera N-Gram modul bedömnings-grenen av experimentet ansluta vokabulär utdata från utbildning gren och ange läget ordförråd till skrivskyddat läge. Vi också inaktivera filtrering av N-gram av frekvensen genom att ange minst 1 instans och maximalt 100 procent och Stäng av val av funktioner.

När textkolumnen i testdata har omvandlats till funktionen för numeriska kolumner, utesluter vi strängkolumner från föregående steg som i utbildning gren. Vi använder sedan modulen poängsätta modell att göra förutsägelser och utvärdera modell att utvärdera det arbete du utfört.

## <a name="step-5-deploy-the-model-to-production"></a>Steg 5: Distribuera modellen till produktion
Modellen är nästan klar att distribueras till produktion. När distribuerat som webbtjänst, den tar fri form textsträng som indata och returnerar en förutsägelse ”hög” eller ”låg”. Den inlärda N-gram ordförråd används för att omvandla text till funktioner och tränade logistic regression-modellen för att göra en förutsägelse från de här funktionerna. 

Om du vill konfigurera förutsägbart experiment spara vi först N-gram ordförråd som datauppsättning och tränade logistic regression-modellen från grenen utbildning av experimentet. Sedan kan spara vi experiment som använder ”Spara som” för att skapa ett experimentdiagram för förutsägelseexperiment. Vi kan ta bort modulen dela Data och utbildning gren från experimentet. Vi sedan ansluta tidigare sparad N-gram ordförråd och modell till extrahera N-Gram-funktioner och poängsätta modell moduler, respektive. Vi har också ta bort modulen utvärdera modell.

Vi Infoga Välj kolumner i datauppsättning modulen innan du Förbearbeta Text-modulen för att ta bort etikettkolumnen och avmarkerar alternativet ”Lägg till poäng kolumn till datauppsättningen” i poäng-modulen. På så sätt kan webbtjänsten inte begär etiketten det försöker förutse och gör inte echo indata funktioner i svaret.

![Förutsägbart Experiment](./media/text-analytics-module-tutorial/predictive-text.png)

Nu har vi ett experiment som kan publiceras som en webbtjänst och anropas med begäranden och svar eller batch-körningen API: er.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om modulerna för textanalys från [MSDN-dokumentationen](https://msdn.microsoft.com/library/azure/dn905886.aspx).


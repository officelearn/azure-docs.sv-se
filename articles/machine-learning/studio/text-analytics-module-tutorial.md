---
title: Användningsfall för sentimentanalys
titleSuffix: ML Studio (classic) - Azure
description: Så här skapar du textanalysmodeller i Azure Machine Learning Studio (klassisk) med moduler för textförbearbetning, N-gram eller funktions hashing
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 89fc5196977f53e040e1a6553b46ca57f39b18b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217887"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio-classic"></a>Skapa en sentimentanalysmodell i Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Du kan använda Azure Machine Learning Studio (klassisk) för att skapa och operationalisera textanalysmodeller. Dessa modeller kan hjälpa dig att lösa till exempel problem med dokumentklassificering eller sentimentanalys.

I ett textanalysexperiment gör du vanligtvis följande:

1. Rensa och förbehandla textdatauppsättning
2. Extrahera numeriska funktionsvektorer från förbearbetad text
3. Tågklassificering eller regressionsmodell
4. Betyg och validera modellen
5. Distribuera modellen till produktion

I den här guiden lär du dig dessa steg när vi går igenom en sentimentanalysmodell med Hjälp av Amazon Book Reviews-datauppsättning (se denna uppsats "Biografier, Bollywood, Boom-boxar och blenders: Domain Adaptation for Sentiment Classification" av John Blitzer, Mark Dredze, och Fernando Pereira; Sammanslutning av computational språkvetenskap (ACL), 2007.) Den här datauppsättningen består av granskningspoäng (1-2 eller 4-5) och en frihandstext. Målet är att förutsäga översynen poäng: låg (1-2) eller hög (4-5).

Du hittar experiment som beskrivs i den här självstudien på Azure AI Gallery:

[Förutsäga bokrecensioner](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Förutsäga bokrecensioner - Prediktivt experiment](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Steg 1: Rensa och förbehandla textdatauppsättning
Vi börjar experimentet genom att dela upp recensionspoängen i kategoriska låga och höga hinkar för att formulera problemet som tvåklassklassificering. Vi använder [moduler för redigera metadata](https://msdn.microsoft.com/library/azure/dn905986.aspx) och [gruppkategoriska värden.](https://msdn.microsoft.com/library/azure/dn906014.aspx)

![Skapa etikett](./media/text-analytics-module-tutorial/create-label.png)

Sedan rensar vi texten med [förprocesstextmodulen.](https://msdn.microsoft.com/library/azure/mt762915.aspx) Rengöringen minskar bullret i datauppsättningen, hjälper dig att hitta de viktigaste funktionerna och förbättra noggrannheten i den slutliga modellen. Vi tar bort stoppord - vanliga ord som "den" eller "a" - och siffror, specialtecken, duplicerade tecken, e-postadresser och webbadresser. Vi konverterar också texten till gemener, lemmatiserar orden och identifierar meningsgränser som sedan indikeras av symbolen |||i förbehandlad text.

![Förbearbeta text](./media/text-analytics-module-tutorial/preprocess-text.png)

Vad händer om du vill använda en anpassad lista med stopwords? Du kan skicka in den som valfri indata. Du kan också använda anpassat C#-syntaxuttryck för att ersätta delsträngar och ta bort ord med en del av tal: substantiv, verb eller adjektiv.

När förbehandlingen är klar delar vi upp data i tåg- och testuppsättningar.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Steg 2: Extrahera numeriska funktionsvektorer från förbearbetad text
Om du vill skapa en modell för textdata måste du vanligtvis konvertera friformstext till numeriska funktionsvektorer. I det här exemplet använder vi [Extrahera N-Gram-funktioner från textmodulen](https://msdn.microsoft.com/library/azure/mt762916.aspx) för att omvandla textdata till ett sådant format. Den här modulen tar en kolumn med blankstegsavgränsade ord och beräknar en ordlista, eller N-gram ord, som visas i datauppsättningen. Sedan räknas hur många gånger varje ord, eller N-gram, visas i varje post och skapar funktionsvektorer från dessa räknas. I den här guiden ställer vi in N-gram storlek till 2, så vår funktion vektorer inkluderar enstaka ord och kombinationer av två efterföljande ord.

![Extrakt N-gram](./media/text-analytics-module-tutorial/extract-ngrams.png)

Vi tillämpar TF * IDF (Term Frequency Inverse Document Frequency) viktning till N-gram räknas. Den här metoden lägger till vikten av ord som visas ofta i en enda post men som är sällsynta i hela datauppsättningen. Andra alternativ inkluderar binär, TF, och graf vägning.

Sådana textfunktioner har ofta hög dimensionalitet. Om din corpus till exempel har 100 000 unika ord, skulle ditt funktionsutrymme ha 100 000 dimensioner eller mer om N-gram används. Modulen Extrahera N-Gram-funktioner ger dig en uppsättning alternativ för att minska dimensionaliteten. Du kan välja att utesluta ord som är korta eller långa, eller för ovanliga eller för ofta för att ha betydande prediktivt värde. I den här självstudien utesluter vi N-gram som visas i färre än 5 poster eller i mer än 80 % av posterna.

Du kan också använda funktionsval för att bara välja de funktioner som är mest korrelerade med förutsägelsemålet. Vi använder Chi-Squared funktionsval för att välja 1000 funktioner. Du kan visa ordförrådet för valda ord eller N-gram genom att klicka på rätt utgång av Extrahera N-gram modul.

Som en alternativ metod för att använda Extrahera N-Gram-funktioner kan du använda funktions hash-modulen. Observera dock att [funktionen Hashing](https://msdn.microsoft.com/library/azure/dn906018.aspx) inte har inbyggd funktion val kapacitet, eller TF * IDF vägning.

## <a name="step-3-train-classification-or-regression-model"></a>Steg 3: Tågklassificering eller regressionsmodell
Nu har texten omvandlats till numeriska funktionskolumner. Datauppsättningen innehåller fortfarande strängkolumner från tidigare faser, så vi använder Välj kolumner i datauppsättning för att utesluta dem.

Vi använder sedan [tvåklassade logistiska regression](https://msdn.microsoft.com/library/azure/dn905994.aspx) för att förutsäga vårt mål: hög eller låg recension poäng. Nu har textanalysproblemet omvandlats till ett vanligt klassificeringsproblem. Du kan använda de verktyg som finns i Azure Machine Learning Studio (klassisk) för att förbättra modellen. Du kan till exempel experimentera med olika klassificerare för att ta reda på hur exakta resultat de ger, eller använda hyperparameterjustering för att förbättra noggrannheten.

![Träna och betygsätta](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Steg 4: Betyg och validera modellen
Hur skulle du validera den tränade modellen? Vi gör mål mot testdatauppsättningen och utvärderar noggrannheten. Modellen lärde sig dock ordförrådet av N-gram och deras vikter från träningsdatauppsättningen. Därför bör vi använda detta ordförråd och dessa vikter när vi extraherar funktioner från testdata, i motsats till att skapa ordförrådet på nytt. Därför lägger vi till modulen Extrahera N-Gram-funktioner i experimentets bedömningsgren, ansluter utdataordförrådet från utbildningsgrenen och ställer in ordförrådsläget till skrivskyddat. Vi inaktiverar också filtrering av N-gram efter frekvens genom att ställa in minst 1 instans och maximalt till 100%, och stänga av funktionsvalet.

När textkolumnen i testdata har omvandlats till numeriska funktionskolumner utesluter vi strängkolumnerna från tidigare faser som i utbildningsgrenen. Vi använder sedan Poängmodellmodul för att göra förutsägelser och utvärdera modellmodulen för att utvärdera noggrannheten.

## <a name="step-5-deploy-the-model-to-production"></a>Steg 5: Distribuera modellen till produktion
Modellen är nästan klar att distribueras till produktion. När den distribueras som webbtjänst krävs fritextsträng som indata och en förutsägelse "hög" eller "låg". Den använder den inlärda N-gram ordförråd för att omvandla texten till funktioner, och utbildade logistiska regression modell för att göra en förutsägelse från dessa funktioner. 

För att ställa in det prediktiva experimentet sparar vi först N-grams ordförråd som datauppsättning och den utbildade logistiska regressionsmodellen från experimentets utbildningsgren. Sedan sparar vi experimentet med "Spara som" för att skapa ett experimentdiagram för förutsägelseexperiment. Vi tar bort modulen Dela data och utbildningsgrenen från experimentet. Vi ansluter sedan den tidigare sparade N-gram ordförråd och modell till Extrahera N-Gram Funktioner och Betyg Modell moduler, respektive. Vi tar även bort modulen Utvärdera modell.

Vi infogar Välj kolumner i datauppsättningsmodulen före förprocesstextmodulen för att ta bort etikettkolumnen och avmarkera alternativet Lägg till poängkolumn till datauppsättning i poängmodulen. På så sätt begär webbtjänsten inte etiketten den försöker förutsäga och upprepar inte indatafunktionerna som svar.

![Prediktivt experiment](./media/text-analytics-module-tutorial/predictive-text.png)

Nu har vi ett experiment som kan publiceras som en webbtjänst och anropas med hjälp av API:er för begäran-svar eller batchkörning.

## <a name="next-steps"></a>Efterföljande moment
Lär dig mer om textanalysmoduler från [MSDN-dokumentation](https://msdn.microsoft.com/library/azure/dn905886.aspx).


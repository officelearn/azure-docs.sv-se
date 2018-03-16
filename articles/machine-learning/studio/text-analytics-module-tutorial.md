---
title: Skapa text analytics modeller i Azure Machine Learning Studio | Microsoft Docs
description: "Hur du skapar text analytics modeller i Azure Machine Learning Studio med moduler för text förbearbetning, N g eller hash-funktionen"
services: machine-learning
documentationcenter: 
author: rastala
manager: jhubbard
editor: 
ms.assetid: 08cd6723-3ae6-4e99-a924-e650942e461b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: roastala
ms.openlocfilehash: 0c2dc3bad6973b7b00f0ed44231e78298f4422fb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="create-text-analytics-models-in-azure-machine-learning-studio"></a>Skapa textanalysmodeller i Azure Machine Learning Studio
Du kan använda Azure Machine Learning för att skapa och använda text analytics modeller. Med hjälp av dessa modeller kan du till exempel lösa problem med dokumentet klassificering eller sentiment analys.

I en text analytics experiment skulle du normalt:

1. Rensa och Förbearbeta text dataset
2. Extrahera numeriska funktionen angreppsmetoderna från förbearbetade text
3. Klassificerings- eller regressionsmodell träningsmodell
4. Poängsätta och validera modellen
5. Distribuera modellen till produktion

I kursen får du lära dig stegen som vi går igenom en sentiment analysmodell använder Amazon Book granskningar dataset (finns i det här dokumentet, research ”biografier, Bollywood, BOM rutor och blandningsföretag: domän anpassning för Sentiment klassificering” av John Blitzer Markera Dredze och Fernando Pereira; Koppling av beräkningar Linguistics (ACL) 2007.) Den här datauppsättningen består av Granska resultat (1 till 2 eller 4-5) och en valfri form text. Målet är att förutsäga granska poäng: låg (1 - 2) eller hög (4-5).

Du kan hitta experiment som beskrivs i den här självstudiekursen i Azure AI-galleriet:

[Förutsäga Book granskningar](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-1)

[Förutsäga Book granskningar - Prediktivt Experiment](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Steg 1: Rensa och Förbearbeta text dataset
Vi börjar experimentet genom att dela granska resultaten i kategoriska låg och hög behållare för att formulera problemet som tvåklass klassificering. Vi använder [redigera Metadata](https://msdn.microsoft.com/library/azure/dn905986.aspx) och [Kategoriska Gruppvärden](https://msdn.microsoft.com/library/azure/dn906014.aspx) moduler.

![Skapa etikett](./media/text-analytics-module-tutorial/create-label.png)

Sedan rensning text med [Förbearbeta Text](https://msdn.microsoft.com/library/azure/mt762915.aspx) modul. Rensning tar bort brus i datauppsättningen hjälpa dig hitta de viktigaste funktionerna och förbättra sista modellen. Vi ta bort stoppord - vanliga ord som ”” eller ”a” - och siffror, specialtecken, duplicerade tecken, e-postadresser och URL: er. Vi också konvertera text till gemener, lemmatize orden och identifiera meningen gränser som sedan visas som ”|||” symbol i förbearbetade text.

![Förbearbeta Text](./media/text-analytics-module-tutorial/preprocess-text.png)

Vad händer om du vill använda en egen lista över stoppord? Du kan överföra den i som valfria indata. Du kan också använda anpassade C#-syntax reguljärt uttryck för att ersätta delsträngar och ta bort ord som en del av tal: substantiv, verb eller adjektiv.

När den förbearbetning är klar kan vi dela data i tåg och testa uppsättningar.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Steg 2: Extrahera numeriska funktionen angreppsmetoderna från förbearbetade text
Om du vill skapa en modell för textdata vanligtvis måste du konvertera Fritextfält till numeriska funktionen angreppsmetoderna. I det här exemplet använder vi [extrahera N Gram funktioner från Text](https://msdn.microsoft.com/library/azure/mt762916.aspx) modulen att omvandla textdata till dessa format. Den här modulen tar en kolumn med avgränsade med blanksteg ord och beräknar en ordlista med ord eller N-g ord som förekommer i dina dataset. Sedan Räknar hur många gånger varje word eller N gram visas i varje post och skapar angreppsmetoderna funktionen från dem räknar. I den här självstudiekursen värdet vi N gram storleken 2, vilket våra funktionen angreppsmetoderna omfattar kombinationer av två efterföljande ord och enstaka ord.

![Extrahera N g](./media/text-analytics-module-tutorial/extract-ngrams.png)

Det använda TF * IDF (termen frekvens inverterade dokumentet frekvens) fråga N gram räknar. Den här metoden lägger till vikten av ord som ofta visas i en enskild post men är ovanliga över hela datamängden. Andra alternativ inkluderar binary TF, och ett diagram över väger.

Textfunktioner har ofta högt dimensionalitet. Till exempel om din Kristi har 100 000 unika ord, skulle funktionen-utrymme ha 100 000 dimensioner eller mer om N gram används. Modulen extrahera N Gram funktioner ger en uppsättning alternativ för att minska dimensionaliteten. Du kan välja att utesluta ord som är för kort eller lång, eller för ovanliga eller för ofta har betydande värde. I den här självstudiekursen exkludera vi N gram som visas i färre än 5 poster eller mer än 80% av poster.

Du kan också använda val av funktioner för att välja endast de funktioner som är mest korrelerade med mål-förutsägelse. Vi använder chi2 Funktionsurval för att välja 1000 funktioner. Du kan visa ordförråd för markerade ord eller N gram genom att klicka på Extrahera N-g modulen rätt utdata.

Du kan använda funktions-hashning modulen som en annan metod för Extrahera N Gram funktioner. Observera dock att [funktions-hashning](https://msdn.microsoft.com/library/azure/dn906018.aspx) har inte inbyggda val av funktioner eller TF * IDF väger.

## <a name="step-3-train-classification-or-regression-model"></a>Steg 3: Klassificerings- eller regressionsmodell träningsmodell
Texten har nu tagits omvandlas till funktionen för numeriska kolumner. Dataset innehåller fortfarande sträng kolumner från föregående steg så att vi använda Välj kolumner i datauppsättning för att utesluta dem.

Vi använder sedan [Two-Class Logistic Regression](https://msdn.microsoft.com/library/azure/dn905994.aspx) att förutsäga målet: granska för hög eller låg poäng. Text analytics problemet har nu tagits omvandlas till en vanlig klassificeringsproblem. Du kan använda verktyg som finns i Azure Machine Learning för att förbättra modellen. Du kan till exempel experimentera med olika klassificerare ta reda på hur korrekta resultat som de ger eller använda hyperparameter för att förbättra.

![Tåg och poäng](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Steg 4: Poängsätta och validera modellen
Hur vill du validera den tränade modellen? Vi poängsätta mot testdatauppsättningen och utvärdera noggrannhet. Dock modellen lärt dig ordförråd för N g och deras vikt i utbildning DataSet. Därför ska användas som ordförråd och dessa vikter när funktioner från testdata i stället för att skapa ordlistan på nytt. Därför vi lägga till extrahera N Gram funktioner modul på den bedömningsprofil grenen av experimentet ansluta ordförråd för utdata från utbildning gren och ange läget ordförråd som skrivskyddad. Vi också inaktivera filtrering på N-g av frekvensen genom att ange minst 1 förekomst och maximalt 100% och inaktivera funktionen markeringen.

När textkolumnen i testdata har transformerats till funktionen för numeriska kolumner, exkludera vi sträng kolumner från föregående steg som i utbildning grenen. Vi använder sedan modulen poängsätta modell att göra förutsägelser och utvärdera modell att utvärdera noggrannhet.

## <a name="step-5-deploy-the-model-to-production"></a>Steg 5: Distribuera modellen till produktion
Modellen är nästan klar att distribueras till produktionen. När du distribuerade som webbtjänsten den tar Fritextfält sträng som inmatning och returnera en förutsägelse ”hög” eller ”låg”. Inlärda N gram ordförråd används för att omvandla texten som ska funktioner och tränad regressionsmodell logistic att göra en förutsägelse från dessa funktioner. 

Om du vill konfigurera prediktivt experiment spara vi först N gram ordförråd som dataset och utbildade logistic regressionsmodellen från grenen utbildning för experimentet. Vi spara experiment med ”Spara som” för att skapa ett experiment diagram för prediktivt experiment. Vi kan ta bort modulen dela Data och grenen utbildning från experimentet. Vi ansluter sedan tidigare sparad N gram ordförråd och modell till extrahera N Gram funktioner och Poängmodell moduler respektive. Vi har också ta bort modulen utvärdera modell.

Vi Infoga Välj kolumner i datauppsättning modulen innan Förbearbeta Text-modulen för att ta bort etikettkolumnen och avmarkerar alternativet ”Lägg till resultatkolumnen DataSet” i modulen poängsätta. På så sätt kan webbtjänsten inte begär etiketten ska förutsäga och har inte echo indata funktioner i svaret.

![Prediktivt Experiment](./media/text-analytics-module-tutorial/predictive-text.png)

Nu har vi ett experiment som kan publiceras som en webbtjänst och anropas med begäran och svar eller batch execution API: er.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om text analytics moduler från [MSDN-dokumentationen](https://msdn.microsoft.com/library/azure/dn905886.aspx).


---
title: 'Självstudie 2: träna kredit risk modeller'
titleSuffix: ML Studio (classic) - Azure
description: En detaljerad självstudie som visar hur du skapar en förutsägelse analys för kredit riskbedömning i Azure Machine Learning Studio (klassisk). Den här självstudien är del två i en självstudieserie i tre delar. Den visar hur du tränar och utvärderar modeller.
keywords: kreditrisk, lösning för förutsägelseanalys, riskbedömning
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 9aeaed6565e69409bc55a71985296b2393226ba4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427492"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio-classic"></a>Självstudie 2: träna kredit risk modeller – Azure Machine Learning Studio (klassisk)

I den här självstudien tittar vi närmare på hur du utvecklar en lösning för förutsägelseanalys. Du utvecklar en enkel modell i Machine Learning Studio (klassisk).  Därefter distribuerar du modellen som en Azure Machine Learning-webbtjänst.  Den här distribuerade modellen kan göra förutsägelser med nya data. Självstudien är **del två i en självstudieserie i tre delar**.

Anta att du behöver förutsäga kreditrisken för en person baserat på den information som han eller hon fyller i på en kreditansökan.  

Kreditriskbedömning är ett komplext problem, men den här självstudien kommer att förenkla processen. Du kommer att använda det som ett exempel på hur du kan skapa en förutsägelse analys lösning med hjälp av Microsoft Azure Machine Learning Studio (klassisk). Du använder Azure Machine Learning Studio (klassisk) och en Machine Learning webb tjänst för den här lösningen.  

I den här självstudien i tre delar börjar du med offentligt tillgängliga kreditriskdata.  Därefter utvecklar du och tränar en förutsägelsemodell.  Slutligen distribuerar du modellen som en webbtjänst.

I [del ett av självstudien](tutorial-part1-credit-risk.md)har du skapat en Machine Learning Studio (klassisk)-arbets yta, överfört data och skapat ett experiment.

I den här delen av självstudien ska du:
 
> [!div class="checklist"]
> * Träna flera modeller
> * Poängsätta och utvärdera modellerna


I [del tre av självstudien](tutorial-part3-credit-risk-deploy.md) ska du distribuera modellen som en webbtjänst.

## <a name="prerequisites"></a>Krav

Slutför [del ett av självstudien](tutorial-part1-credit-risk.md).

## <a name="train"></a>Träna flera modeller

En av fördelarna med att använda Azure Machine Learning Studio (klassisk) för att skapa maskin inlärnings modeller är möjligheten att testa mer än en typ av modell i taget i ett enda experiment och jämföra resultatet. Den här typen av experimentering hjälper dig att hitta den bästa lösningen för ditt problem.

I experimentet som vi utvecklar i den här självstudien ska du skapa två olika typer av modeller och sedan jämföra deras bedömningsresultat för att avgöra vilken algoritm som du vill använda i vårt slutliga experiment.  

Det finns olika modeller som du kan välja mellan. Du visar de tillgängliga modellerna genom att expandera noden **Machine Learning** på modulpaletten och sedan **Initialize Model** (Initiera modell) och noderna under den. I det här experimentet väljer du den [dubbelriktade SVM-datorn (support Vector Machine][two-class-support-vector-machine] ) och de [två klasserna i besluts trädet][two-class-boosted-decision-tree] .

> [!TIP]
> För att få hjälp att avgöra vilken Machine Learning algoritm som bäst motsvarar det specifika problem som du försöker lösa, se [så här väljer du algoritmer för Microsoft Azure Machine Learning Studio (klassisk)](algorithm-choice.md).
> 
> 

Du kommer att lägga till både modulen för [besluts träd med två klasser][two-class-boosted-decision-tree] och modulen [stöd för dubbelriktad Vector][two-class-support-vector-machine] i det här experimentet.

### <a name="two-class-boosted-decision-tree"></a>Tvåklassigt förbättrat beslutsträd

Först konfigurerar du modellen för det förbättrade beslutsträdet.

1. Leta upp modulen [besluts träd med två klasser][two-class-boosted-decision-tree] i modulen modul och dra den till arbets ytan.

1. Hitta modulen [träna modell][train-model] , dra den till arbets ytan och Anslut sedan utdatan från modulen för [besluts trädet med två klasser][two-class-boosted-decision-tree] till den vänstra Indataporten för modulen [träna modell][train-model] .
   
   Modulen för [besluts träd med två klasser][two-class-boosted-decision-tree] initierar den generiska modellen och [träna modellen][train-model] använder tränings data för att träna modellen. 

1. Anslut det vänstra resultatet av den vänstra [R-skriptkommando][execute-r-script] till den högra Indataporten för modulen [träna modell][train-model] (i den här självstudien [använde du data från vänster sida](#train) i modulen dela data för utbildning).
   
   > [!TIP]
   > du behöver inte två indata och en av utdata från modulen [Kör R-skript][execute-r-script] för det här experimentet, så att du kan lämna dem frånkopplade. 
   > 
   > 

Nu ser den här delen av experimentet ut ungefär så här:  

![Träna en modell](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

Nu måste du berätta för modulen [träna modell][train-model] att du vill att modellen ska förutsäga kredit risk svärdet.

1. Välj modulen [träna modell][train-model] . Klicka på **Starta kolumnväljaren** i fönstret **Egenskaper**.

1. I dialogrutan **Select a single column** (Välj en enskild kolumn) skriver du ”credit risk” (kreditrisk) i sökfältet under **Tillgängliga kolumner**, väljer ”Credit risk” nedan och klickar sedan på högerpilen ( **>** ) för att flytta ”Credit risk” till **Valda kolumner**. 

    ![Välj kolumnen Credit Risk för träningsmodellmodulen](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Klicka på kryssmarkeringen **OK**.

### <a name="two-class-support-vector-machine"></a>Tvåklassig dator för vektorstöd

Nu ska du ställa in SVM-modellen.  

Men först en kort beskrivning av SVM. Förbättrade beslutsträd fungerar bra med alla slags funktioner. Men eftersom SVM-modulen genererar en linjär klassificerare, har modellen som den genererar det bästa testfelet när alla numeriska funktioner har samma skala. Om du vill konvertera alla numeriska funktioner till samma skala använder du en "tanh"-omvandling (med modulen [normalisera data][normalize-data] ). På så sätt omvandlas våra tal till intervallet [0,1]. SVM-modulen konverterar strängfunktioner till kategoriska funktioner och sedan till binära 0/1-funktioner, så att du inte behöver transformera strängfunktioner manuellt. Dessutom vill du inte transformera kolumnen Credit Risk (kolumn 21) – den är numerisk, men det är det värdet som vi tränar modellen att förutsäga, så du lämnar det som det är.  

För att konfigurera SVM-modellen gör du följande:

1. Leta upp modulen [dubbelriktad Vector Machine][two-class-support-vector-machine] i modulen modul och dra den till arbets ytan.

1. Högerklicka på modulen [träna modell][train-model] , Välj **Kopiera**och högerklicka på arbets ytan och välj **Klistra in**. Kopian av modulen [träna modell][train-model] har samma kolumn val som originalet.

1. Anslut utdata från modulen med [stöd för dubbelriktad Vector][two-class-support-vector-machine] till den vänstra Indataporten för modulen för den andra modulen [modell][train-model] .

1. Hitta modulen [normalisera data][normalize-data] och dra den till arbets ytan.

1. Anslut vänster utdata från den vänstra [R-skript][execute-r-script] -modulen till indata för den här modulen (Observera att utdataporten för en modul kan vara ansluten till fler än en modul).

1. Anslut den vänstra utdataporten för modulen [normalisera data][normalize-data] till den högra Indataporten för modulen andra [träna modell][train-model] .

Den här delen av vårt experiment bör nu se ut ungefär så här:  

![Träna den andra modellen](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Konfigurera modulen [normalisera data][normalize-data] :

1. Klicka för att välja modulen [normalisera data][normalize-data] . I fönstret **Egenskaper** väljer du **Tanh** för parametern **Transformation method** (Transformeringsmetod).

1. Klicka på **Starta kolumnväljaren**, välj ”Inga kolumner” för **Börja med**, välj **Inkludera** i den första listrutan, välj **kolumntyp** i den andra listrutan och välj **Numerisk** i den tredje listrutan. Detta anger att alla numeriska kolumner (endast numeriska) transformeras.

1. Klicka på plustecknet (+) till höger om den här raden – när du gör det skapas en rad med listrutor. Välj **Exkludera** i den första listrutan, välj **kolumnnamn** i den andra listrutan och ange ”Credit Risk” (Kreditrisk) i textfältet. Detta anger att kolumnen Credit Risk (Kreditrisk) bör ignoreras (du måste göra detta eftersom den här kolumnen är numerisk och därför skulle transformeras om du inte uteslöt den).

1. Klicka på kryssmarkeringen **OK**.  

    ![Välj kolumner för modulen Normalize Data (Normalisera data)](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


[Normaliserings data][normalize-data] module har nu ställts in för att utföra en tanh-omvandling på alla numeriska kolumner förutom kredit risk kolumnen.  

## <a name="score-and-evaluate-the-models"></a>Poängsätta och utvärdera modellerna

du använder test data som har separerats av modulen [dela data][split] för att visa upp våra tränade modeller. Du kan sedan jämföra resultaten av de två modellerna för att se vilken som genererat bäst resultat.  

### <a name="add-the-score-model-modules"></a>Lägga till modulerna av typen Poängsätta modell

1. Leta upp modulen [Poäng modell][score-model] och dra den till arbets ytan.

1. Anslut modulen [träna modell][train-model] som är ansluten till modulen [besluts träd med två klasser][two-class-boosted-decision-tree] till den vänstra Indataporten för modulen [Poäng modell][score-model] .

1. Anslut höger [Kör R-skriptkommando][execute-r-script] (våra test data) till den högra Indataporten för modulen [Poäng modell][score-model] .

    ![Modulen Poängsätta modell är ansluten](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   Modulen [Poäng modell][score-model] kan nu ta kredit information från test data, köra den genom modellen och jämföra de förutsägelser som modellen genererar med kolumnen faktisk kredit risk i test data.

1. Kopiera och klistra in modulen [Poäng modell][score-model] för att skapa en andra kopia.

1. Anslut utdata från SVM-modellen (det vill säga utdataporten för modulen [träna modell][train-model] som är ansluten till den [dubbelriktade modulen för Vector-][two-class-support-vector-machine] modulen) till Indataporten för den andra [poängen modell][score-model] -modulen.

1. För SVM-modellen måste du göra samma transformering av testdata som du gjorde för träningsdata. Kopiera och klistra in modulen [normalisera data][normalize-data] för att skapa en andra kopia och koppla den till den högra [Kör R-skript][execute-r-script] -modulen.

1. Anslut vänster utdata från den andra [normaliserings data][normalize-data] -modulen till den högra Indataporten för den andra [poängen modell][score-model] -modulen.

    ![Båda Poängsätta modell-modulerna har anslutits](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>Lägga till modulen Utvärdera modell

Om du vill utvärdera de två resultat resultaten och jämföra dem använder du en [utvärdera modell][evaluate-model] -modul.  

1. Hitta modulen [utvärdera modell][evaluate-model] och dra den till arbets ytan.

1. Anslut utdataporten för den [Poäng modell][score-model] modul som är kopplad till träd modellen med högre beslut till den vänstra Indataporten för modulen [utvärdera modell][evaluate-model] .

1. Anslut den andra [poängen modell][score-model] -modulen till rätt dataport.  

    ![Modulen Utvärdera modell har anslutits](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Kör experimentet och kontrollera resultaten

Kör experimentet genom att klicka på knappen **Kör** nedanför arbetsytan. Det kan ta några minuter. En snurrande indikator på varje modul anger att den körs. Därefter visas en grön bock när modulen har slutförts. När alla moduler visas med en bockmarkering är experimentet klart.

Nu bör experimentet se ut ungefär så här:  

![Utvärdera båda modellerna](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Kontrol lera resultaten genom att klicka på utdataporten för modulen [utvärdera modell][evaluate-model] och välja **visualisera**.  

Modulen [utvärdera modell][evaluate-model] genererar ett par kurvor och mått som gör det möjligt att jämföra resultaten av de två poängen. Du kan visa resultaten som ROC-kurvor (egenskaper för mottagaroperator), kurvor av typen Precision/Återkalla eller Lyft. Ytterligare data som visas innehåller en felmatris, kumulativa värden för området under kurvan (AUC) och andra mått. Du kan ändra tröskelvärdet genom att flytta skjutreglaget åt vänster eller höger och se hur det påverkar uppsättningen mått.  

Till höger om diagrammet klickar du på **Scored dataset** (Poängsatt datauppsättning) eller **Scored dataset to compare** (Poängsatt datauppsättning att jämföra) för att markera den associerade kurvan och för att visa de associerade måtten nedan. I förklaringen för kurvorna motsvarar "returnerad data uppsättning" den vänstra Indataporten för modulen [utvärdera modell][evaluate-model] – i vårt fall är detta den utökat besluts träd modellen. ”Scored dataset to compare” (Poängsatt datauppsättning att jämföra) motsvarar den högra indataporten – SVM-modellen i vårt fall. När du klickar på någon av dessa etiketter markeras kurvan för den modellen och motsvarande mått visas, som du ser i följande bild.  

![ROC-kurvor för modeller](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Genom att undersöka dessa värden kan du avgöra vilken modell som bäst kan ge dig de resultat som du letar efter. Du kan gå tillbaka och iterera ditt experiment genom att ändra parametervärden i de olika modellerna. 

Vetenskapen och konsten att tolka resultaten och att finjustera modellens prestanda förklaras inte närmare i den här självstudien. Mer information finns i följande artiklar:
- [Utvärdera modell prestanda i Azure Machine Learning Studio (klassisk)](evaluate-model-performance.md)
- [Välj parametrar för att optimera algoritmerna i Azure Machine Learning Studio (klassisk)](algorithm-parameters-optimize.md)
- [Tolka modell resultat i Azure Machine Learning Studio (klassisk)](interpret-model-results.md)

> [!TIP]
> Varje gång du kör experimentet sparas en post över iterationen i körningshistoriken. Du kan visa dessa iterationer och återgå till någon av dem genom att klicka på **View run history** (Visa körningshistorik) nedanför arbetsytan. Du kan också klicka på **Prior Run** (Tidigare körning) i fönstret **Egenskaper** för att gå tillbaka till iterationen som direkt föregår den som är öppen.
> 
> Du kan göra en kopia av valfri iteration av experimentet genom att klicka på **Spara som** nedanför arbetsytan. 
> Använd experimentets **sammanfattning** och **beskrivning** så att du vet vad du har testat i dina iterationer av experimentet.
> 
> Mer information finns i [Hantera experiment iterationer i Azure Machine Learning Studio (klassisk)](manage-experiment-iterations.md).  
> 
> 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du slutfört dessa steg: 
 
> [!div class="checklist"]
> * Skapa ett experiment
> * Träna flera modeller
> * Poängsätta och utvärdera modellerna

Nu är du redo att distribuera modeller för dessa data.

> [!div class="nextstepaction"]
> [Självstudiekurs 3 – distribuera modeller](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
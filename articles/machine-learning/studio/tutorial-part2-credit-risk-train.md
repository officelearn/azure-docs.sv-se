---
title: 'Självstudie 2: Träna kreditriskmodeller'
titleSuffix: Azure Machine Learning Studio
description: En detaljerad självstudie om hur du skapar en förutsägelseanalys för kreditriskbedömning i Azure Machine Learning Studio. Den här självstudien är del två i en självstudieserie i tre delar. Den visar hur du tränar och utvärderar modeller.
keywords: kreditrisk, lösning för förutsägelseanalys, riskbedömning
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 8ffcfc86823d46e65e116eed86ef35fcba2a99bf
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56007329"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio"></a>Självstudie 2: Träna kreditriskmodeller – Azure Machine Learning Studio

I den här självstudien ska vi titta närmare på hur du utvecklar en lösning för förutsägelseanalys. Du ska utveckla en enkel modell i Machine Learning Studio.  Därefter ska du distribuera modellen som en Azure Machine Learning-webbtjänst.  Den här distribuerade modellen kan göra förutsägelser med nya data. Självstudien är **del två i en självstudieserie i tre delar**.

Anta att du behöver förutsäga kreditrisken för en person baserat på den information som han eller hon fyller i på en kreditansökan.  

Kreditriskbedömning är ett komplext problem, men den här självstudien ska förenkla det lite. Vi använder den som ett exempel på hur du skapar en lösning för förutsägelseanalys i Microsoft Azure Machine Learning. Du ska använda Azure Machine Learning Studio och en Machine Learning-webbtjänst för den här lösningen.  

I den här självstudien i tre delar börjar du med offentligt tillgängliga kreditriskdata.  Därefter ska du utveckla och träna en förutsägelsemodell.  Slutligen ska du distribuera modellen som en webbtjänst.

I [del ett av självstudien](tutorial-part1-credit-risk.md) skapade du en Machine Learning Studio-arbetsyta, överförde data och skapade ett experiment.

I den här delen av självstudien ska du:
 
> [!div class="checklist"]
> * Träna flera modeller
> * Betygsätta och utvärdera modellerna


I [del tre av självstudien](tutorial-part3-credit-risk-deploy.md) ska du distribuera modellen som en webbtjänst.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="prerequisites"></a>Nödvändiga komponenter

Slutför [del ett av självstudien](tutorial-part1-credit-risk.md).

## <a name="train"></a>Träna flera modeller

En av fördelarna med att använda Azure Machine Learning Studio för att skapa maskininlärningsmodeller är möjligheten att testa mer än en typ av modell i taget i ett enda experiment och jämföra resultaten. Den här typen av experimentering hjälper dig att hitta den bästa lösningen för ditt problem.

I experimentet som vi utvecklar i den här självstudien ska du skapa två olika typer av modeller och sedan jämföra deras bedömningsresultat för att avgöra vilken algoritm som du vill använda i vårt slutliga experiment.  

Det finns olika modeller som du kan välja mellan. Du visar de tillgängliga modellerna genom att expandera noden **Machine Learning** på modulpaletten och sedan **Initialize Model** (Initiera modell) och noderna under den. I det här experimentet ska du välja modulerna [Tvåklassig dator för vektorstöd][two-class-support-vector-machine] (SVM) och [Tvåklassigt förbättrat beslutsträd][two-class-boosted-decision-tree].

> [!TIP]
> Om du vill ha hjälp med att avgöra vilken maskininlärningsalgoritm som passar bäst för det specifika problem som du försöker lösa läser du [Så här väljer du algoritmer för Microsoft Azure Machine Learning Studio](algorithm-choice.md).
> 
> 

Du ska lägga till både modulen [Tvåklassigt förbättrat beslutsträd][two-class-boosted-decision-tree] och modulen [Tvåklassig dator för vektorstöd][two-class-support-vector-machine] i det här experimentet.

### <a name="two-class-boosted-decision-tree"></a>Tvåklassigt förbättrat beslutsträd

Först konfigurerar du modellen för det förbättrade beslutsträdet.

1. Leta upp modulen [Tvåklassigt förbättrat beslutsträd][two-class-boosted-decision-tree] på modulpaletten och dra den till arbetsytan.

1. Leta upp [träningsmodellmodulen][train-model], dra den till arbetsytan och anslut den sedan till utdata från modulen [Tvåklassigt förbättrat beslutsträd][two-class-boosted-decision-tree] till vänster indataport för [träningsmodellmodulen][train-model].
   
   Modulen [Tvåklassigt förbättrat beslutsträd][two-class-boosted-decision-tree] initierar den allmänna modellen, och [träningsmodellen][train-model] använder träningsdata för att träna modellen. 

1. Anslut den vänstra [Kör R-skript][execute-r-script]-modulens utdataport till den högra [träningsmodellmodulens][train-model] indataport (i den här självstudien [använde du data från vänster sida](#train) av modulen Dela data för träning).
   
   > [!TIP]
   > Du behöver inte två av dessa indata och en av [Kör R-skript][execute-r-script]-modulens utdata i det här experimentet, så du kan lämna dem frånkopplade. 
   > 
   > 

Nu ser den här delen av experimentet ut ungefär så här:  

![Träna en modell](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

Nu måste du meddela [träningsmodellmodulen][train-model] att du vill att modellen ska förutsäga kreditriskvärdet.

1. Välj [träningsmodellmodulen][train-model]. Klicka på **Starta kolumnväljaren** i fönstret **Egenskaper**.

1. I dialogrutan **Select a single column** (Välj en enskild kolumn) skriver du ”credit risk” (kreditrisk) i sökfältet under **Tillgängliga kolumner**, väljer ”Credit risk” nedan och klickar sedan på högerpilen (**>**) för att flytta ”Credit risk” till **Valda kolumner**. 

    ![Välj kolumnen Credit Risk för träningsmodellmodulen](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Klicka på kryssmarkeringen **OK**.

### <a name="two-class-support-vector-machine"></a>Tvåklassig dator för vektorstöd

Nu ska du ställa in SVM-modellen.  

Men först en kort beskrivning av SVM. Förbättrade beslutsträd fungerar bra med alla slags funktioner. Men eftersom SVM-modulen genererar en linjär klassificerare, har modellen som den genererar det bästa testfelet när alla numeriska funktioner har samma skala. Om du vill konvertera alla numeriska funktioner till samma skala använder du en ”Tanh”-transformation (med modulen [Normalisera data][normalize-data]). På så sätt omvandlas våra tal till intervallet [0,1]. SVM-modulen konverterar strängfunktioner till kategoriska funktioner och sedan till binära 0/1-funktioner, så att du inte behöver transformera strängfunktioner manuellt. Dessutom vill du inte transformera kolumnen Credit Risk (kolumn 21) – den är numerisk, men det är det värdet som vi tränar modellen att förutsäga, så du lämnar det som det är.  

För att konfigurera SVM-modellen gör du följande:

1. Leta upp modulen [Tvåklassig dator för stödvektor][two-class-support-vector-machine] på modulpaletten och dra den till arbetsytan.

1. Högerklicka på [träningsmodellmodulen][train-model], välj **Kopiera** och högerklicka sedan på arbetsytan och välj **Klistra in**. Kopian av [träningsmodellmodulen][train-model] har samma kolumnurval som originalet.

1. Anslut utdata från modulen [tvåklassig dator för stödvektor][two-class-support-vector-machine] till den andra [träningsmodellmodulens][train-model] vänstra indataport.

1. Leta upp modulen [Normalize Data][normalize-data] (Normalisera data) och dra den till arbetsytan.

1. Anslut vänster utdata från [Kör R-skript][execute-r-script]-modulens vänstra utdata till indata för den här modulen (observera att utdataporten för en modul kan anslutas till mer än en modul).

1. Anslut den vänstra utdataporten för modulen [Normalize Data][normalize-data] (Normalisera data) till den andra [träningsmodellmodulens][train-model] högra indataport.

Den här delen av vårt experiment bör nu se ut ungefär så här:  

![Träna den andra modellen](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Konfigurera nu modulen [Normalize Data][normalize-data] (Normalisera data):

1. Klicka för att välja modulen [Normalize Data][normalize-data] (Normalisera data). I fönstret **Egenskaper** väljer du **Tanh** för parametern **Transformation method** (Transformeringsmetod).

1. Klicka på **Starta kolumnväljaren**, välj ”Inga kolumner” för **Börja med**, välj **Inkludera** i den första listrutan, välj **kolumntyp** i den andra listrutan och välj **Numerisk** i den tredje listrutan. Detta anger att alla numeriska kolumner (endast numeriska) transformeras.

1. Klicka på plustecknet (+) till höger om den här raden – när du gör det skapas en rad med listrutor. Välj **Exkludera** i den första listrutan, välj **kolumnnamn** i den andra listrutan och ange ”Credit Risk” (Kreditrisk) i textfältet. Detta anger att kolumnen Credit Risk (Kreditrisk) bör ignoreras (du måste göra detta eftersom den här kolumnen är numerisk och därför skulle transformeras om du inte uteslöt den).

1. Klicka på kryssmarkeringen **OK**.  

    ![Välj kolumner för modulen Normalize Data (Normalisera data)](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


Nu är modulen [Normalize Data][normalize-data] inställd på att utföra en Tanh-transformering på alla numeriska kolumner utom för kolumnen Credit Risk (Kreditrisk).  

## <a name="score-and-evaluate-the-models"></a>Poängsätta och utvärdera modellerna

Du bedömer våra tränade modeller genom att använda de testdata som separerades ut av modulen [Dela data][split]. Du kan sedan jämföra resultaten av de två modellerna för att se vilken som genererat bäst resultat.  

### <a name="add-the-score-model-modules"></a>Lägga till modulerna av typen Poängsätta modell

1. Leta upp modulen [Poängsätta modell][score-model] och dra den till arbetsytan.

1. Anslut [träningsmodellmodulen][train-model] som är ansluten till modulen [Tvåklassigt förbättrat beslutsträd][two-class-boosted-decision-tree] till den vänstra indataporten för modulen [Poängsätta modell][score-model].

1. Anslut den högra [Kör R-skript][execute-r-script]-modulen (våra testdata) till höger indataport för modulen [Poängsätta modell][score-model].

    ![Modulen Poängsätta modell är ansluten](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   Nu kan modulen [Poängsätta modell][score-model] hämta kreditinformationen från testdata, köra den genom modellen och jämföra de förutsägelser som modellen genererar med den faktiska kreditriskkolumnen i testdata.

1. Kopiera och klistra in modulen [Poängsätta modell][score-model] för att skapa en andra kopia.

1. Anslut utdataporten för SVM-modellen (det vill säga utdataporten för [träningsmodellmodulen][train-model] som är ansluten till modulen [Tvåklassig dator för stödvektor][two-class-support-vector-machine]) till indataporten för den andra [Poängsätta modell][score-model]-modulen.

1. För SVM-modellen måste du göra samma transformering av testdata som du gjorde för träningsdata. Så, du kopierar och klistrar in modulen [Normalize Data][normalize-data] (Normalisera data) för att skapa en andra kopia och ansluter den till den högra [Kör R-skript][execute-r-script]-modulen.

1. Anslut vänster utdata för den andra [Normalize Data][normalize-data]-modulen (Normalisera data) till höger indataport för den andra [Poängsätta modell][score-model]-modulen.

    ![Båda Poängsätta modell-modulerna har anslutits](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>Lägga till modulen Utvärdera modell

För att utvärdera de två bedömningsresultaten och jämföra dem använder du modulen [Utvärdera modell][evaluate-model].  

1. Leta upp modulen [Utvärdera modell][evaluate-model] och dra den till arbetsytan.

1. Anslut utdataporten för modulen [Poängsätta modell][score-model] som är associerad med modellen för det förbättrade beslutsträdet till den vänstra indataporten för modulen [Utvärdera modell][evaluate-model].

1. Anslut den andra [Poängsätta modell][score-model]-modulen till den högra indataporten.  

    ![Modulen Utvärdera modell har anslutits](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Kör experimentet och kontrollera resultaten

Kör experimentet genom att klicka på knappen **Kör** nedanför arbetsytan. Det kan ta några minuter. En snurrande indikator på varje modul anger att den körs. Därefter visas en grön bock när modulen har slutförts. När alla moduler visas med en bockmarkering är experimentet klart.

Nu bör experimentet se ut ungefär så här:  

![Utvärdera båda modellerna](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Kontrollera resultaten genom att klicka på utdataporten för modulen [Utvärdera modell][evaluate-model] och välj **Visualisera**.  

Modulen [Utvärdera modell][evaluate-model] genererar ett par kurvor och mått som gör det möjligt att jämföra resultaten av de två poängsatta modellerna. Du kan visa resultaten som ROC-kurvor (egenskaper för mottagaroperator), kurvor av typen Precision/Återkalla eller Lyft. Ytterligare data som visas innehåller en felmatris, kumulativa värden för området under kurvan (AUC) och andra mått. Du kan ändra tröskelvärdet genom att flytta skjutreglaget åt vänster eller höger och se hur det påverkar uppsättningen mått.  

Till höger om diagrammet klickar du på **Scored dataset** (Poängsatt datauppsättning) eller **Scored dataset to compare** (Poängsatt datauppsättning att jämföra) för att markera den associerade kurvan och för att visa de associerade måtten nedan. I förklaringen för kurvorna motsvarar ”Scored dataset” (Poängsatt datauppsättning) den vänstra indataporten för modulen [Utvärdera modell][evaluate-model] – i vårt fall är detta modellen för det förbättrade beslutsträdet. ”Scored dataset to compare” (Poängsatt datauppsättning att jämföra) motsvarar den högra indataporten – SVM-modellen i vårt fall. När du klickar på någon av dessa etiketter markeras kurvan för den modellen och motsvarande mått visas, som du ser i följande bild.  

![ROC-kurvor för modeller](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Genom att undersöka dessa värden kan du avgöra vilken modell som bäst kan ge dig de resultat som du letar efter. Du kan gå tillbaka och iterera ditt experiment genom att ändra parametervärden i de olika modellerna. 

Vetenskapen och konsten att tolka resultaten och att finjustera modellens prestanda förklaras inte närmare i den här självstudien. Mer information finns i följande artiklar:
- [Så här utvärderar du modellens prestanda i Azure Machine Learning](evaluate-model-performance.md)
- [Välja parametrar för att optimera algoritmerna i Azure Machine Learning](algorithm-parameters-optimize.md)
- [Tolka modellens resultat i Azure Machine Learning](interpret-model-results.md)

> [!TIP]
> Varje gång du kör experimentet sparas en post över iterationen i körningshistoriken. Du kan visa dessa iterationer och återgå till någon av dem genom att klicka på **View run history** (Visa körningshistorik) nedanför arbetsytan. Du kan också klicka på **Prior Run** (Tidigare körning) i fönstret **Egenskaper** för att gå tillbaka till iterationen som direkt föregår den som är öppen.
> 
> Du kan göra en kopia av valfri iteration av experimentet genom att klicka på **Spara som** nedanför arbetsytan. 
> Använd experimentets **sammanfattning** och **beskrivning** så att du vet vad du har testat i dina iterationer av experimentet.
> 
> Mer information finns i [Hantera iterationer av experiment i Azure Machine Learning Studio](manage-experiment-iterations.md).  
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
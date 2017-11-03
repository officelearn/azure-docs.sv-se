---
title: "Steg 4: Träna och utvärdera de analytiska förutsägelsemodeller | Microsoft Docs"
description: "Steg 4 i utveckla en förutsägelselösning genomgång: tåg och poängsätta och utvärdera flera modeller i Azure Machine Learning Studio."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: d905f6b3-9201-4117-b769-5f9ed5ee1cac
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
ms.openlocfilehash: 5a545b9f9f7f935be91fed743779ebd6b4d930a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models"></a>Genomgång steg 4: Utbilda i och utvärdera förutsägbara analytiska modeller
Det här avsnittet innehåller det fjärde steget i den här genomgången [utveckla en förutsägelseanalys i Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Skapa en Machine Learning-arbetsyta](walkthrough-1-create-ml-workspace.md)
2. [Överför befintliga data](walkthrough-2-upload-data.md)
3. [Skapa ett nytt experiment](walkthrough-3-create-new-experiment.md)
4. **Träna och utvärdera modellerna**
5. [Distribuera webbtjänsten](walkthrough-5-publish-web-service.md)
6. [Få åtkomst till webbtjänsten](walkthrough-6-access-web-service.md)

- - -
En av fördelarna med att använda Azure Machine Learning Studio för att skapa machine learning-modeller är möjligheten att testa fler än en typ av modellen samtidigt i en enda experiment och jämför resultaten. Den här typen av experiment hjälper dig att hitta den bästa lösningen för ditt problem.

I experimentet Vi utvecklar i den här genomgången, vi skapa två olika typer av modeller och sedan jämföra bedömningsprofil resultaten för att avgöra vilken algoritm som vi vill använda i vår slutliga experimentet.  

Det finns olika modeller som vi kan välja från. För att se de tillgängliga modellerna, expandera den **Maskininlärning** nod på modulpaletten, och expandera sedan **initiera modell** och noderna under den. Vid tillämpningen av experimentet vi välja den [Two-Class Support Vector Machine] [ two-class-support-vector-machine] (SVM) och [två-Tvåklassförhöjt beslutsträd] [ two-class-boosted-decision-tree] moduler.    

> [!TIP]
> För att få hjälp för att bestämma vilken Machine Learning-algoritm som bäst passar viss problemet du försöker lösa, se [så väljer du algoritmer för Microsoft Azure Machine Learning](algorithm-choice.md).
> 
> 

## <a name="train-the-models"></a>Träna modeller

Vi lägger till både den [två-Tvåklassförhöjt beslutsträd] [ two-class-boosted-decision-tree] modulen och [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modul i experimentet.

### <a name="two-class-boosted-decision-tree"></a>Two-Class Tvåklassförhöjda beslutsträdet

Först ska vi ställa in förstärkta trädet modellen.

1. Hitta de [två-Tvåklassförhöjt beslutsträd] [ two-class-boosted-decision-tree] modulen på modulpaletten och drar den till arbetsytan.

2. Hitta de [Träningsmodell] [ train-model] modulen, drar den till arbetsytan och ansluter sedan utdata från den [två-Tvåklassförhöjt beslutsträd] [ two-class-boosted-decision-tree] modulen till vänster inkommande port för den [Träningsmodell] [ train-model] modul.
   
   Den [två-Tvåklassförhöjt beslutsträd] [ two-class-boosted-decision-tree] modulen initierar allmän modell och [träna modell] [ train-model] använder utbildningsdata för att träna på modell. 

3. Ansluta vänstra utdata från vänster [köra R-skriptet] [ execute-r-script] modulen till höger inkommande port för den [Träningsmodell] [ train-model] modul (vi valt i [Steg3](walkthrough-3-create-new-experiment.md) i den här genomgången för att använda data från vänster sida av modulen dela Data för utbildning).
   
   > [!TIP]
   > Vi behöver inte två indata och en av utdata för den [köra R-skriptet] [ execute-r-script] modul för experimentet, så vi kan lämna dem. 
   > 
   > 

Den här delen av experimentet nu ser ut ungefär så här:  

![En modell][1]

Nu behöver vi berätta det [Träningsmodell] [ train-model] modul som vi vill modellen för att förutsäga kreditrisken för värdet.

1. Välj den [Träningsmodell] [ train-model] modul. I den **egenskaper** rutan klickar du på **starta kolumnväljaren**.

2. I den **Markera en kolumn** dialogrutan Skriv ”kredit risk” i sökfältet under **tillgängliga kolumner**, markerar ”kredit risk” nedan och klicka på högerpilen ( **>** ) att flytta ”kreditrisk” till **valda kolumner**. 

    ![Välj kolumnen kreditrisken för träningsmodellmodulen][0]

3. Klicka på den **OK** är markerat.

### <a name="two-class-support-vector-machine"></a>Tvåklassig dator för vektorstöd

Nu ska ställa vi in SVM modellen.  

Första, en förklaring om SVM. Förstärkta beslutsträd fungerar bra med funktioner för alla typer. Men eftersom modulen SVM genererar en linjär klassificerare, har den modell som genereras bästa testfel när alla numeriska funktioner har samma skala. Om du vill konvertera alla numeriska funktioner samma skala vi använder en ”Tanh”-omvandling (med den [normalisera Data] [ normalize-data] module). Detta omvandlar våra nummer i intervallet [0,1]. Modulen SVM konverterar sträng funktioner till kategoriska funktioner och sedan till binära 0-1-funktioner, så vi inte behöver manuellt Omforma strängen funktioner. Dessutom vill vi inte transformera kreditrisk kolumnen (kolumn 21) - är det numeriska men det är det värde som vi tränar modellen för att förutsäga, så vi behöver inte använder den.  

Om du vill konfigurera SVM modellen gör du följande:

1. Hitta de [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modulen på modulpaletten och drar den till arbetsytan.

2. Högerklicka på den [Träningsmodell] [ train-model] modulen, Välj **kopiera**, och högerklicka sedan på arbetsytan och välj **klistra in**. Kopia av den [Träningsmodell] [ train-model] modulen har samma kolumn urval som originalet.

3. Ansluta utdata från den [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modulen till vänster inkommande port för andra [Träningsmodell] [ train-model] modul.

4. Hitta de [normalisera Data] [ normalize-data] modulen och drar den till arbetsytan.

5. Ansluta vänstra utdata från vänster [köra R-skriptet] [ execute-r-script] modulen till indata för den här modulen (Observera att utdataporten för en modul kan anslutas till mer än en modul).

6. Anslut den vänstra utdataporten för den [normalisera Data] [ normalize-data] modulen till höger inkommande port för andra [Träningsmodell] [ train-model] modul.

Den här delen av vår experimentet bör nu se ut ungefär så här:  

![Andra modell][2]  

Konfigurera nu den [normalisera Data] [ normalize-data] modulen:

1. Markera den [normalisera Data] [ normalize-data] modul. I den **egenskaper** väljer **Tanh** för den **omvandling metoden** parameter.

2. Klicka på **starta kolumnväljaren**, Välj ”inga kolumner” för **börjar med**väljer **inkludera** i den första listrutan väljer **kolumntypen** i den andra listrutan och välj **numeriska** i tredje listrutan. Anger att alla numeriska kolumner (och endast numeriskt) omvandlas.

3. Klicka på plustecknet (+) till höger om den här raden - detta skapar en rad av nedrullningsbara listorna. Välj **undanta** i den första listrutan väljer **kolumnnamn** i andra listrutan och ange ”kredit risk” i textfältet. Detta anger att kolumnen kreditrisk ska ignoreras (vi behöver göra detta eftersom den här kolumnen är numeriska och så skulle omvandlas om vi inte utesluter den).

4. Klicka på den **OK** är markerat.  

    ![Välj kolumner för modulen normalisera Data][5]

Den [normalisera Data] [ normalize-data] modulen nu är inställd på att utföra en Tanh omvandling på alla numeriska kolumner utom den kreditrisk.  

## <a name="score-and-evaluate-the-models"></a>Poängsätta och utvärdera modellerna

Vi använder informationen tester som separeras av den [dela Data] [ split] modulen poängsätta våra tränade modeller. Vi kan sedan jämföra resultatet av de två modellerna Se som genererats bättre resultat.  

### <a name="add-the-score-model-modules"></a>Lägga till poängsätta modell-moduler

1. Hitta de [Poängmodell] [ score-model] modulen och drar den till arbetsytan.

2. Ansluta den [Träningsmodell] [ train-model] modul som är ansluten till den [två-Tvåklassförhöjt beslutsträd] [ two-class-boosted-decision-tree] modulen till vänster inkommande port för den [Poängmodell] [ score-model] modul.

3. Ansluta till höger [köra R-skriptet] [ execute-r-script] modul (våra tester data) till höger inkommande port för den [Poängmodell] [ score-model] modul.

    ![Ansluten att modulen poängsätta modell][6]
   
   Den [Poängmodell] [ score-model] modul kan nu ta kredit information från tester data, kör via modellen och jämföra förutsägelser modellen genererar med den faktiska kredit risk kolumnen i den testa data.

4. Kopiera och klistra in den [Poängmodell] [ score-model] modul för att skapa en andra kopia.

5. Ansluta utdata från SVM modellen (det vill säga utdataporten för den [Träningsmodell] [ train-model] modul som är ansluten till den [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modulen) till den inkommande porten för andra [Poängmodell] [ score-model] modul.

6. Vi har utför samma transformeringen till testdata som vi gjorde för utbildning-data för SVM-modellen. Så kopiera och klistra in den [normalisera Data] [ normalize-data] modul för att skapa en andra kopia och ansluta till höger [köra R-skriptet] [ execute-r-script] modul.

7. Ansluta vänstra utdata från andra [normalisera Data] [ normalize-data] modulen till höger inkommande port för andra [Poängmodell] [ score-model] modul.

    ![Båda poängsätta modell-moduler som är ansluten][7]

### <a name="add-the-evaluate-model-module"></a>Lägg till modulen utvärdera modell

Om du vill utvärdera bedömningsprofil resultaten och jämför dem, använder vi en [utvärdera modell] [ evaluate-model] modul.  

1. Hitta de [utvärdera modell] [ evaluate-model] modulen och drar den till arbetsytan.

2. Ansluta utdataporten för den [Poängmodell] [ score-model] modul som är associerade med den förstärkta träd modellen till den vänstra indataporten av den [utvärdera modell] [ evaluate-model] modul.

3. Anslut den andra [Poängmodell] [ score-model] modulen till höger inkommande port.  

    ![Utvärdera modellen modulen ansluten][8]

### <a name="run-the-experiment-and-check-the-results"></a>Kör experimentet och kontrollera resultaten

Kör experimentet genom att klicka på den **kör** knapp under arbetsytan. Det kan ta några minuter. En snurrande indikator för varje modul visar att den körs, och sedan en grön bock visas när modulen är klar. När alla moduler är markerat har experimentet slutförts.

Experimentet bör nu se ut ungefär så här:  

![Utvärdering av båda modellerna][3]

Kontrollera resultaten, klicka på utdataporten för den [utvärdera modell] [ evaluate-model] modulen och välj **visualisera**.  

Den [utvärdera modell] [ evaluate-model] modulen genererar ett par med kurvor och mått som gör det möjligt att jämföra resultatet av de två poängsatta modellerna. Du kan visa resultat som mottagaren operatorn egenskap (ROC) kurvor, Precision/återkalla kurvor eller Lift kurvor. Ytterligare data som visas innehåller en förvirring matris kumulativa värden för området under kurvan (AUC) och andra mått. Du kan ändra tröskelvärdet genom att flytta skjutreglaget åt vänster eller höger och se hur den påverkar uppsättningen mått.  

Klicka till höger om diagrammet **bedömas dataset** eller **bedömas dataset för att jämföra** Markera associerade kurvan och visa den associerade måtten nedan. I förklaringen för kurvorna ”bedömas dataset” motsvarar den vänstra indataporten av den [utvärdera modell] [ evaluate-model] modul - i vårt fall är detta förstärkta trädet modellen. ”Bedömas dataset för att jämföra” motsvarar den högra indataporten - SVM modellen i vårt fall. När du klickar på en av etiketterna kurvan för den modellen som är markerad och motsvarande mått visas som visas i följande bild.  

![ROC kurvor för modeller][4]

Du kan välja vilken modell som ligger närmast ger dig det resultat som du letar efter genom att undersöka dessa värden. Du kan gå tillbaka och iterera experimentet genom att ändra parametervärden i olika modeller. 

Vetenskap och bilder av tolka resultaten och justera prestanda för modellen ligger utanför omfånget för den här genomgången. För ytterligare hjälp kan du läsa följande artiklar:
- [Hur du utvärdera modellen prestanda i Azure Machine Learning](evaluate-model-performance.md)
- [Välj parametrar för att optimera algoritmerna i Azure Machine Learning](algorithm-parameters-optimize.md)
- [Tolka modellen resultaten i Azure Machine Learning](interpret-model-results.md)

> [!TIP]
> Varje gång du kör experimentet en post på den iterationen sparas i historiken kör. Du kan visa dessa iterationer och återgå till någon av dem, genom att klicka på **visa KÖRNINGSHISTORIK** under arbetsytan. Du kan också klicka på **tidigare kör** i den **egenskaper** rutan Gå tillbaka till iteration omedelbart före den som du har öppnat.
> 
> Du kan göra en kopia av eventuella iterationer av experimentet genom att klicka på **Spara som** under arbetsytan. 
> Använda arbetsytan för experimentet **sammanfattning** och **beskrivning** egenskaper för att hålla reda på vad du har gjort i iterationer av experiment.
> 
> Mer information finns i [hantera iterationer av experiment i Azure Machine Learning Studio](manage-experiment-iterations.md).  
> 
> 

- - -
**Nästa: [distribuera webbtjänsten](walkthrough-5-publish-web-service.md)**

[0]: ./media/walkthrough-4-train-and-evaluate-models/train-model-select-column.png
[1]: ./media/walkthrough-4-train-and-evaluate-models/experiment-with-train-model.png
[2]: ./media/walkthrough-4-train-and-evaluate-models/svm-model-added.png
[3]: ./media/walkthrough-4-train-and-evaluate-models/final-experiment.png
[4]: ./media/walkthrough-4-train-and-evaluate-models/roc-curves.png
[5]: ./media/walkthrough-4-train-and-evaluate-models/normalize-data-select-column.png
[6]: ./media/walkthrough-4-train-and-evaluate-models/score-model-connected.png
[7]: ./media/walkthrough-4-train-and-evaluate-models/both-score-models-added.png
[8]: ./media/walkthrough-4-train-and-evaluate-models/evaluate-model-added.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

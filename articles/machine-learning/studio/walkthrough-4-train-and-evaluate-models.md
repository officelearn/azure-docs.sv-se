---
title: 'Steg 4: Träna och utvärdera förutsägbara analytiska modeller | Microsoft Docs'
description: 'Steg 4 i utveckla en förutsägelselösning genomgång: Train, bedöma och utvärdera flera modeller i Azure Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: d905f6b3-9201-4117-b769-5f9ed5ee1cac
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: d0277db665259d485c5a5a06f0013f3c0d6a177c
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52261793"
---
# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models"></a>Genomgång steg 4: Utbilda i och utvärdera förutsägbara analytiska modeller
Det här avsnittet innehåller det fjärde steget i den här genomgången [utveckla en lösning för förutsägelseanalys i Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Skapa en Machine Learning-arbetsyta](walkthrough-1-create-ml-workspace.md)
2. [Överför befintliga data](walkthrough-2-upload-data.md)
3. [Skapa ett nytt experiment](walkthrough-3-create-new-experiment.md)
4. **Träna och utvärdera modellerna**
5. [Distribuera webbtjänsten](walkthrough-5-publish-web-service.md)
6. [Få åtkomst till webbtjänsten](walkthrough-6-access-web-service.md)

- - -
En av fördelarna med att använda Azure Machine Learning Studio för att skapa machine learning-modeller är möjligheten att testa mer än en typ av modell i taget i ett enda experiment och jämföra resultaten. Den här typen av experimentering hjälper dig att hitta den bästa lösningen för ditt problem.

I experimentet som vi håller på att utveckla i den här genomgången, vi skapar två olika typer av modeller och jämför deras bedömnings resultaten för att bestämma vilken algoritm som vi vill använda i vår slutliga experimentet.  

Det finns olika modeller som vi kan välja från. Om du vill se de tillgängliga modellerna, expandera den **Maskininlärning** noden på modulpaletten, och expandera sedan **initiera modell** och noderna under den. För det här experimentet väljer vi den [två-Class Support Vector Machine] [ two-class-support-vector-machine] (SVM) och [Tvåklassförhöjt beslutsträd] [ two-class-boosted-decision-tree] moduler.    

> [!TIP]
> Om du vill ha hjälp med att bestämma vilka Machine Learning-algoritm som bäst passar särskilt problematisk du försöker lösa, se [så väljer du algoritmer för Microsoft Azure Machine Learning](algorithm-choice.md).
> 
> 

## <a name="train-the-models"></a>Träna modeller

Vi lägger till både den [Tvåklassförhöjt beslutsträd] [ two-class-boosted-decision-tree] modulen och [två-Class Support Vector Machine] [ two-class-support-vector-machine] modul i det här experimentet.

### <a name="two-class-boosted-decision-tree"></a>Tvåklassförhöjt beslutsträd

Först måste vi ställer in beslutsträd trädet modellen.

1. Hitta den [Tvåklassförhöjt beslutsträd] [ two-class-boosted-decision-tree] modulen på modulpaletten och dra den till arbetsytan.

2. Hitta den [Träningsmodell] [ train-model] modulen genom att dra den till arbetsytan och Anslut utdataporten för den [Tvåklassförhöjt beslutsträd] [ two-class-boosted-decision-tree] modul till vänstra indataporten för den [Träningsmodell] [ train-model] modulen.
   
   Den [Tvåklassförhöjt beslutsträd] [ two-class-boosted-decision-tree] modulen initierar allmän modell och [träna modell] [ train-model] använder utbildningsdata för att träna den modell. 

3. Anslut vänstra utdataporten för vänster [kör R-skript] [ execute-r-script] indataporten för modulen till höger för den [Träningsmodell] [ train-model] modulen (beslutat i [Steg3](walkthrough-3-create-new-experiment.md) i den här genomgången för att använda data från vänster sida av modulen dela Data för träning).
   
   > [!TIP]
   > Vi behöver inte två av indata och en av utdata för den [kör R-skript] [ execute-r-script] -modulen för det här experimentet, så vi kan lämna dem ej anslutna. 
   > 
   > 

Den här delen av experimentet nu ser ut ungefär så här:  

![Träna en modell][1]

Nu behöver vi visa den [Träningsmodell] [ train-model] modul som vi vill modellen att förutsäga kreditrisk värdet.

1. Välj den [Träningsmodell] [ train-model] modulen. I den **egenskaper** fönstret klickar du på **starta kolumnväljaren**.

2. I den **Markera en enda kolumn** dialogrutan skriver du ”kredit risk” i sökfältet under **tillgängliga kolumner**, Välj ”kredit risk” nedan och klicka på högerpilen (**>**) att flytta ”kreditrisk” till **valda kolumner**. 

    ![Välj kolumnen kreditrisk för träningsmodellmodulen][0]

3. Klicka på den **OK** kryssmarkeringen.

### <a name="two-class-support-vector-machine"></a>Tvåklassig dator för vektorstöd

Nu ska ställa vi in SVM modellen.  

Första, en förklaring om SVM. Förbättrat beslutsträd fungerar bra med funktioner i alla typer. Men eftersom modulen SVM genererar en linjär klassificerare, har den modell som genererar det bästa testfel när alla numeriska funktioner har samma skala. Om du vill konvertera alla numeriska funktioner samma skala, använder vi en ”Tanh”-omvandling (med den [normalisera Data] [ normalize-data] modul). Detta omvandlar vår nummer i intervallet [0,1]. Modulen SVM konverterar sträng funktioner till kategoriska funktioner och sedan till binär 0/1-funktioner, så vi inte behöver manuellt transformera sträng funktioner. Dessutom vi vill inte omvandla den kreditrisk kolumnen (21) – är det numeriska, men det är det värde som vi tränar modellen för att förutsäga, så vi behöver inte använder den.  

Om du vill konfigurera SVM modellen gör du följande:

1. Hitta den [två-Class Support Vector Machine] [ two-class-support-vector-machine] modulen på modulpaletten och dra den till arbetsytan.

2. Högerklicka på den [Träningsmodell] [ train-model] modulen, väljer **kopia**, och högerklicka på arbetsytan och välj **klistra in**. Kopia av den [Träningsmodell] [ train-model] modulen har samma Kolumnurval som originalet.

3. Anslut utdataporten för den [två-Class Support Vector Machine] [ two-class-support-vector-machine] modul till vänstra indataporten för andra [Träningsmodell] [ train-model] modulen.

4. Hitta den [normalisera Data] [ normalize-data] modulen och dra den till arbetsytan.

5. Anslut vänstra utdataporten för vänster [kör R-skript] [ execute-r-script] modulen till indata för den här modulen (Observera att utdataporten för en modul kan anslutas till mer än en modul).

6. Anslut den vänstra utdataporten för den [normalisera Data] [ normalize-data] indataporten för modulen till höger för andra [Träningsmodell] [ train-model] modulen.

Den här delen av vårt experiment bör nu se ut ungefär så här:  

![Träna andra modell][2]  

Konfigurera nu den [normalisera Data] [ normalize-data] modulen:

1. Klickar du på den [normalisera Data] [ normalize-data] modulen. I den **egenskaper** väljer **Tanh** för den **omvandling metoden** parametern.

2. Klicka på **starta kolumnväljaren**, Välj ”inga kolumner” för **börjar med**väljer **inkludera** i den första listrutan väljer du **kolumntyp** i den andra listrutan och välj **numeriska** i tredje listrutan. Detta anger att alla numeriska kolumner (och endast numeriskt) omvandlas.

3. Klicka på plustecknet (+) till höger om den här raden – detta skapar en rad med listrutor. Välj **undanta** i den första listrutan Välj **kolumnnamn** i andra listrutan och ange ”kredit risk” i textfältet. Detta anger att kolumnen kreditrisk ska ignoreras (vi måste göra detta eftersom den här kolumnen är numeriska och så skulle omvandlas om vi inte utesluter den).

4. Klicka på den **OK** kryssmarkeringen.  

    ![Markera kolumner för modulen normalisera Data][5]

Den [normalisera Data] [ normalize-data] modulen nu är inställd på att utföra en Tanh omvandling på alla numeriska kolumner utom den kreditrisk.  

## <a name="score-and-evaluate-the-models"></a>Poängsätta och utvärdera modellerna

Vi använder testdata som separeras av den [dela Data] [ split] modul för att bedöma vår tränade modeller. Vi kan sedan jämföra resultaten av de två modellerna att se som genereras bättre resultat.  

### <a name="add-the-score-model-modules"></a>Lägg till modulerna som poängsätta modell

1. Hitta den [Poängmodell] [ score-model] modulen och dra den till arbetsytan.

2. Anslut den [Träningsmodell] [ train-model] modul som är ansluten till den [Tvåklassförhöjt beslutsträd] [ two-class-boosted-decision-tree] modul till vänstra indataporten för den [Poängmodell] [ score-model] modulen.

3. Ansluta höger [kör R-skript] [ execute-r-script] indataporten för modulen (vår testdata) till höger för den [Poängmodell] [ score-model] modulen.

    ![Ansluten att modulen poängsätta modell][6]
   
   Den [Poängmodell] [ score-model] modulen kan nu ta kredit informationen från testdata, kör via modellen och jämföra förutsägelser modellen genererar med den faktiska kredit risk kolumnen i den testa data.

4. Kopiera och klistra in den [Poängmodell] [ score-model] modul för att skapa en andra kopia.

5. Anslut utdataporten för SVM-modellen (det vill säga utdataporten för den [Träningsmodell] [ train-model] modul som är ansluten till den [två-Class Support Vector Machine] [ two-class-support-vector-machine] modulen) till indataporten för andra [Poängmodell] [ score-model] modulen.

6. För SVM-modellen har vi göra samma omvandlingen till testdata som vi gjorde att träningsdata. Så kopiera och klistra in den [normalisera Data] [ normalize-data] modul för att skapa en andra kopia och ansluter den till höger [kör R-skript] [ execute-r-script] modulen.

7. Anslut vänstra utdataporten för andra [normalisera Data] [ normalize-data] indataporten för modulen till höger för andra [Poängmodell] [ score-model] modulen.

    ![Båda poängsätta modell-moduler som är anslutna][7]

### <a name="add-the-evaluate-model-module"></a>Lägg till modulen utvärdera modell

För att utvärdera de två bedömnings resultaten och jämföra dem, använder vi en [utvärdera modell] [ evaluate-model] modulen.  

1. Hitta den [utvärdera modell] [ evaluate-model] modulen och dra den till arbetsytan.

2. Anslut utdataporten för den [Poängmodell] [ score-model] som är associerade med beslutsträd trädet modellen till den vänstra indataporten för modulen den [utvärdera modell] [ evaluate-model] modulen.

3. Anslut den andra [Poängmodell] [ score-model] indataporten för modulen till höger.  

    ![Utvärdera modellen moduler som är anslutna][8]

### <a name="run-the-experiment-and-check-the-results"></a>Kör experimentet och kontrollera resultaten

Kör experimentet genom att klicka på den **kör** knappen under arbetsytan. Det kan ta några minuter. En snurrande indikator på varje modul visar att den körs och sedan en grön bock visas när modulen är klar. När alla moduler med en bock, är experimentet klart.

Experimentet bör nu se ut ungefär så här:  

![Utvärdera båda modellerna][3]

Kontrollera resultaten genom att klicka på utdataporten för den [utvärdera modell] [ evaluate-model] modul och välj **visualisera**.  

Den [utvärdera modell] [ evaluate-model] modulen genererar ett par kurvor och mått som gör det möjligt att jämföra resultatet av de två poängsatta modellerna. Du kan visa resultat som mottagare operatorn egenskap (ROC) kurvor, Precision/återkallande kurvor eller Lift kurvor. Ytterligare data som visas innehåller en felmatris, kumulativa värden för området under kurvan (AUC) och andra mått. Du kan ändra tröskelvärdet genom att flytta skjutreglaget åt vänster eller höger och se hur den påverkar uppsättning mått.  

Till höger i diagrammet, klickar du på **poängsätts datauppsättning** eller **poängsätts datauppsättning för att jämföra** att markera associerade kurvan och för att visa de associera mått nedan. I förklaringen för kurvorna ”poängsätts datauppsättningen” motsvarar den vänstra indataporten för den [utvärdera modell] [ evaluate-model] modul – i vårt fall är detta beslutsträd trädet modellen. ”Poängsätts datauppsättning för att jämföra” motsvarar den högra indataporten - SVM modellen i vårt fall. När du klickar på någon av dessa etiketter kurvan för den modellen är markerad och motsvarande mått visas, enligt följande bild.  

![ROC kurvor för modeller][4]

Du kan bestämma vilken modell som är närmast ger dig det resultat som du letar efter genom att undersöka dessa värden. Du kan gå tillbaka och iterera ditt experiment genom att ändra parametervärden i de olika modellerna. 

Vetenskap och bilder av tolka resultaten och justera modellens prestanda ligger utanför omfånget för den här genomgången. Mer hjälp kan du läsa följande artiklar:
- [Hur du utvärderar du modellens prestanda i Azure Machine Learning](evaluate-model-performance.md)
- [Välj parametrar för att optimera algoritmerna i Azure Machine Learning](algorithm-parameters-optimize.md)
- [Tolka modellresultat i Azure Machine Learning](interpret-model-results.md)

> [!TIP]
> Varje gång du kör experimentet en post i den iterationen sparas i historiken kör. Du kan visa dessa iterationer och återgå till någon av dem genom att klicka på **visa KÖRNINGSHISTORIK** under arbetsytan. Du kan också klicka på **tidigare kör** i den **egenskaper** att du återgår till iteration omedelbart före den som du har öppen.
> 
> Du kan göra en kopia av eventuella iterationer av experimentet genom att klicka på **Spara som** under arbetsytan. 
> Använd experimentet **sammanfattning** och **beskrivning** egenskaper så att ha koll på vad du har gjort i din iterationer av experiment.
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

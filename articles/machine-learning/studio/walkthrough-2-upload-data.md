---
title: 'Steg 2: Ladda upp data till en Machine Learning-experiment | Microsoft Docs'
description: 'Steg 2 av utveckla en förutsägelselösning genomgång: Överför lagras offentliga data i Azure Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 9f4bc52e-9919-4dea-90ea-5cf7cc506d85
ms.service: machine-learning
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.openlocfilehash: f482b1273f83f5ae5bb4f1e64609767ee0c5fe32
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-experiment"></a>Genomgång steg 2: Överför befintliga data i ett Azure Machine Learning-experiment
Detta är det andra steget i den här genomgången [utveckla en förutsägelseanalys i Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Skapa en Machine Learning-arbetsyta](walkthrough-1-create-ml-workspace.md)
2. **Överför befintliga data**
3. [Skapa ett nytt experiment](walkthrough-3-create-new-experiment.md)
4. [Träna och utvärdera modellerna](walkthrough-4-train-and-evaluate-models.md)
5. [Distribuera webbtjänsten](walkthrough-5-publish-web-service.md)
6. [Få åtkomst till webbtjänsten](walkthrough-6-access-web-service.md)

- - -
För att utveckla en förutsägelsemodell för kreditrisk behöver vi data som vi kan använda för att träna och testa modellen. Den här genomgången använder vi ”UCI Statlog (tyska kredit Data) datauppsättningen” från UC Irvine Machine Learning-databasen. Du hittar den här:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Vi använder den fil som heter **german.data**. Hämta den här filen till den lokala hårddisken.  

Den **german.data** datamängden innehåller rader med 20 variabler för 1000 senaste ansöker om kredit. Variablerna 20 representerar den dataset uppsättning funktioner (den *funktionen vector*), vilket möjliggör identifieringsegenskaper för varje kredit sökanden. Ytterligare en kolumn i varje rad representerar sökandens beräknade kreditrisk med 700 sökanden identifieras som en låg kreditrisk och 300 som en hög risk.

UCI webbplatser som innehåller en beskrivning av attribut för funktionen vektorn för dessa data. Detta inkluderar finansiell information, kredithistorik, anställningsstatus och personlig information. För varje ansökan har en binär klassificering angivna som anger om de är låg eller hög kredit risk. 

Vi använder informationen för att träna en förutsägelseanalysmodell. När det är klart ska vår modell kunna acceptera en funktionen-vektor för en ny person och förutsäga om han eller hon är en låg eller hög kreditrisk.  

Här är en intressant snodd. Beskrivning av datamängden på webbplatsen UCI nämns vad det kostar om vi misclassify kreditrisken för en person.
Om modellen beräknar en hög kreditrisken för en person som är faktiskt en låg kreditrisk, har modellen gjort en felklassificering.
Men omvänd felklassificering är fem gånger dyrare att finansinstitutet: om modellen beräknar en låg kreditrisken för en person som är faktiskt en hög kreditrisk.

Därför vill vi träna vår modell så att kostnaden för den här senare typen av felklassificering är fem gånger större än misclassifying på andra sätt.
Det är ett enkelt sätt att göra detta när träna modellen i vårt experiment genom att duplicera (fem gånger) transaktioner som representerar någon med en hög kreditrisk. Sedan om modellen felaktigt någon som en låg kreditrisk klassificerar när de är faktiskt en hög risk, har modellen den samma felklassificering fem gånger, en gång för varje kopia. Detta ökar kostnaden för det här felet i utbildning resultaten.


## <a name="convert-the-dataset-format"></a>Konvertera dataset-format
Den ursprungliga datauppsättningen används ett tomt kommaavgränsade format. Machine Learning Studio fungerar bättre med en fil med kommaavgränsade värden (CSV), så att det kommer att konvertera datauppsättningen genom att ersätta blanksteg med kommatecken.  

Det finns många sätt att omvandla data. Ett sätt är med hjälp av följande Windows PowerShell-kommando:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Ett annat sätt är med hjälp av kommandot sed Unix:  

    sed 's/ /,/g' german.data > german.csv  

I båda fallen kan vi har skapat en CSV-version av data i en fil med namnet **german.csv** som vi kan använda i vårt experiment.

## <a name="upload-the-dataset-to-machine-learning-studio"></a>Ladda upp datauppsättningen till Machine Learning Studio
När data har konverterats till CSV-format, som vi behöver överföra den till Machine Learning Studio. 

1. Öppna startsidan för Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net)). 

2. Klicka på menyn ![menyn][1] i det övre vänstra hörnet i fönstret klickar du på **Azure Machine Learning**väljer **Studio**, och logga in.

3. Klicka på **+ ny** längst ned i fönstret.

4. Välj **DATASET**.

5. Välj **från en lokal fil**.

    ![Lägg till en datamängd från en lokal fil][2]

6. I den **ladda upp en ny datamängd** dialogrutan klickar du på **Bläddra** och Sök efter den **german.csv** fil som du skapade.

7. Ange ett namn för datamängden. Anropa den ”UCI tyska kreditkortdata” i den här genomgången.

8. Datatypen, Välj **generiska CSV-filen utan rubrik (. nh.csv)**.

9. Lägg till en beskrivning om du vill ha.

10. Klicka på den **OK** är markerat.  

    ![Ladda upp datauppsättningen][3]

Detta överför data i en dataset-modul som vi kan använda i ett experiment.

Du kan hantera datauppsättningar som du har överfört till Studio genom att klicka på den **DATAUPPSÄTTNINGAR** fliken till vänster i fönstret Studio.

![Hantera datamängder][4]

Mer information om hur du importerar andra typer av data i ett experiment finns [importera utbildningsdata till Azure Machine Learning Studio](import-data.md).

**Nästa: [skapa ett nytt experiment](walkthrough-3-create-new-experiment.md)**

[1]: media/walkthrough-2-upload-data/menu.png
[2]: media/walkthrough-2-upload-data/add-dataset.png
[3]: media/walkthrough-2-upload-data/upload-dataset.png
[4]: media/walkthrough-2-upload-data/dataset-list.png

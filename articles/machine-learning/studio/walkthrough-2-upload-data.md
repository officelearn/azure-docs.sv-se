---
Rubrik: ”Steg 2: Ladda upp data till en Machine Learning Studio-experiment' titleSuffix: Beskrivning av Azure Machine Learning Studio: ”Steg 2 av utveckla en förutsägelselösning genomgång: Ladda upp lagras offentliga data till Azure Machine Learning Studio ”.
tjänster: machine learning ms.service: machine learning ms.component: studio ms.topic: artikel

Författare: garyericson ms.author: garye ms.custom: föregående författare = heatherbshapiro, tidigare ms.author=hshapiro ms.date: 03/23/2017
---
# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-studio-experiment"></a>Genomgång steg 2: Överför befintliga data i ett Azure Machine Learning Studio-experiment
Det här är det andra steget i den här genomgången [utveckla en lösning för förutsägelseanalys i Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Skapa en Machine Learning-arbetsyta](walkthrough-1-create-ml-workspace.md)
2. **Överför befintliga data**
3. [Skapa ett nytt experiment](walkthrough-3-create-new-experiment.md)
4. [Träna och utvärdera modellerna](walkthrough-4-train-and-evaluate-models.md)
5. [Distribuera webbtjänsten](walkthrough-5-publish-web-service.md)
6. [Få åtkomst till webbtjänsten](walkthrough-6-access-web-service.md)

- - -
För att utveckla en förutsägande modell för kreditrisk kan behöver vi data som vi kan använda för att träna och testa modellen. Den här genomgången använder vi ”UCI Statlog (tyska kredit Data) Data Set” från UC Irvine Machine Learning-databasen. Du kan hitta den här:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Vi använder filen **german.data**. Ladda ned den här filen till den lokala hårddisken.  

Den **german.data** datauppsättningen innehåller rader med 20 variabler för 1000 senaste sökande för kredit. Variablerna 20 representerar datauppsättningens uppsättning funktioner (den *funktionen vektor*), vilket möjliggör identifieringsegenskaper för varje kredit sökande. Ytterligare en kolumn i varje rad representerar sökandens beräknade kreditrisk med 700 sökande identifieras som ett låga kreditrisk och 300 som en hög risk.

UCI webbplatser som innehåller en beskrivning av attributen för funktionen vektor för dessa data. Detta inkluderar finansiell information, kredithistorik, anställningsstatus och personlig information. För varje sökande en binär klassificering har viss som anger om de är låg eller hög risk-kredit. 

Vi använder dessa data för att träna en modell för förutsägelseanalys. När det är klart ska vår modell kunna acceptera en funktionen vektor för en ny person och förutsäga om han eller hon är en låg eller hög kreditrisk.  

Här är en typ av intressant. Beskrivning av datauppsättningen på webbplatsen UCI nämner vad det kostar om vi misclassify kreditrisken för en person.
Om modellen förutsäger en hög kreditrisken för en person som är faktiskt en låg kreditrisk har modellen gjort en felklassificering.
Men omvänd felklassificering är fem gånger dyrare till finansinstitutet: om modellen förutsäger ett låga kreditrisken för en person som är faktiskt en hög kreditrisk.

Därför vill vi tränar vår modell så att kostnaden för den här senare typen av felklassificering är fem gånger högre än misclassifying det andra sättet.
Det är ett enkelt sätt att göra detta när träna modellen i vår experiment genom att duplicera (fem gånger) de poster som representerar någon med en hög kreditrisk. Sedan, om modellen felaktigt någon som ett låga kreditrisk klassificerar när de är faktiskt en hög risk, modellen har den samma felklassificering fem gånger, en gång för varje dubblett. Detta ökar kostnaden för det här felet i resultaten för utbildning.


## <a name="convert-the-dataset-format"></a>Konvertera formatet för datauppsättning
Den ursprungliga datauppsättningen använder en tom fil-formatet. Machine Learning Studio fungerar bättre med en fil med kommaavgränsade värden (CSV), så att det kommer att konvertera datauppsättningen genom att ersätta blanksteg med kommatecken.  

Det finns många sätt att omvandla dessa data. Ett sätt är med hjälp av följande Windows PowerShell-kommando:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Ett annat sätt är med hjälp av kommandot Unix sed:  

    sed 's/ /,/g' german.data > german.csv  

I båda fallen har vi skapat en CSV-version av data i en fil med namnet **german.csv** som vi kan använda i vår experiment.

## <a name="upload-the-dataset-to-machine-learning-studio"></a>Ladda upp datauppsättningen till Machine Learning Studio
När data har konverterats till CSV-format, som vi behöver överföra den till Machine Learning Studio. 

1. Öppna startsidan för Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net)). 

2. Klicka på menyn ![menyn][1] i det övre vänstra hörnet i fönstret klickar du på **Azure Machine Learning**väljer **Studio**, och logga in.

3. Klicka på **+ ny** längst ned i fönstret.

4. Välj **DATAUPPSÄTTNING**.

5. Välj **från lokal fil**.

    ![Lägg till en datamängd från en lokal fil][2]

6. I den **ladda upp en ny datauppsättning** dialogrutan klickar du på **Bläddra** och hitta den **german.csv** fil som du skapade.

7. Ange ett namn för datauppsättningen. Den här genomgången anropar du den till ”UCI tyska kreditkortsdata”.

8. Datatyp, Välj **generisk CSV-fil med ingen rubrik (. nh.csv)**.

9. Lägg till en beskrivning om du vill ha.

10. Klicka på den **OK** kryssmarkeringen.  

    ![Ladda upp datauppsättningen][3]

Detta överför data till en datauppsättning-modul som vi kan använda i ett experiment.

Du kan hantera datauppsättningar som du har överfört till Studio genom att klicka på den **DATAUPPSÄTTNINGAR** fliken till vänster om fönstret Studio.

![Hantera datamängder][4]

Läs mer om hur du importerar andra typer av data i ett experiment, [importera dina utbildningsdata till Azure Machine Learning Studio](import-data.md).

**Nästa: [Skapa ett nytt experiment](walkthrough-3-create-new-experiment.md)**

[1]: media/walkthrough-2-upload-data/menu.png
[2]: media/walkthrough-2-upload-data/add-dataset.png
[3]: media/walkthrough-2-upload-data/upload-dataset.png
[4]: media/walkthrough-2-upload-data/dataset-list.png

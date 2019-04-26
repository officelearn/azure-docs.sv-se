---
title: Självstudie – skapa och kör en Jupyter-anteckningsbok i Azure
description: Så här skapar du en Jupyter-anteckningsbok en körning i Azure-anteckningsböcker som visar processen för linjär regression i datavetenskap.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 65bbb5fe-9939-4e8e-8f5b-c197d4be142a
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: kraigb
ms.openlocfilehash: d5ccf3e9f35a8d35387962278577333ff92ff02b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60238288"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Självstudie: skapa och köra en Jupyter-anteckningsbok med Python

Den här självstudien vägleder dig genom processen för att skapa en fullständig Jupyter-anteckningsbok som visar enkel linjär regression med Azure-anteckningsböcker. Under den här självstudien får bekanta du dig med Jupyter-anteckningsboken användargränssnitt, vilket inkluderar skapa olika celler, kör celler och presentera anteckningsboken som ett bildspel.

Slutförda anteckningsboken kan hittas på [GitHub - exempel för Azure-anteckningsböcker](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). Den här självstudien börjar dock med ett nytt projekt och en tom anteckningsbok så får du skapa det steg för steg.

## <a name="create-the-project"></a>Skapa projektet

1. Gå till [Azure anteckningsböcker](https://notebooks.azure.com) och logga in. (Mer information finns i [Snabbstart – logga in på Azure-anteckningsböcker](quickstart-sign-in-azure-notebooks.md)).

1. Din offentliga profil-sida väljer du **Mina projekt** överst på sidan:

    ![Mina projekt länk överst i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. På den **Mina projekt** väljer **+ nytt projekt** (kortkommandot: n); knappen kan visas endast som **+** om webbläsaren är smal:

    ![Nytt projekt-kommando på sidan Mina projekt](media/quickstarts/new-project-command.png)

1. I den **Skapa nytt projekt** popup-fönstret som visas, ange eller ange följande information och välj sedan **skapa**:

    - **Projektnamnet**: Exempel på linjär Regression – Cricket Chirps
    - **Projektet ID**: linjär regression exempel
    - **Offentliga projekt**: (avmarkerad)
    - **Skapa en README.md**: (avmarkerad)

1. Efter en liten stund navigerar du till det nya projektet i Azure-anteckningsböcker.

## <a name="create-the-data-file"></a>Skapa datafilen

Den linjära regressionsmodellen som du skapar i anteckningsboken hämtar data från en fil i ditt projekt med namnet *cricket_chirps.csv*. Du kan skapa den här filen genom att kopiera den från [GitHub - exempel för Azure-anteckningsböcker](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps), eller genom att ange data direkt. I följande avsnitt beskrivs båda metoderna.

### <a name="upload-the-data-file"></a>Ladda upp filen

1. På instrumentpanelen projekt i Azure anteckningsböcker väljer **överför** > **från URL**
1. I popup-fönstret anger du följande URL i **URL: en för filen** och *cricket_chirps.csv* i **filnamn**och välj sedan **klar**.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. Den *cricket_chirps.csv* fil bör nu visas i listan över filer i ditt projekt:

    ![Nyligen skapade CSV-fil som visar i listan över filer i projektet](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Skapa en fil från grunden

1. På instrumentpanelen projekt i Azure anteckningsböcker väljer **+ ny** > **tom fil**
1. Ett fält visas i listan över filer i projektet. Ange *cricket_chirps.csv* och tryck på RETUR.
1. Högerklicka på *cricket_chirps.csv* och välj **redigera filen**.
1. I redigeringsprogrammet som visas anger du följande information:

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. Välj **spara fil** att spara filen och återgå till instrumentpanelen för projektet.

## <a name="install-project-level-packages"></a>Installera på paket i projektet

Inom en bärbar dator, kan du alltid använda kommandon som `!pip install` i en kodcell för att installera nödvändiga paket. Dessa kommandon körs varje gång du kör denna notebook kod celler och kan ta lång tid. Därför kan du i stället installera paket i projektet nivå med en `requirements.txt` fil.

1. Använda processen som beskrivs i [skapa en fil från grunden](#create-a-file-from-scratch) att skapa en fil med namnet `requirements.txt` med följande innehåll:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    Du kan också ladda upp en `requirements.txt` filen från den lokala datorn om du föredrar, enligt beskrivningen på [har laddat upp datafilen](#upload-the-data-file).

1. På instrumentpanelen för projektet väljer **Projektinställningar**.
1. I popup-fönstret som visas väljer du den **miljö** och sedan välja **+ Lägg till**.
1. I den första listruta-kontrollen (åtgärd) under **steg för konfiguration av miljö**, Välj **Requirements.txt**.
1. I den andra listruta kontrollen (filnamn), väljer *requirements.txt* (filen som du skapade).
1. I den tredje nedrullningsbar listruta (Python-version), väljer **Python Version 3.6**.
1. Välj **Spara**.

![Fliken Projekt inställningar miljö att ange en requirements.txt-fil](media/tutorial/tutorial-requirements-txt.png)

Med det här steget av installationen på plats körs alla anteckningsboken som du kör i projektet i en miljö där de paket som är installerade.

## <a name="create-and-run-a-notebook"></a>Skapa och kör en notebook-fil

Du kan nu skapa och öppna anteckningsboken med redo datafilen och projekt miljö uppsättningen.

1. På instrumentpanelen för projektet väljer **+ ny** > **Notebook**.
1. I popup-fönstret anger du *linjär Regression exempel – Cricket Chirps.ipynb* för **objektnamn**, Välj **Python 3.6** för språket, Välj **New**.
1. När denna nya notebook visas på listan, väljer du det startar den bärbara datorn. En ny webbläsarflik öppnas automatiskt.
1. Eftersom du har en *requirements.txt* filen i miljöinställningarna för visas meddelandet ”väntar på din behållare till Slutför förbereds”. Du kan välja **OK** att stänga meddelandet och fortsätta arbeta i anteckningsboken; du kan inte köra kod celler, men tills miljön är fullständigt konfigurerad.
1. Anteckningsboken öppnas i Jupyter-gränssnitt med en enda tom kodcell som standard.

    [![Inledande vy av en ny anteckningsbok i Azure-anteckningsböcker](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Tour bärbar dator-gränssnitt

Med den bärbara datorn körs, kan du lägga till kod och Markdown-celler kör cellerna och hantera anteckningsboken. Först, men det är värt att ta en stund åt att bekanta dig med gränssnittet. Fullständig dokumentation, Välj den **hjälpa** > **Notebook hjälpa** menykommandot.

Överst i fönstret kan du se följande objekt:

(A) namnet på din bärbara dator som du kan redigera genom att klicka på.
(B) knappar för att navigera till som innehåller projektet och instrumentpanelen projekt som öppnar nya flikar i webbläsaren.
(C) en meny med kommandon för att arbeta med den bärbara datorn.
(D) ett verktygsfält med genvägar för vanliga åtgärder.
(E) Redigering arbetsytan som innehåller celler.
(F) indikator för om anteckningsboken är betrodd (standardvärdet är **ej betrodd**).
(G) kernel som används för att köra anteckningsboken tillsammans med en indikator för aktiviteten.

[![Delar av Användargränssnittet för Jupyter-gränssnittet](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter innehåller en inbyggd genomgång av de primära UI-element. Starta den guidade visningen genom att välja den **hjälpa** > **användaren gränssnittet rundtur** kommando och klicka på via de popup-fönster.

Grupper av menykommandon är följande:

| Meny | Beskrivning |
| --- | --- |
| Fil | Kommandon för att hantera notebook-filen, inklusive kommandon för att skapa och kopiera notebooks, visa en förhandsgranskning och hämta anteckningsboken i olika format. |
| Redigera | Vanliga kommandon klipp ut, kopiera, klistra in celler, hitta och Ersätt värden, hantera cell bifogade filer och infoga bilder.  |
| Visa | Kommandon för att styra synligheten för olika delar av Jupyter UI. |
| Infoga | Kommandon för att infoga en ny cell ovanför eller under den aktuella cellen. Du kan använda dessa kommandon ofta när du skapar en anteckningsbok. |
| Cell | De olika **kör** kommandon körs en eller flera celler i olika kombinationer. Den **celltyp** kommandon ändra typen av en cell mellan **kod**, **Markdown**, och **Raw NBConvert** (klartext). Den **aktuella utdata** och **alla utdata** kommandona bestämmer hur utdata från kör kod visas och inkludera ett kommando för att rensa alla utdata. |
| Kernel | Kommandon för att hantera hur koden körs i kerneln, tillsammans med **ändra kernel** ändra språk eller Python-version som används för att köra anteckningsboken. |
| Data | Kommandon för att ladda upp och ladda ned filer från projektet eller session. Se [arbetar med data projektfiler](work-with-project-data-files.md) |
| widgetar | Kommandon för att hantera [Jupyter widgetar](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), som ger ytterligare funktioner för visualisering, mappa och ritning.|
| Hjälp | Kommandon som du ger hjälp och dokumentation för Jupyter-gränssnittet. |

De flesta av kommandona i verktygsfältet har motsvarande menykommandon. Ett undantag är **RETUR/redigera öka bildspel**, vilket beskrivs på [resurs- och finns anteckningsböcker](present-jupyter-notebooks-slideshow.md).

Du kan använda ett antal dessa kommandon som du fylla i anteckningsboken i avsnitten som följer.

## <a name="create-a-markdown-cell"></a>Skapa en Markdown-cell

1. Klicka på den första tomma cellen som visas på arbetsytan anteckningsboken. En cell är som standard en **kod** typ, vilket innebär att den har utformats för att innehålla att köra flödet kod för den valda kerneln (Python, R, eller F#). Den aktuella typen visas i typen listrutan i verktygsfältet:

    ![Cell listruta verktygsfältet](media/tutorial/tutorial-cell-type-drop-down.png)

1. Ändra typen till cellen **Markdown** med verktygsfältet listrutan; Alternativt kan använda den **Cell** > **celltyp**  >   **Markdown** menykommandot:

    ![Kommandot i cell typ-menyn](media/tutorial/tutorial-cell-type-menu.png)

1. Klicka på cellen för att börja redigera genom att ange sedan följande Markdown:

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](https://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. För att rendera Markdown i HTML-format för webbläsaren, Välj den **kör** kommandot i verktygsfältet, eller använda den **Cell** > **kör celler** kommando. Markdown-kod för att formatera och länkar visas nu som du förväntade dig i en webbläsare.

1. När du kör den sista cellen i anteckningsboken skapar Jupyter automatiskt en ny cell under den som du körde. Placera mer Markdown i den här cellen genom att upprepa stegen i det här avsnittet med följande Markdown:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Dubbelklicka i den renderade cellen om du vill redigera Markdown igen. Kör cellen för att rendera HTML igen när du har gjort ändringar.

## <a name="create-a-code-cell-with-commands"></a>Skapa en kodcell med kommandon

Som det beskrivs i föregående cell i Markdown, kan du inkludera kommandon direkt i anteckningsboken. Du kan använda kommandon för att installera paket genom att köra curl eller wget att hämta data eller något annat. Jupyter-anteckningsböcker köras effektivt i en Linux-dator, så att du har fullständig Linux-kommandot Ange att arbeta med.

1. Ange kommandot nedan i cellen koden som visas när du använt **kör** på föregående cell i Markdown. Om du inte ser en ny cell, skapa en med **infoga** > **Cell nedan** eller Använd den **+** i verktygsfältet.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Innan du kör cellen måste du skapa en ny cell med den **+** i verktygsfältet, ange den till Markdown och anger följande förklaring:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Välj den **Cell** > **kör alla** kommando för att köra alla celler i anteckningsboken. Observera att Markdown-celler återges som HTML och kommandot Kör i kerneln och notera kernel-indikator som beskrivs i Markdown själva:

    ![Upptagen indikator för notebook-kernel](media/tutorial/tutorial-kernel-busy.png)

1. Det tar lite tid för alla också den `pip install` kommandon som ska köras, och eftersom du redan har installerat paketen i projektet-miljö (och eftersom de är också inkluderat i Azure-anteckningsböcker som standard), många meddelanden som läser ”krav visas redan uppfyllde ”. Alla dessa utdata kan vara störande visuellt, så Välj som säljer (med ett enda klick) och sedan använda den **Cell** > **Cell utdata** > **växla**att dölja utdata. Du kan också använda den **Rensa** på samma undermenyn ta bort utdata helt.

    Den **växla** kommandot är aktiverad visas endast de senaste utdata från cellen; om du kör cellen igen utdata visas igen.

1. Eftersom paket som är installerade i projektmiljön, kommentera ut den `! pip install` kommandon med hjälp av `#`; det här sättet de ligger kvar i anteckningsboken som instruerande material, men tar inte som helst för att köra och inte producerar utdata som är onödiga. I det här fallet anger att hålla kommenterade kommandona i anteckningsboken även denna notebook beroenden.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>Skapa de återstående cellerna

För att fylla i resten av anteckningsboken skapa du sedan ett antal celler som Markdown och kod. För varje cell som anges nedan, först skapa den nya cellen, sedan ange vilket och sedan klistra in innehållet.

Du kan vänta med att köra anteckningsboken när du har skapat varje cell, är det intressant att köras varje cell som du har skapat den. Inte alla celler visa utdata; Om du inte ser några fel, förutsätter cellen körde normalt.

Varje kodcell är beroende av den kod som körs i föregående celler och om du inte att köra en av cellerna senare celler kan ge fel. Om du upptäcker att du har glömt att köra en cell, försök med den **Cell** > **kör alla ovan** innan du kör den aktiva cellen.

Om du ser oväntade resultat (som du förmodligen kommer!), kontrollerar du att varje cell är inställd på ”Code” eller ”Markdown” efter behov. Ett ”ogiltig syntax”-fel uppstår till exempel vanligtvis när du har angett Markdown i kodcellen.

1. Markdown-cellen:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Kodcell; När du kör, visar innehåll som utdata. Du kan ignorera utdata genom att kommentera ut den `print` instruktionen.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > Du kan se varningar från den här koden om ”numpy.dtype storlek ändras”. varningar kan ignoreras.

1. Markdown-cellen:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Kodcell; När du kör, har den här cellen inga utdata.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Markdown-cellen:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept`).
    ```

1. Kodcell; När du kör, den här cellen visar utdata, `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`.

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Markdown-cellen:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_` and `coef_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Kodcell; När du kör, den här cellen visar resultat som `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]`.

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Markdown-cellen:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Markdown-cellen:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Kodcell; När du kör, skapar den här cellen en grafisk rityta. Om du inte se diagram först gången (och i stället finns i ”bild storlek 640 x 480 med 1 axlar”), kör du cellen igen.

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![Diagram utdata från matplotlib-kod](media/tutorial/tutorial-plot-output.png)

1. Markdown-cellen:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Rensa utdata och kör alla celler

Du har skapat både en del av att köra kod i kontexten för en fullständig genomgång om linjär regression när du har följt stegen i föregående avsnitt för att fylla i anteckningsboken. Den här direkt kombinationen av kod och text är en av de stora fördelarna med anteckningsböcker!

Försöka köra hela anteckningsboken nu:

1. Rensa alla kernel sessionsdata och alla cell utdata genom att välja **Kernel** > **omstart och rensa utdata**. Det här kommandot är alltid en bra som ska köras när du har slutfört en bärbar dator, utan bara för att se till att du inte har skapat några onormalt beroenden mellan kod celler.

1. Kör en notebook med hjälp av **Cell** > **kör alla**. Observera kernel-indikatorn är ifyllt när koden körs.

    Om du har någon kod som körs för länge eller på annat sätt fastnar, du kan stoppa kernel genom att använda den **Kernel** > **avbryter** kommando.

1. Bläddra igenom anteckningsboken för att granska resultaten. (Om igen området inte visas kan du köra cellen.)

## <a name="save-halt-and-close-the-notebook"></a>Spara, stoppa och stänger anteckningsboken

Under den tid som du redigerar en bärbar dator, kan du spara det aktuella tillståndet med den **filen** > **spara och kontrollpunkt** kommando eller spara i verktygsfältet. En ”kontrollpunkt” skapas en ögonblicksbild som du kan återgå till när som helst under sessionen. Kontrollpunkter om du vill göra en serie experimentella ändringar och om dessa ändringar inte fungerar kan du bara kan återgå till en kontrollpunkt med hjälp av den **filen** > **återgå till kontrollpunkt** kommando. En annan metod är att skapa extra celler och kommentera ut all kod som du inte vill köra. i båda fallen fungerar.

Du kan också använda den **filen** > **kopierar** kommandot när du vill göra en kopia av det aktuella tillståndet för anteckningsboken i en ny fil i projektet. Denna kopia öppnas automatiskt i en ny webbläsarflik.

När du är klar med en bärbar dator, använder den **filen** > **Stäng och stanna** kommando som stänger anteckningsboken och stängs av kernel som har körts den. Azure anteckningsböcker stänger sedan webbläsarfliken automatiskt.

## <a name="debug-notebooks-using-visual-studio-code"></a>Felsöka anteckningsböcker som använder Visual Studio Code

Om koden cellerna i anteckningsboken inte fungerar på det sätt som du förväntar dig, kan du ha koden buggar eller andra fel. Men än att använda `print` -uttryck för att visa värdet för variabler, alla felsökning anläggningar inte omfattas av en typisk Jupyter-miljö.

Som tur är kan du kan ladda ned den bärbara datorn *.ipynb* filen och sedan öppna den i Visual Studio Code med Python-tillägg. Tillägget importerar direkt en bärbar dator som en enda kodfil bevara din Markdown-celler i kommentarerna. När du har importerat anteckningsboken, kan du använda felsökaren för Visual Studio Code för att gå igenom koden, ange brytpunkter, granska tillstånd och så vidare. När du har gjort ändringar i koden du sedan exportera den *.ipynb* från Visual Studio Code och ladda upp den tillbaka till Azure-anteckningsböcker.

Mer information finns i [felsöka en Jupyter-anteckningsbok](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) i Visual Studio Code-dokumentationen.

Se även [Visual Studio Code - stöd för Jupyter](https://code.visualstudio.com/docs/python/jupyter-support) för ytterligare funktioner i Visual Studio Code för Jupyter-anteckningsböcker.

## <a name="next-steps"></a>Nästa steg

- [Utforska exempelanteckningsböcker](azure-notebooks-samples.md)

Instruktionsartiklar:

- [Skapa och klona projekt](create-clone-jupyter-notebooks.md)
- [Konfigurera och hantera projekt](configure-manage-azure-notebooks-projects.md)
- [Installera paket inifrån en notebook-fil](install-packages-jupyter-notebook.md)
- [Presentera ett bildspel](present-jupyter-notebooks-slideshow.md)
- [Arbeta med datafiler](work-with-project-data-files.md)
- [Få åtkomst till dataresurser](access-data-resources-jupyter-notebooks.md)
- [Använda Azure Machine Learning Services](use-machine-learning-services-jupyter-notebooks.md)

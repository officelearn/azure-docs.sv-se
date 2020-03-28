---
title: Självstudiekurs - skapa och köra en Jupyter-anteckningsbok - Förhandsversionen av Azure Notebooks
description: Lär dig hur du skapar och kör en Jupyter-anteckningsbok i förhandsversionen av Azure Notebooks som demonstrerar processen med linjär regression i datavetenskap.
ms.topic: tutorial
ms.date: 01/11/2019
ms.openlocfilehash: 8a1c13f41ef1588b040b3540b852d83764c6ce79
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75660825"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Självstudiekurs: skapa och kör en Jupyter-anteckningsbok med Python

Den här självstudien går igenom processen att använda Azure Notebooks för att skapa en komplett Jupyter-anteckningsbok som visar enkel linjär regression. Under den här självstudien bekantar du dig med jupyterns anteckningsboksgränssnitt, som inkluderar att skapa olika celler, köra celler och presentera anteckningsboken som ett bildspel.

Den färdiga anteckningsboken finns på [GitHub - Azure Notebooks Samples](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). Den här självstudien börjar dock med ett nytt projekt och en tom anteckningsbok så att du kan uppleva att skapa den steg för steg.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en projektanteckningsbok med några exempeldata
> * Använda gränssnittet för bärbara datorer för att skapa en mängd olika typer av celler
> * Köra anteckningsboken
> * Spara anteckningsboken
> * Felsöka anteckningsboken i Visual Studio-kod

## <a name="create-the-project"></a>Skapa projektet

1. Gå till [Azure-anteckningsböcker](https://notebooks.azure.com) och logga in. (Mer information finns [i Snabbstart - Logga in på Azure-anteckningsböcker](quickstart-sign-in-azure-notebooks.md)).

1. På din offentliga profilsida väljer du **Mina projekt** högst upp på sidan:

    ![Länken Mina projekt visas högst upp i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. På sidan **Mina projekt** väljer du + **Nytt projekt** (kortkommando: n); knappen får bara **+** visas som om webbläsarfönstret är smalt:

    ![Nytt projektkommando på sidan Mina projekt](media/quickstarts/new-project-command.png)

1. Ange eller ange följande information **i popup-fönstret Skapa nytt projekt** som visas och välj sedan **Skapa:**

   - **Projektnamn**: Linjär regression Exempel - Cricket kvittrar
   - **Projekt-ID:** linjär-regression-exempel
   - **Offentligt projekt**: (rensat)
   - **Skapa en README.md:**(rensad)

1. Efter en stund navigerar Azure Notebooks dig till det nya projektet.

## <a name="create-the-data-file"></a>Skapa datafilen

Den linjära regressionsmodellen som du skapar i anteckningsboken hämtar data från en fil i projektet som kallas *cricket_chirps.csv*. Du kan skapa den här filen antingen genom att kopiera den från [GitHub - Azure Notebooks Samples](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)eller genom att ange data direkt. I följande avsnitt beskrivs båda tillvägagångssätten.

### <a name="upload-the-data-file"></a>Ladda upp datafilen

1. På projektinstrumentpanelen i Azure Notebooks väljer du **Ladda upp** > **från URL**
1. I popup-fönstret anger du följande URL i **Fil-URL** och *cricket_chirps.csv* i **Filnamn**och väljer sedan **Klar**.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. Filen *cricket_chirps.csv* ska nu visas i projektets fillista:

    ![Nyskapade CSV-filer som visas i projektfilslistan](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Skapa en fil från grunden

1. På projektinstrumentpanelen i Azure Notebooks väljer du **+ Ny** > **tom fil**
1. Ett fält visas i projektets fillista. Ange *cricket_chirps.csv* och tryck på Retur.
1. Högerklicka *på cricket_chirps.csv* och välj **Redigera fil**.
1. Ange följande data i redigeraren som visas:

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

1. Välj **Spara fil** om du vill spara filen och gå tillbaka till projektinstrumentpanelen.

## <a name="install-project-level-packages"></a>Installera projektnivåpaket

I en anteckningsbok kan du alltid `!pip install` använda kommandon som i en kodcell för att installera nödvändiga paket. Sådana kommandon körs dock varje gång du kör anteckningsbokens kodceller och kan ta lång tid. Därför kan du i stället installera paket på `requirements.txt` projektnivå med hjälp av en fil.

1. Använd processen som beskrivs i Skapa en `requirements.txt` fil från [grunden](#create-a-file-from-scratch) för att skapa en fil med namnet med följande innehåll:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    Du kan också `requirements.txt` ladda upp en fil från den lokala datorn om du vill, enligt beskrivningen [i Ladda upp datafilen](#upload-the-data-file).

1. Välj Projektinställningar på **instrumentpanelen**för projektet .
1. I popup-fönstret som visas väljer du fliken **Miljö** och väljer sedan **+Lägg till**.
1. I den första rullgardinsmenyn (åtgärden) under **Steg för miljöinställningar**väljer du **Requirements.txt**.
1. I den andra listrutan (filnamnet) väljer du *requirements.txt* (filen du skapade).
1. I den tredje rullgardinsmenyn (Python-versionen) väljer du **Python Version 3.6**.
1. Välj **Spara**.

![Fliken Miljö i Project Settings som anger en requirements.txt-fil](media/tutorial/tutorial-requirements-txt.png)

Med det här installationssteget på plats körs alla bärbara datorer som du kör i projektet i en miljö där dessa paket är installerade.

## <a name="create-and-run-a-notebook"></a>Skapa och kör en notebook-fil

När datafilen är klar och projektmiljön är inställd kan du nu skapa och öppna anteckningsboken.

1. Välj **+ Ny** > **anteckningsbok på**instrumentpanelen .
1. I popup-fönstret anger du *Linjärt regressionsexempel - Cricket Chirps.ipynb* för **objektnamn**, väljer **Python 3.6** för språket och väljer sedan **Nytt**.
1. När den nya anteckningsboken visas i fillistan markerar du den för att starta anteckningsboken. En ny webbläsarflik öppnas automatiskt.
1. Eftersom du har en *requirements.txt-fil* i miljöinställningarna visas meddelandet "Väntar på att behållaren ska förberedas igen". Du kan välja **OK** för att stänga meddelandet och fortsätta arbeta i anteckningsboken. Du kan dock inte köra kodceller förrän miljön har konfigurerats helt.
1. Anteckningsboken öppnas i Jupyter-gränssnittet med en enda tom kodcell som standard.

    [![Inledande vy av en ny anteckningsbok i Azure Notebooks](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Tour gränssnittet för bärbara datorer

När anteckningsboken körs kan du lägga till kod- och Markdown-celler, köra dessa celler och hantera anteckningsbokens funktion. Först är det dock värt att ta några minuter att bekanta dig med gränssnittet. Om du vill **Help** > ha fullständig dokumentation väljer du**hjälpanteckningsanteckningsmenyn** Hjälpanteckningsbok.

Längst upp i fönstret visas följande objekt:

(A) Namnet på anteckningsboken, som du kan redigera genom att klicka.
(B) Knappar för att navigera till det innehållande projektet och instrumentpanelen för projekt, som öppnar nya flikar i webbläsaren.
(C) En meny med kommandon för att arbeta med anteckningsboken.
(D) ett verktygsfält med genvägar för vanliga åtgärder.
(E) redigeringsduken som innehåller celler.
(F) indikator på om anteckningsboken är betrodd (standard är **inte betrodd**).
(G) kärnan som används för att köra anteckningsboken tillsammans med en aktivitetsindikator.

[![Primära gränssnittsområden i Jupyter-gränssnittet](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter ger en inbyggd rundtur i de primära gränssnittselementen. Starta turnén genom att välja kommandot **Hjälp** > **användargränssnittsturné** och klicka igenom popup-fönster.

Grupperna med menykommandon är följande:

| Meny | Beskrivning |
| --- | --- |
| Fil | Kommandon för att hantera anteckningsboksfilen, inklusive kommandon för att skapa och kopiera anteckningsböcker, visa en förhandsgranskning och hämta anteckningsboken i en mängd olika format. |
| Redigera | Vanliga kommandon för att klippa ut, kopiera och klistra in celler, söka efter och ersätta värden, hantera cellbilagor och infoga bilder.  |
| Visa | Kommandon för att styra synligheten för olika delar av Jupyter-användargränssnittet. |
| Infogning | Kommandon för att infoga en ny cell ovanför eller under den aktuella cellen. Du använder dessa kommandon ofta när du skapar en anteckningsbok. |
| Mobiltelefon | De olika **runkommandona** kör en eller flera celler i olika kombinationer. Kommandona **Celltyp** ändrar celltypens typ mellan **Kod**, **Markdown**och **Raw NBConvert** (oformaterad text). Kommandona **Aktuella utdata** och **alla utdata** styr hur utdata från körningskoden visas och innehåller ett kommando för att rensa alla utdata. |
| Kernel | Kommandon för att hantera hur kod körs i kärnan, tillsammans med **Ändra kärna** för att ändra språket eller Python-versionen som används för att köra anteckningsboken. |
| Data | Kommandon för att ladda upp och ladda ned filer från projektet eller sessionen. Se [Arbeta med projektdatafiler](work-with-project-data-files.md) |
| Widgets | Kommandon för att hantera [Jupyter Widgets](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), som ger ytterligare funktioner för visualisering, mappning och plottning.|
| Hjälp | Kommandon som ger hjälp och dokumentation för Jupyter-gränssnittet. |

De flesta kommandon i verktygsfältet har motsvarande menykommandon. Ett undantag är **Enter/Edit RISE Bildspel**, som diskuteras på [Dela och presentera anteckningsböcker](present-jupyter-notebooks-slideshow.md).

Du använder ett antal av dessa kommandon när du fyller i anteckningsboken i de avsnitt som följer.

## <a name="create-a-markdown-cell"></a>Skapa en Markdown-cell

1. Klicka i den första tomma cellen som visas på arbetsytan för bärbara datorer. Som standard är en cell en **kodtyp,** vilket innebär att den är utformad för att innehålla körbar kod för den valda kärnan (Python, R eller F#). Den aktuella typen visas i textrutan i verktygsfältet:

    ![Nedrullningsgrupp för celltyp](media/tutorial/tutorial-cell-type-drop-down.png)

1. Ändra celltypen till **Markdown** med hjälp av verktygsfältets listruta. alternativt använder du menykommandot **Cell** > **celltyp** > **Markdown:**

    ![Menykommando för celltyp](media/tutorial/tutorial-cell-type-menu.png)

1. Klicka i cellen för att börja redigera och ange sedan följande Markdown:

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

1. Om du vill rendera markdownen till HTML för webbläsaren markerar du kommandot **Kör** i verktygsfältet eller använder kommandot **Cell** > **Run Cells.** Markdown-koden för formatering och länkar visas nu som du förväntar dig att de ska i en webbläsare.

1. När du kör den sista cellen i anteckningsboken skapar Jupyter automatiskt en ny cell under den du körde. Placera mer Markdown i den här cellen genom att upprepa stegen i det här avsnittet med följande Markdown:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Om du vill redigera Markdown igen dubbelklickar du i den renderade cellen. Om du vill rendera HTML igen efter att du har gjort ändringar kör du cellen.

## <a name="create-a-code-cell-with-commands"></a>Skapa en kodcell med kommandon

Som den tidigare Markdown-cellen förklarade kan du inkludera kommandon direkt i anteckningsboken. Du kan använda kommandon för att installera paket, köra curl eller wget för att hämta data eller något annat. Jupyter bärbara datorer körs effektivt inom en Virtuell Linux-maskin, så du har hela Linux-kommandot inställt på att arbeta med.

1. Ange kommandona nedan i kodcellen som visades efter att du använde **Kör** på den tidigare Markdown-cellen. Om du inte ser en ny cell skapar du en **+** med **Infoga** > cell**nedan** eller använder knappen i verktygsfältet.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Innan du kör cellen ska **+** du skapa en ny cell med knappen i verktygsfältet, ställa in den på Markdown och ange följande förklaring:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Markera kommandot **Cell** > **kör alla** om du vill köra alla celler i anteckningsboken. Observera att Markdown-cellerna återges som HTML och kommandot körs i kärnan och observerar kärnindikatorn enligt beskrivningen i Markdown själv:

    ![Upptagen indikator för den bärbara kärnan](media/tutorial/tutorial-kernel-busy.png)

1. Det tar också lite tid `pip install` för alla kommandon att köras, och eftersom du redan har installerat dessa paket i projektmiljön (och eftersom de också ingår i Azure Notebooks som standard), ser du många meddelanden som lyder, "Krav redan uppfyllt." Allt detta utdata kan vara visuellt störande, så markera cellen (med ett enda klick), och använd sedan > **cellcellutdata** > **växla** för att dölja utdata. **Cell** Du kan också använda kommandot **Rensa** på samma undermeny för att ta bort utdata helt.

    Kommandot **Växla** döljer bara den senaste utdata från cellen. Om du kör cellen igen visas utdata igen.

1. Eftersom paketen är installerade i projektmiljön `! pip install` kommenterar `#`du kommandona med ; på så sätt kan de finnas kvar i anteckningsboken som instruktionsmaterial men tar inte någon tid att köra och kommer inte att producera onödiga utdata. I det här fallet anger om du behåller de kommenterade kommandona i anteckningsboken också anteckningsbokens beroenden.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>Skapa de återstående cellerna

Om du vill fylla i resten av anteckningsboken skapar du nästa serie Markdown- och kodceller. För varje cell som visas nedan skapar du först den nya cellen och anger sedan typen och klistrar sedan in innehållet.

Även om du kan vänta med att köra anteckningsboken när du har skapat varje cell, är det intressant att köra varje cell när du skapar den. Alla celler visar inte utdata. Om du inte ser några fel antar du att cellen kördes normalt.

Varje kodcell beror på vilken kod som har körts i tidigare celler, och om du försummar att köra en av cellerna kan senare celler skapa fel. Om du upptäcker att du har glömt att köra en cell kan du prova att använda **cellen** > **Kör alla ovanstående** innan du kör den aktuella cellen.

Om du ser oväntade resultat (som du förmodligen kommer!), kontrollera att varje cell är inställd på "Kod" eller "Markdown" om det behövs. Ett ogiltigt syntaxfel uppstår till exempel vanligtvis när du har angett Markdown i kodcellen.

1. Markdown cell:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Kodcell; när du kör, visar tabellinnehållet som utdata. Du kan undertrycka utdata genom `print` att kommentera utsatsen.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > Du kan se varningar från den här koden om "numpy.dtype size changed"; varningarna kan ignoreras på ett säkert sätt.

1. Markdown cell:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Kodcell; När den körs har den här cellen inga utdata.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Markdown cell:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. Kodcell; när den körs visar den `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`här cellen utdata.

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Markdown cell:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Kodcell; när den körs visar `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]`den här cellen resultat som .

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Markdown cell:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Markdown cell:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Kodcell; När den körs skapar cellen ett grafiskt ritdiagram. Om du inte ser diagrammet första gången (och i stället ser "Bildstorlek 640x480 med 1 axlar" kör du cellen igen.

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

    ![Ritutgång från matplotlib-koden](media/tutorial/tutorial-plot-output.png)

1. Markdown cell:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Rensa utdata och kör alla celler igen

När du har följt stegen i föregående avsnitt för att fylla i hela anteckningsboken har du skapat både en kod som körs i samband med en fullständig självstudiekurs om linjär regression. Denna direkta kombination av kod och text är en av de stora fördelarna med bärbara datorer!

Prova att köra hela anteckningsboken igen nu:

1. Rensa alla kärnets sessionsdata och alla cellutdata genom att välja **Kernel** > **Restart & Clear Output**. Det här kommandot är alltid bra att köra när du har slutfört en anteckningsbok, bara för att se till att du inte har skapat några konstiga beroenden mellan kodceller.

1. Kör anteckningsboken igen med **Cell** > **Run All**. Observera att kärnindikatorn är ifylld medan koden körs.

    Om du har någon kod som körs för länge eller på annat sätt fastnar kan du stoppa kärnan med kommandot **Kernel** > **Interrupt.**

1. Bläddra igenom anteckningsboken för att undersöka resultaten. (Om diagrammet inte visas igen körs cellen igen.)

## <a name="save-halt-and-close-the-notebook"></a>Spara, stoppa och stänga anteckningsboken

Under den tid du redigerar en anteckningsbok kan du spara dess aktuella tillstånd med kommandot**Spara och kontrollpunkt** **för fil** > eller spara i verktygsfältet. En "kontrollpunkt" skapar en ögonblicksbild som du kan återgå till när som helst under sessionen. Med kontrollpunkter kan du göra en serie experimentella ändringar, och om dessa ändringar inte **File** > fungerar kan du bara återgå till en kontrollpunkt med kommandot**Återgå till kontrollpunkt.** En alternativ metod är att skapa extra celler och kommentera alla koder som du inte vill köra. hur som helst fungerar.

Du kan också använda kommandot **Skapa** > **fil en kopia** när som helst för att göra en kopia av anteckningsbokens aktuella tillstånd till en ny fil i projektet. Den kopian öppnas automatiskt på en ny webbläsarflik.

När du är klar med en anteckningsbok använder du kommandot **Stäng** > **fil och stoppar,** som stänger anteckningsboken och stänger av kärnan som har kört den. Azure-anteckningsböcker stänger sedan webbläsarfliken automatiskt.

## <a name="debug-notebooks-using-visual-studio-code"></a>Felsöka anteckningsböcker med Visual Studio-kod

Om kodcellerna i anteckningsboken inte beter sig som du förväntar dig kan du ha kodfel eller andra defekter. Men förutom att `print` använda satser för att visa värdet av variabler, erbjuder en typisk Jupyter-miljö inte några felsökningsmöjligheter.

Lyckligtvis kan du ladda ner anteckningsbokens *IPynb-fil* och sedan öppna den i Visual Studio-kod med Python-tillägget. Tillägget importerar direkt en anteckningsbok som en enda kodfil och bevarar Dina Markdown-celler i kommentarer. När du har importerat anteckningsboken kan du använda felsökningen av Visual Studio-kod för att gå igenom koden, ange brytpunkter, undersöka tillstånd och så vidare. När du har korrigerat koden exporterar du sedan *IPynb-filen* från Visual Studio-koden och överför den till Azure Notebooks.

Mer information finns i [Felsöka en Jupyter-anteckningsbok](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) i Dokumentationen för Visual Studio-kod.

Se även [Visual Studio Code - Jupyter stöd](https://code.visualstudio.com/docs/python/jupyter-support) för ytterligare funktioner i Visual Studio Code för Jupyter bärbara datorer.

## <a name="next-steps"></a>Nästa steg

- [Utforska notebook-exempelfiler](azure-notebooks-samples.md)

Instruktionsartiklar:

- [Skapa och klona projekt](create-clone-jupyter-notebooks.md)
- [Konfigurera och hantera projekt](configure-manage-azure-notebooks-projects.md)
- [Installera paket inifrån en notebook-fil](install-packages-jupyter-notebook.md)
- [Presentera ett bildspel](present-jupyter-notebooks-slideshow.md)
- [Arbeta med datafiler](work-with-project-data-files.md)
- [Få åtkomst till dataresurser](access-data-resources-jupyter-notebooks.md)
- [Använd Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)

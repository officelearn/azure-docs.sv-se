---
title: "Förbereda data för klassificering av Iris, självstudie i Machine Learning (förhandsversion) | Microsoft Docs"
description: "I den här kompletta självstudien får du lära dig att använda Azure Machine Learning (förhandsversion) från slutpunkt till slutpunkt. Det här är del 1 om förberedelse av data."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/28/2017
ms.openlocfilehash: 975a86c1f9d9692f6eadd232177f33cbbbeeff2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="classifying-iris-part-1-prepare-data"></a>Klassificera Iris del 1: förbereda data
Azure Machine Learning (förhandsversion) är en integrerad, avancerad lösning för datavetenskap och analys, som datatekniker kan använda till att förbereda data, utveckla experiment och distribuera modeller i molnskala.

Den här självstudien är del ett i en serie med tre delar. I den här självstudien går vi igenom grunderna i Machine Learning (förhandsversion). I den här guiden får du lära dig hur man:
> [!div class="checklist"]
> * Skapa ett projekt i Azure Machine Learning Workbench
> * Skapa ett databearbetningspaket
> * Generera Python/PySpark-kod som anropar databearbetningspaketet

I den här självstudien gör vi det enkelt för oss och använder den tidlösa [Iris-datauppsättningen](https://en.wikipedia.org/wiki/Iris_flower_data_set). Skärmbilderna är Windows-specifika, men upplevelsen är nästan identisk i macOS.

## <a name="prerequisites"></a>Krav
- Skapa ett konto för Machine Learning-experimentering
- Installera Azure Machine Learning Workbench

Du kan följa [snabbstarten för att installera och skapa konton](quickstart-installation.md) för att installera Azure Machine Learning Workbench. Installationen innehåller också kommandoradsgränssnittet (CLI).

## <a name="create-a-new-project-in-azure-ml-workbench"></a>Skapa ett nytt projekt i Azure ML Workbench
1. Starta appen Azure Machine Learning Workbench och logga in om det behövs. I rutan **PROJEKT** klickar du på ikonen **+** för att skapa ett **Nytt projekt**.

   ![ny arbetsyta](media/tutorial-classifying-iris/new_ws.png)

2. Fyll i information i **Skapa nytt projekt**. 

   ![Nytt projekt](media/tutorial-classifying-iris/new_project.png)

   - Ange ett namn på projektet i fältet **Projektnamn**. Du kan till exempel använda värdet **myIris**.
   - Välj i vilken **Projektkatalog** projektet ska skapas. Du kan till exempel använda värdet **C:\Temp**. 
   - Ange en **Projektbeskrivning**. 
   - Fältet för **Git-lagringsplats** är valfritt och kan lämnas tomt. Du kan ange en befintlig, tom Git-lagringsplats (en lagringsplats utan huvudgren) på VSTS. Om du gör det så kan du aktivera scenarier för roaming och delning senare. Mer information finns i artikeln [Använda Git-lagringsplatser](using-git-ml-project.md). 
   - Välj en **Arbetsyta**, i den här självstudien används till exempel **IrisGarden**. 
   - Välj mallen **Klassificera Iris** i listan med projektmallar. 

3. Klicka på knappen **Skapa** för att skapa projektet. Projektet skapas och öppnas för dig.

## <a name="create-a-data-preparation-package"></a>Skapa ett databearbetningspaket
1. Öppna filen **iris.csv** från **filvyn**. Filen är en enkel tabell med 5 kolumner och 150 rader. Det har fyra numeriska kolumner och en strängmålkolumn. Den har inga kolumnrubriker.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > Du bör inte ha med datafiler i projektmappen, särskilt om filerna är stora. Vi tar med **iris.csv** i den här mallen i demonstrationssyfte eftersom den är mycket liten. Mer information finns i artikeln [Läsa och skriva stora datafiler](how-to-read-write-files.md).

2. I **datavyn** klickar du på ikonen **+** för att lägga till en ny datakälla. Guiden **Lägg till datakälla** startar. 

   ![datavy](media/tutorial-classifying-iris/data_view.png)

3. Slutför guiden Förberedelse av data. 
   - På den första skärmen väljer du alternativet **File(s)/Directory** (Fil(er)/katalog) och klickar på **Nästa**.
   - På den andra skärmen väljer du den lokala filen **iris.csv**, till exempel ”C:\Temp\myIris\iris.csv”.
   - På den tredje skärmen **Filinformation** godkänner du standardvärdena.
   - På den fjärde skärmen **Datatyper** ändrar du **DATATYP** från _Sträng_ till _Numeriskt_ för _Column1_ till och med _Column4_ eftersom dessa kolumner är numeriska värden. 
   - På den femte och sjätte skärmen godkänner du standardinställningarna.
   - Klicka på knappen **Slutför**.

   ![välj iris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Se till att du väljer filen **iris.csv** från den aktuella projektkatalogen, annars kan senare steg misslyckas. 

4. Den nya filen **iris-1.dsource** skapas. Filen får ett unikt namn med ”-1”, eftersom exempelprojektet redan har en onumrerad version av filen **iris.dsource**.  

   Filen öppnas och data visas. En serie med kolumnrubriker, från **Column1** till **Column5**, läggs automatiskt till i datauppsättningen. Om du rullar ned till slutet ser du att den sista raden i datauppsättningen är tom. Det beror på en extra radbrytning i csv-filen.

   ![datavy för iris](media/tutorial-classifying-iris/iris_data_view.png)

5. Klicka nu på knappen **Mått**. Här visas histogram och en komplett uppsättning statistik som beräknas för varje kolumn. Om du vill se data igen kan du klicka på knappen **Data**. 

   ![datavy för iris](media/tutorial-classifying-iris/iris_metrics_view.png)

6. Klicka på knappen **Förbered**. Dialogrutan **Förbered** öppnas. 

   Exempelprojektet innehåller filen **iris.dprep** och du uppmanas därför som standard att skapa ett nytt dataflöde i det befintliga databearbetningspaketet **iris.dprep**. 

   Ändra värdet i listrutan till **+Nytt databearbetningspaket**, ange det nya värdet ”iris-1” och klicka sedan på **OK**.

   ![Datavy för iris](media/tutorial-classifying-iris/new_dprep.png)

   Ett nytt databearbetningspaket med namnet **iris-1.dprep** skapas och öppnas i redigeraren för databearbetning.

7. Nu ska vi utföra lite grundläggande databearbetning. Byt namn på kolumnerna genom att klicka på respektive kolumnrubrik så att texten kan redigeras. 

   Ange **Sepal Length** (Foderbladlängd), **Sepal Width** (Foderbladbredd), **Petal Length** (Kronbladlängd), **Petal Width** (Kronbladbredd) och **Species** (Typer) för de fem kolumnerna.

   ![Byta namn på kolumner](media/tutorial-classifying-iris/rename_column.png)

8. Om du vill räkna ut särskilda värden markerar du kolumnen **Species** (Typer) och högerklickar på den. Välj **Antal värden**. 

   ![Klicka på Antal värden](media/tutorial-classifying-iris/value_count.png)

   Den här åtgärden öppnar rutan **Inspectors** (Kontroller) och visar ett histogram med fyra staplar. Lägg märke till att målkolumnen har tre separata värden: **Iris_virginica**, **Iris_versicolor**, **Iris-setosa** och ett **(null)**-värde.

9. Filtrera ut null-värden genom att markera stapeln från grafen som representerar null-värdet. Det finns en rad med ett **(null)**-värde. Om du vill ta bort den här raden klickar du på filterknappen **-**.

   ![Histogram över antal värden](media/tutorial-classifying-iris/filter_out.png)

10. Lägg märke till de enskilda stegen som beskrivs i fönstret **STEG**. När du ändrade namn på kolumner och filtrerade rader med null-värden registrerades varje åtgärd som ett databearbetningssteg. Du kan redigera enskilda steg för att justera inställningarna, ändra ordning på stegen och även ta bort steg.

   ![Steg](media/tutorial-classifying-iris/steps.png)

11. Stäng nu databearbetningsredigeraren genom att klicka på **X** på fliken **iris-1** med en grafikikon. Arbetet sparas automatiskt i filen **iris-1.dprep** som visas under rubriken **Data Preparations** (Dataförberedelser).

## <a name="generate-pythonpyspark-code-to-invoke-data-prep-package"></a>Generera Python/PySpark-kod som anropar databearbetningspaketet

1. Högerklicka på filen **iris 1.dprep** så att snabbmenyn visas och välj alternativet för att **generera fil med kod för dataåtkomst**. 

   ![generera kod](media/tutorial-classifying-iris/generate_code.png)

2. En ny fil med namnet **iris 1.py** öppnas med följande rader med kod:

   ```python
   # This code snippet will load the referenced package and return a DataFrame.
   # If the code is run in a PySpark environment, the code will return a
   # Spark DataFrame. If not, the code will return a Pandas DataFrame.

   from azureml.dataprep.package import run
   df = run('iris.dprep', dataflow_idx=0)
   ```

   Det här kodstycket anropar den logik du skapade som ett databearbetningspaket. Beroende på i vilken kontext den här koden körs representerar `df` olika typer av dataramar. En [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) används när den körs med Python-körning, eller så används en [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) vid körning i Spark. 

   Mer information om hur du förbereder data i Azure Machine Learning Workbench finns i guiden om att [komma igång med databearbetning](data-prep-getting-started.md).

## <a name="next-steps"></a>Nästa steg
I del ett av den här självstudieserien med tre delar har du använt Azure Machine Learning Workbench till att:
> [!div class="checklist"]
> * Skapa ett nytt projekt 
> * Skapa ett databearbetningspaket
> * Generera Python/PySpark-kod som anropar databearbetningspaketet

Nu är du redo att gå vidare till nästa del i serien, där du får skapa en Machine Learning-modell.
> [!div class="nextstepaction"]
> [Skapa en modell](tutorial-classifying-iris-part-2.md)

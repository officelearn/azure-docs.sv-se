---
title: "Förbereda data för klassificering av Iris, självstudie i Azure Machine Learning (förhandsversion) | Microsoft Docs"
description: "I den här kompletta självstudien får du lära dig att använda Azure Machine Learning (förhandsversionen) från slutpunkt till slutpunkt. Det här är del ett, som beskriver förberedelse av data."
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
ms.openlocfilehash: fcef59d38cce5f8860508ebeeda0b1b97f5e83f1
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2017
---
# <a name="classify-iris-part-1-prepare-the-data"></a>Klassificera Iris del 1: Förbereda data
Azure Machine Learning (förhandsversionen) är en integrerad, avancerad lösning för datavetenskap och analys som datatekniker kan använda för att förbereda data, utveckla experiment och distribuera modeller i molnskala.

Den här självstudien är del ett i en serie med tre delar. I den här självstudien går vi igenom grunderna i Azure Machine Learning (förhandsversion). Lär dig att:
> [!div class="checklist"]
> * Skapa ett projekt i Azure Machine Learning Workbench.
> * Skapa ett dataförberedningspaket.
> * Generera Python/PySpark-kod som anropar ett dataförberedningspaket.

Den här självstudien använder den tidlösa [Iris-datauppsättningen](https://en.wikipedia.org/wiki/Iris_flower_data_set). Skärmbilderna är Windows-specifika, men upplevelsen är nästan identisk i Mac OS.

## <a name="prerequisites"></a>Krav
- Skapa ett konto för Machine Learning-experimentering.
- Installera Azure Machine Learning Workbench.

Du kan följa anvisningarna i artikeln [Snabbstarten för att installera och skapa konton](quickstart-installation.md) för att installera Azure Machine Learning Workbench. Den här installationen innehåller även Azures plattformsoberoende kommandoradsverktyg, som även kallas Azure CLI.

## <a name="create-a-new-project-in-azure-machine-learning-workbench"></a>Skapa ett nytt projekt i Azure Machine Learning Workbench
1. Öppna appen Azure Machine Learning Workbench och logga in om det behövs. Välj plustecknet (**+**) i fönstret **PROJEKT** för att skapa ett **Nytt projekt**.

   ![Ny arbetsyta](media/tutorial-classifying-iris/new_ws.png)

2. Fyll i information i **Skapa nytt projekt**: 

   ![Nytt projekt](media/tutorial-classifying-iris/new_project.png)

   - Ange ett namn för projektet i fältet **Projektnamn**. Du kan till exempel använda värdet **myIris**.
   - Välj i vilken **Projektkatalog** projektet ska skapas. Du kan till exempel använda värdet `C:\Temp\`. 
   - Ange en **Projektbeskrivning**. Det här är valfritt. 
   - Fältet för **Git-lagringsplats** är också valfritt och kan lämnas tomt. Du kan ange en befintlig, tom Git-lagringsplats (en lagringsplats utan huvudgren) på Visual Studio Team Services. Om du använder en Git-lagringsplats som redan finns så kan du aktivera scenarion för roaming och delning senare. Mer information finns i [Use Git repo](using-git-ml-project.md) (Använd Git-lagringsplats). 
   - Välj en **Arbetsyta**. I den här självstudien används till exempel **IrisGarden**. 
   - Välj mallen **Klassificera Iris** i listan med projektmallar. 

3. Välj knappen **Skapa**. Projektet skapas och öppnas för dig.

## <a name="create-a-data-preparation-package"></a>Skapa ett databearbetningspaket
1. Öppna filen **iris.csv** från **filvyn**. Filen är en tabell med 5 kolumner och 150 rader. Det har fyra numeriska kolumner och en strängmålkolumn. Den har inga kolumnrubriker.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > Inkludera inte datafiler i projektmappen, särskilt om filerna är stora. Vi tar med **iris.csv** i den här mallen i demonstrationssyfte eftersom den är mycket liten. Mer information finns i [Läsa och skriva stora datafiler](how-to-read-write-files.md).

2. I **datavyn** klickar du på plustecknet (**+**) för att lägga till en ny datakälla. Sidan **Lägg till datakälla** öppnas. 

   ![Datavy](media/tutorial-classifying-iris/data_view.png)

3. Låt standardvärdena vara kvar och välj sedan knappen **Nästa**.  
 
   ![Välj iris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Se till att du väljer filen **iris.csv** från den aktuella projektkatalogen för den här övningen. Annars kan senare steg misslyckas.
   
4. När du har markerat filen väljer du knappen **Slutför**.

4. Den nya filen som heter **iris-1.dsource** skapas. Filen får ett unikt namn med ”-1”, eftersom exempelprojektet redan har en onumrerad version av filen **iris.dsource**.  

   Filen öppnas och data visas. En serie med kolumnrubriker, från **Column1** till **Column5**, läggs automatiskt till i datauppsättningen. Om du rullar ned till slutet ser du att den sista raden i datauppsättningen är tom. Raden är tom på grund av en extra radbrytning i csv-filen.

   ![Datavy för iris](media/tutorial-classifying-iris/iris_data_view.png)

5. Välj knappen **Mått**. Här visas histogram. En komplett uppsättning statistik som har beräknats för varje kolumn. Om du vill se data igen kan du välja knappen **Data**. 

   ![Datavy för iris](media/tutorial-classifying-iris/iris_metrics_view.png)

6. Välj knappen **Förbered**. Dialogrutan **Förbered** öppnas. 

   Exempelprojektet levereras med en **iris.dprep**-fil. Som standard uppmanas du att skapa ett nytt dataflöde i dataförberedningspaketet **iris.dprep** som redan finns. 

   Välj **+ Nytt dataförberedningspaket** på den nedrullningsbara menyn, ange ett nytt värde för paketnamnet, använd **iris-1** och välj sedan **OK**.

   ![Datavy för iris](media/tutorial-classifying-iris/new_dprep.png)

   Ett nytt dataförberedningspaket med namnet **iris-1.dprep** skapas och öppnas i redigeraren för dataförberedning.

7. Nu ska vi utföra lite grundläggande databearbetning. Byt namn på kolumnerna. Markera varje kolumnrubrik för att redigera rubriktexten. 

   Ange **Sepal Length** (Foderbladlängd), **Sepal Width** (Foderbladbredd), **Petal Length** (Kronbladlängd), **Petal Width** (Kronbladbredd) och **Species** (Typer) för de fem kolumnerna.

   ![Byta namn på kolumner](media/tutorial-classifying-iris/rename_column.png)

8. Om du vill räkna ut särskilda värden markerar du kolumnen **Species** (Typer) och sedan högerklickar du för att markera den. Välj **Antal värden** på den nedrullningsbara menyn. 

   ![Välj Antal värden](media/tutorial-classifying-iris/value_count.png)

   Den här åtgärden öppnar fönstret **Inspectors** (Kontroller) och visar ett histogram med fyra staplar. Målkolumnen har tre separata värden: **Iris_virginica**, **Iris_versicolor**, **Iris-setosa** och ett **(null)**-värde.

9. Filtrera ut null-värden genom att markera stapeln från grafen som representerar null-värdet. Det finns en rad med ett **(null)**-värde. Välj minustecknet (**-**) för att ta bort den här raden.

   ![Histogram över antal värden](media/tutorial-classifying-iris/filter_out.png)

10. Lägg märke till de enskilda stegen som beskrivs i fönstret **STEG**. När du ändrade namn på kolumner och filtrerade rader med null-värden registrerades varje åtgärd som ett dataförberedningssteg. Du kan redigera enskilda steg för att justera inställningarna, ändra ordning på stegen eller ta bort steg.

   ![Steg](media/tutorial-classifying-iris/steps.png)

11. Stäng redigeraren för dataförberedning. Välj **Stäng** (x) på fliken **iris-1** med diagramikonen. Arbetet sparas automatiskt i filen **iris-1.dprep** som visas under rubriken **Data Preparations** (Dataförberedelser).

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>Generera Python/PySpark-kod som anropar ett dataförberedningspaket

1. Högerklicka på filen **iris 1.dprep** så att snabbmenyn visas och välj alternativet för att **generera fil med kod för dataåtkomst**. 

   ![Generera kod](media/tutorial-classifying-iris/generate_code.png)

2. En ny fil med namnet **iris 1.py** öppnas med följande rader med kod:

   ```python
   # Use the Azure Machine Learning data preparation package
   from azureml.dataprep import package

   # Use the Azure Machine Learning data collector to log various metrics
   from azureml.logging import get_azureml_logger
   logger = get_azureml_logger()

   # This call will load the referenced package and return a DataFrame.
   # If run in a PySpark environment, this call returns a
   # Spark DataFrame. If not, it will return a Pandas DataFrame.
   df = package.run('iris-1.dprep', dataflow_idx=0)

   # Remove this line and add code that uses the DataFrame
   df.head(10)
   ```

   Det här kodstycket anropar den logik du skapade som ett databearbetningspaket. Beroende på i vilken kontext den här koden körs representerar `df` olika typer av dataramar. En [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) används när den körs med Python-körning, eller så används en [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) vid körning i Spark. 

   Mer information om hur du förbereder data i Azure Machine Learning Workbench finns i guiden [Get started with data preparation](data-prep-getting-started.md) (Kom igång med dataförberedning).

## <a name="next-steps"></a>Nästa steg
I del ett av den här självstudieserien med tre delar har du använt Azure Machine Learning Workbench för att:
> [!div class="checklist"]
> * Skapa ett nytt projekt. 
> * Skapa ett dataförberedningspaket.
> * Generera Python/PySpark-kod som anropar ett dataförberedningspaket.

Nu är du redo att gå vidare till nästa del i serien, där du får lära dig hur du skapar en Azure Machine Learning-modell:
> [!div class="nextstepaction"]
> [Skapa en modell](tutorial-classifying-iris-part-2.md)

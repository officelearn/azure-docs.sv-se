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
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e88b6bf74b7492353e5d5d004bde12fa4787e405
ms.contentlocale: sv-se
ms.lasthandoff: 09/25/2017

---

# <a name="classifying-iris-part-1-prepare-data"></a>Klassificera Iris del 1: förbereda data
Azure Machine Learning (förhandsversion) är en integrerad, avancerad lösning för datavetenskap och analys, som datatekniker kan använda till att förbereda data, utveckla experiment och distribuera modeller i molnskala.

Den här självstudien är del ett i en serie med tre delar. I den här självstudien går vi igenom grunderna i Machine Learning (förhandsversion). I den här självstudien får du lära dig att:
> [!div class="checklist"]
> * Skapa ett projekt i Azure Machine Learning Workbench
> * Skapa ett databearbetningspaket
> * Generera Python/PySpark-kod som anropar databearbetningspaketet

I den här självstudien gör vi det enkelt för oss och använder den tidlösa [Iris-datauppsättningen](https://en.wikipedia.org/wiki/Iris_flower_data_set). Skärmbilderna är Windows-specifika, men upplevelsen är nästan identisk i macOS.

## <a name="launch-azure-machine-learning-workbench"></a>Starta Azure Machine Learning Workbench
Följ [snabbstarten för att installera och skapa konton](quickstart-installation.md) så att du installerar Azure Machine Learning Workbench, som även innehåller kommandoradsgränssnittet (CLI). Starta appen Azure Machine Learning Workbench och logga in om det behövs.

## <a name="create-a-new-project"></a>Skapa ett nytt projekt
1. I rutan **PROJEKT** klickar du på ikonen **+** för att skapa ett **Nytt projekt**.

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
1. Öppna filen `iris.csv` från **filvyn**. Filen är en enkel tabell med 5 kolumner och 150 rader. Det har fyra numeriska kolumner och en strängmålkolumn. Den har inga kolumnrubriker.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   >Obs! Du bör inte ha med datafiler i projektmappen, särskilt om filerna är stora. Vi tar med `iris.csv` i den här mallen i demonstrationssyfte eftersom den är mycket liten. Mer information finns i artikeln [Läsa och skriva stora datafiler](how-to-read-write-files.md).

2. I **datavyn** klickar du på ikonen **+** för att lägga till en ny datakälla. Guiden **Lägg till datakälla** startar. 

   ![datavy](media/tutorial-classifying-iris/data_view.png)

3. Välj alternativet **Fil(er)/katalog** och välj den lokala filen `iris.csv`. Acceptera standardinställningarna på varje skärm och klicka sedan på **Slutför**. 

   ![välj iris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Se till att du väljer filen `iris.csv` från den aktuella projektkatalogen, annars kan senare steg misslyckas. 

4. Den nya filen `iris-1.dsource` skapas. Filens namn är unikt, med ”-1”, eftersom exempelprojektet redan har en onumrerad version av filen `iris.dsource`.  Filen öppnas och data visas. En serie med kolumnrubriker, från `Column1` till `Column5`, läggs automatiskt till i datauppsättningen. Om du rullar ned till slutet ser du att den sista raden i datauppsättningen är tom. Det beror på en extra radbrytning i csv-filen.

   ![datavy för iris](media/tutorial-classifying-iris/iris_data_view.png)

5. Klicka nu på knappen **Mått**. Här visas histogram och en komplett uppsättning statistik som beräknas för varje kolumn. Om du vill se data igen kan du klicka på knappen **Data**. 

   ![datavy för iris](media/tutorial-classifying-iris/iris_metrics_view.png)

6. Klicka på knappen **Förbered** bredvid knappen **Mått** (eller på knappen **Data** om du står i måttvyn). Dialogrutan **Förbered** öppnas. Exempelprojektet innehåller redan filen `iris.dprep`, så som standard uppmanas du att skapa ett nytt dataflöde i det befintliga databearbetningspaketet **iris.dprep**. Ändra värdet i listrutan till **+Nytt databearbetningspaket**, ange det nya värdet ”iris-1” och klicka sedan på **OK**.

   ![datavy för iris](media/tutorial-classifying-iris/new_dprep.png)

Ett nytt databearbetningspaket med namnet `iris-1.dprep` skapas och öppnas i redigeraren för databearbetning.

Nu ska vi utföra lite enklare databearbetning. Byt namn på kolumnerna genom att klicka på respektive kolumnrubrik så att texten kan redigeras. Ange `Sepal Length`, `Sepal Width`, `Petal Length`, `Petal Width` och `Species` för de fem kolumnerna.

![byt namn på kolumnerna](media/tutorial-classifying-iris/rename_column.png)

Välj kolumnen `Species` och högerklicka på den. Välj **Antal värden**. 

![antal värden](media/tutorial-classifying-iris/value_count.png)

Den här åtgärden skapar ett histogram med fyra staplar. Lägg märke till att vår målkolumn har tre unika värden `Iris_virginica`, `Iris_versicolor` och `Iris-setosa`. Dessutom finns det en rad med värdet `(null)`. Nu ska vi ta bort den här raden genom att markera stapeln som representerar null-värdet och ta bort den genom att klicka på filterknappen **-**. 

![antal värden](media/tutorial-classifying-iris/filter_out.png)

Medan du arbetar med att byta namn på kolumner och att filtrera bort raden med nullvärdet lagras varje åtgärd du utför som ett förberedelsesteg i fönstret **STEG**. Du kan redigera dem (om du vill justera inställningarna), ordna om dem och ta bort dem.

![steg](media/tutorial-classifying-iris/steps.png)

## <a name="generate-pythonpyspark-code-to-invoke-data-prep-package"></a>Generera Python/PySpark-kod som anropar databearbetningspaketet

Stäng nu redigeraren för förberedelse av data. (Oroa dig inte, den sparas automatiskt.) Högerklicka på filen **iris 1.dprep** så att snabbmenyn visas och välj alternativet för att **generera fil med kod för dataåtkomst**. 

![generera kod](media/tutorial-classifying-iris/generate_code.png)

Filen **iris 1.py** skapas med följande två kodrader ifyllda (tillsammans med några kommentarer):

```python
# This code snippet will load the referenced package and return a DataFrame.
# If the code is run in a PySpark environment, the code will return a
# Spark DataFrame. If not, the code will return a Pandas DataFrame.

from azureml.dataprep.package import run
df = run('iris.dprep', dataflow_idx=0)
```
Det här kodstycket anropar den logik du skapade som ett databearbetningspaket. Beroende på i vilken kontext koden körs kan `df` vara en [pandas-DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) vid Python-körning eller en [Spark-DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) vid körning i Spark. Mer information om hur du förbereder data i Azure Machine Learning Workbench finns i guiden om att [komma igång med databearbetning](data-prep-getting-started.md).

## <a name="next-steps"></a>Nästa steg
I del ett av den här självstudieserien med tre delar har du använt Azure Machine Learning Workbench till att:
> [!div class="checklist"]
> * Skapa ett nytt projekt 
> * Skapa ett databearbetningspaket
> * Generera Python/PySpark-kod som anropar databearbetningspaketet

Nu är du redo att gå vidare till nästa del i serien, där du får skapa en Machine Learning-modell.
> [!div class="nextstepaction"]
> [Skapa en modell](tutorial-classifying-iris-part-2.md)


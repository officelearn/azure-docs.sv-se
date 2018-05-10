---
title: Skapa en modellsjälvstudie för Azure Machine Learning (förhandsversion) | Microsoft Docs
description: I den här kompletta självstudien får du lära dig att använda Azure Machine Learning (förhandsversionen) från slutpunkt till slutpunkt. Det här är del två som beskriver experimentering.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/15/2018
ms.openlocfilehash: 24ac4d699a511cc99936cb2cd4b245de01984163
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-2-classify-iris---build-a-model"></a>Självstudie 2: Klassificera Iris – bygga en modell
Azure Machine Learning-tjänsterna (förhandsversionen) är en integrerad lösning för datavetenskap och analys som datatekniker kan använda för att förbereda data, utveckla experiment och distribuera modeller i molnskala.

Den här självstudien är **del två i en serie med tre delar**. I den här delen av självstudien använder du Azure Machine Learning för att göra följande:

> [!div class="checklist"]
> * Öppna skript och granskar kod
> * Köra skript i en lokal miljö
> * Granska körningshistorik
> * Köra skript i ett lokalt Azure CLI-fönster
> * Köra skript i en lokal Docker-miljö
> * Köra skript i en fjärransluten Docker-miljö
> * Köra skript i en Azure HDInsight-molnmiljö

Den här självstudien använder den tidlösa [Iris-datauppsättningen](https://en.wikipedia.org/wiki/Iris_flower_data_set). 

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:
- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 
- Ett experimentkonto och Azure Machine Learning Workbench installerad enligt anvisningarna i denna [snabbstart](../service/quickstart-installation.md)
- Projektet och förberedda Iris-data från [del 1 av självstudiekursen](tutorial-classifying-iris-part-1.md)
- En Docker-motor måste vara installerad och köras lokalt. Det räcker med Docker Community Edition. Mer information om hur du installerar Docker finns här: https://docs.docker.com/engine/installation/.

## <a name="review-irissklearnpy-and-the-configuration-files"></a>Granska iris_sklearn.py och konfigurationsfilerna

1. Starta programmet Azure Machine Learning Workbench.

1. Öppna projektet **myIris** som du skapade i [del 1 av självstudiekursen](tutorial-classifying-iris-part-1.md).

2. När projektet är öppet väljer du knappen **Filer** (mappikonen) längst till vänster i fönstret för att öppna fillistan i projektmappen.

   ![Öppna ett projekt i Azure Machine Learning Workbench](media/tutorial-classifying-iris/2-project-open.png)

3. Välj Python-skriptfilen **iris_sklearn.py**. 

   ![Välj ett skript](media/tutorial-classifying-iris/2-choose-iris_sklearn.png)

   Koden öppnas på en ny textredigeringsflik i Workbench. Det här är det skript som du använder under hela den här delen av kursen. 

   >[!NOTE]
   >Koden du ser kanske inte är exakt samma som föregående kod eftersom det här exempelprojektet uppdateras ofta.
   
   ![Öppna en fil](media/tutorial-classifying-iris/open_iris_sklearn.png)

4. Granska Python-skriptkoden så att du bekantar dig med kodningsstilen. 

   Skriptet **iris_sklearn.py** utför följande uppgifter:

   * Standardversionen av dataförberedningspaketet **iris.dprep** läses in för att skapa en [pandas-DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). 

   * Slumpvis utvalda funktioner läggs till som gör det svårare att lösa problemet. Slumpmässigheten behövs eftersom Iris är en liten datamängd som enkelt kan klassificeras med nästan 100 % noggrannhet.

   * Machine Learning-biblioteket [scikit-learn](http://scikit-learn.org/stable/index.html) används till att skapa en Logistic Regression-modell.  Det här biblioteket innehåller Azure Machine Learning Workbench som standard.

   * Serialiserar modellen genom att använda biblioteket [pickle](https://docs.python.org/3/library/pickle.html) i en fil i mappen `outputs`. 
   
   * Läser in den serialiserade modellen och deserialiserar den tillbaka till minnet.

   * Den deserialiserade modellen används till att göra en förutsägelse för en ny post. 

   * Två diagram ritas upp med hjälp av biblioteket [matplotlib](https://matplotlib.org/), en felmatris och en ROC-kurva (receiver operating characteristic) med flera klasser, och de sparas sedan i mappen `outputs`. Du kan installera det här biblioteket i din miljö, om det inte redan fanns där.

   * Visar automatiskt regulariseringshastigheten och modellnoggrannheten i körningshistoriken. Objektet `run_logger` används kontinuerligt för att lagra regulariseringshastigheten och modellnoggrannheten i loggarna. 


## <a name="run-irissklearnpy-in-your-local-environment"></a>Kör iris_sklearn.py i din lokala miljö

1. Starta kommandoradsgränssnittet (CLI) för Azure Machine Learning:
   1. Starta Azure Machine Learning Workbench.

   1. Gå till menyn i Workbench och välj **Arkiv** > **Öppna kommandotolken**. 
   
   Fönstret med kommandoradsgränssnittet (CLI) i Azure Machine Learning startar i projektmappen `C:\Temp\myIris\>` i Windows. Det här projektet är detsamma som det som du skapade i del 1 av självstudiekursen.

   >[!IMPORTANT]
   >Du måste använda CLI-fönstret för att kunna slutföra de nästkommande stegen.

1. Gå till CLI-fönstret och installera Pythons ritbibliotek **matplotlib**, om du inte redan har det installerat.

   Skriptet **iris_sklearn.py** är beroende av två Python-paket: **scikit-learn** och **matplotlib**.  Paketet **scikit-learn** har redan installerats av Azure Machine Learning Workbench. Om du inte redan har installerat **matplotlib** måste du göra det nu.

   Om du fortsätter utan att installera **matplotlib** kan du ändå köra koden i den här självstudiekursen. Däremot kan koden inte producera felmatrisen eller ROC-kurvan för flera klasser i visualiseringen av historiken.

   ```azurecli
   pip install matplotlib
   ```

   Den här installationen tar ungefär en minut.

1. Gå tillbaka till Workbench-programmet. 

1. Hitta fliken med namnet **iris_sklearn.py**. 

   ![Hitta flik med skript](media/tutorial-classifying-iris/2-iris_sklearn-tab.png)

1. I verktygsfältet högst upp på fliken väljer du **lokal** som körningsmiljö och `iris_sklearn.py` som det skript som ska köras. De kan redan vara förvalda.

   ![Val mellan lokal och skript](media/tutorial-classifying-iris/2-local-script.png)

1. Gå till den högra sidan av verktygsfältet och ange `0.01` i fältet **Argument**. 

   Det här värdet motsvarar regulariseringshastigheten för Logistic Regression-modellen.

   ![Val mellan lokal och skript](media/tutorial-classifying-iris/2-local-script-arguments.png)

1. Klicka på knappen **Kör**. Ett jobb schemaläggs omedelbart. Jobbet står med i fönstret **Jobb** till höger i Workbench-fönstret. 

   ![Val mellan lokal och skript](media/tutorial-classifying-iris/2-local-script-arguments-run.png)

   Efter en liten stund ändras statusen för jobbet från **Skickar** till **Körs** och sedan slutligen till **Slutfört**.

1. Välj **Slutfört** i jobbstatustexten i fönstret **Jobb**. 

   ![Kör sklearn](media/tutorial-classifying-iris/2-completed.png)

   Ett popup-fönster öppnas med standardutdatatext (stdout) från körningen. Om du vill stänga stdout-texten väljer du knappen **Stäng** (**x**) längst upp till höger i popup-fönstret.

   ![Standardutdata](media/tutorial-classifying-iris/2-standard-output.png)

9. I samma jobbstatus i fönstret **Jobb** väljer du den blå texten **iris_sklearn.py [n]** (_n_ är körningsnumret) precis ovanför statusen **Slutförd** och starttiden. Fönstret **Körningsegenskaper** öppnas och visar följande information för den specifika körningen:
   - Information om **Körningsegenskaper**
   - **Utdata**
   - **Mått**
   - Eventuella **Visualiseringar**
   - **Loggar** 

   När körningen har slutförts visas följande resultat i popup-fönstret:

   >[!NOTE]
   >Eftersom vi i självstudiekursen introducerade vissa slumpmässigheter i inlärningsuppsättningen kommer de exakta resultaten att variera något från de resultat som visas här.

   ```text
   Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]
   
   Iris dataset shape: (150, 5)
   Regularization rate is 0.01
   LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
          verbose=0, warm_start=False)
   Accuracy is 0.6792452830188679
   
   ==========================================
   Serialize and deserialize using the outputs folder.
   
   Export the model to model.pkl
   Import the model from model.pkl
   New sample: [[3.0, 3.6, 1.3, 0.25]]
   Predicted class is ['Iris-setosa']
   Plotting confusion matrix...
   Confusion matrix in text:
   [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
   Confusion matrix plotted.
   Plotting ROC curve....
   ROC curve plotted.
   Confusion matrix and ROC curve plotted. See them in Run History details pane.
   ```
    
10. Stäng fliken **Körningsegenskaper** och återgå sedan till fliken **iris_sklearn.py**. 

11. Upprepa för flera körningar. 

    Ange en serie med värden i fältet **Argument**, från `0.001` till `10`. Välj **Kör** för att köra koden några gånger till. De argumentvärden du ändrar varje gång skickas till Logistic Regression-modellen i koden, vilket ger olika resultat varje gång.

## <a name="review-the-run-history-in-detail"></a>Granska körningshistoriken i detalj
I Azure Machine Learning Workbench sparas varje skriptkörning som en post i körningshistoriken. Du kan visa körningshistoriken för ett visst skript genom att öppna vyn **Körningar**.

1. Välj knappen **Körningar** (klockikonen) i det vänstra verktygsfältet för att öppna listan över **Körningar**. Välj **iris_sklearn.py** för att se **Instrumentpanelen för körningar** för `iris_sklearn.py`.

   ![Körningsvy](media/tutorial-classifying-iris/run_view.png)

1. Fliken med **Instrumentpanelen för körningar** öppnas. 

   Granska statistiken som sparats från de många körningarna. Diagram återges längst upp på fliken. Varje körning har ett på varandra efterföljande nummer och körningsinformation visas i tabellen längst ned på skärmen.

   ![Instrumentpanelen för körningar](media/tutorial-classifying-iris/run_dashboard.png)

1. Filtrera tabellen och välj sedan något av diagrammen för att visa status, längd, noggrannhet och regulariseringshastighet för varje körning. 

1. Markera kryssrutorna bredvid två eller flera körningar i tabellen **Körningar**. Klicka på knappen **Jämför** för att öppna ett fönster med en detaljerad jämförelse. Granska jämförelsen. 

1. Välj bakåtknappen **Körningslista** uppe till vänster i **Jämförelsefönstret** så att du återgår till **Instrumentpanelen för körningar**.

   ![Gå tillbaka till körningslistan](media/tutorial-classifying-iris/2-compare-back.png)

1. Välj en enskild körning så att detaljvyn visas. Observera att statistiken för den valda körningen visas i avsnittet **Körningsegenskaper**. Filerna som skrivits till utdatamappen visas i avsnittet **Utdata** och du kan ladda ned filerna därifrån.

   ![Körningsinformation](media/tutorial-classifying-iris/run_details.png)

   De två diagrammen, felmatrisen och ROC-kurvan för flera klasser, återges i avsnittet **Visualisering**. Du hittar även alla loggfiler i avsnittet **Loggar**.


## <a name="run-scripts-in-local-docker-environments"></a>Köra skript i lokala Docker-miljöer

Du kan också experimentera med skript som körs med en lokal Docker-behållare. Du kan enkelt konfigurera ytterligare körningsmiljöer, som t.ex. Docker, och köra skriptet i dessa miljöer. 

>[!NOTE]
>Om du vill experimentera med att skicka skript som ska köras i en Docker-behållare på en fjärransluten virtuell dator i Azure eller ett Azure HDInsight Spark-kluster kan du följa [instruktionerna för att skapa en Ubuntu-baserad Azure Data Science Virtual Machine eller ett HDInsight-kluster](how-to-create-dsvm-hdi.md).

1. Om du inte har gjort det ännu kan du installera och starta Docker lokalt på din Windows- eller MacOS-dator. Läs installationsinstruktionerna för Docker på https://docs.docker.com/install/. Det räcker med Community Edition.

1. Välj **mappikonen** i det vänstra fönstret så att du öppnar listan med **Filer** för projektet. Expandera mappen `aml_config`. 

2. Det finns flera förkonfigurerade miljöer: **docker-python**, **docker-spark** och **lokalt**. 

   Alla miljöer har två filer, som `docker.compute` (för både **docker-python** och **docker-spark**) och `docker-python.runconfig`. Öppna varje fil för att se att vissa alternativ kan konfigureras i textredigeraren.  

   Välj **Stäng** (**x**) på flikarna i eventuella öppna textredigerare för att frigöra utrymme.

3. Kör skriptet **iris_sklearn.py** med miljön **docker-python**: 

   - Välj **klockikonen** i det vänstra verktygsfältet för att öppna fönstret **Körningar**. Välj **Alla körningar**. 

   - Längst upp på fliken **Alla körningar** väljer du **docker-python** som målmiljö istället för standardvärdet **lokalt**. 

   - Gå sedan till höger sida och välj **iris_sklearn.py** som skriptet att köra. 

   - Lämna fältet **Argument** tomt eftersom skriptet anger ett standardvärde. 

   - Klicka på knappen **Kör**.

4. Observera att ett nytt jobb startar. Det visas i fönstret **Jobb** till höger i Workbench-fönstret.

   När du kör mot Docker för första gången tar körningen några minuter mer att slutföras. 

   Azure Machine Learning Workbench skapar en ny Docker-fil i bakgrunden. 
   Den nya docker-filen refererar till basavbildningen för Docker, som anges i filen `docker.compute`, Python-paket med beroenden som anges i filen `conda_dependencies.yml`. 
   
   Docker-motorn utför följande uppgifter:

    - Laddar ned basavbildningen från Azure.
    - Installerar Python-paketet som anges i filen `conda_dependencies.yml`.
    - Startar en Docker-behållare.
    - Kopior eller referenser, beroende på körningskonfigurationen, den lokala kopian av projektmappen.      
    - Kör skriptet `iris_sklearn.py`.

   I slutändan bör du se exakt samma resultat som när du använder **lokalt** som mål.

5. Nu ska vi prova Spark. Docker-basavbildningen innehåller en förinstallerad och förkonfigurerad Spark-instans som du kan använda för att utföra PySpark-skript. Genom att använda basavbildningen kan du på ett enkelt sätt utveckla och testa ditt eget Spark-program utan att behöva ägna tid åt att installera och konfigurera Spark själv. 

   Öppna filen `iris_spark.py`. Skriptet läser in datafilen `iris.csv` och sedan används Logistic Regression-algoritmen från Spark Machine Learning-biblioteket till att klassificera Iris-datauppsättningen. Ändra nu körningsmiljön till **docker-spark**, skriptet till **iris_spark.py** och kör sedan skriptet igen. Den här processen tar lite längre tid eftersom en Spark-session måste skapas och startas i Docker-behållaren. Du kan också se att stdout inte är samma som stdout för `iris_spark.py`.

6. Starta några fler körningar och prova olika argument. 

7. Öppna filen `iris_spark.py` för att se Logistic Regression-modellen som skapats med Spark Machine Learning-biblioteket. 

8. Interagera med fönstret **Jobb**, öppna listan med körningshistorik och visa detaljer om dina körningar i olika körningsmiljöer.

## <a name="run-scripts-in-the-cli-window"></a>Köra skript i CLI-fönstret

1. Starta kommandoradsgränssnittet (CLI) för Azure Machine Learning:
   1. Starta Azure Machine Learning Workbench.

   1. Gå till menyn i Workbench och välj **Arkiv** > **Öppna kommandotolken**. 
   
   Kommandotolken startar i projektmappen `C:\Temp\myIris\>` i Windows. Det här är det som du skapade i del 1 av självstudiekursen.

   >[!IMPORTANT]
   >Du måste använda CLI-fönstret för att kunna slutföra de nästkommande stegen.

1. Logga in på Azure i CLI-fönstret. [Läs mer om az-inloggning](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   Du kan redan vara inloggad. I så fall kan du hoppa över det här steget.

   1. Skriv följande i kommandotolken:
      ```azurecli
      az login
      ```

      Det här kommandot returnerar en kod som ska användas i webbläsaren på https://aka.ms/devicelogin.

   1. Gå till https://aka.ms/devicelogin med webbläsaren.

   1. När du uppmanas göra det anger du den kod som du fick i kommandotolken.

   Workbench-appen och kommandoradsgränssnittet (CLI) använder oberoende cacheminnen för inloggningsuppgifter vid autentisering mot Azure-resurser. När du väl har loggat in behöver du inte autentisera dig igen förrän din cachelagrade token upphör att gälla. 

1. Om din organisation har flera Azure-prenumerationer (företagsmiljö) måste du ange vilken prenumeration som ska användas. Leta reda på rätt prenumeration, konfigurera den med hjälp av prenumerations-ID och testa den.

   1. Använd det här kommandot för att visa en lista med alla Azure-prenumerationer som du har åtkomst till:
   
      ```azurecli
      az account list -o table
      ```

      Kommandot **az account list** returnerar listan med prenumerationer som är tillgängliga för din inloggning. 
      Om det finns fler än en ska du leta reda på prenumerations-ID:t för den prenumeration som du vill använda.

   1. Ange vilken Azure-prenumeration du vill använda som standardkonto:
   
      ```azurecli
      az account set -s <your-subscription-id>
      ```
      där \<your-subscription-id\> är ID-värdet för den prenumeration som du vill använda. Ta inte med hakparenteserna.

   1. Bekräfta de nya inställningarna för prenumerationen genom att begära ut information om den aktuella prenumerationen. 

      ```azurecli
      az account show
      ```    

1. Gå till CLI-fönstret och installera Pythons ritbibliotek **matplotlib**, om du inte redan har det installerat.

   ```azurecli
   pip install matplotlib
   ```

1. Gå till CLI-fönstret och skicka skriptet **iris_sklearn.py** som ett experiment.

   Körningen av iris_sklearn.py görs mot det lokala beräkningssammanhanget.

   + I Windows:
     ```azurecli
     az ml experiment submit -c local .\iris_sklearn.py
     ```

   + I MacOS:
     ```azurecli
     az ml experiment submit -c local iris_sklearn.py
     ```
   
   Resultatet bör likna följande:
    ```text
    RunId: myIris_1521077190506
    
    Executing user inputs .....
    ===========================
    
    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]
    
    Iris dataset shape: (150, 5)
    Regularization rate is 0.01
    LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
              intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
              penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
              verbose=0, warm_start=False)
    Accuracy is 0.6792452830188679
        
    ==========================================
    Serialize and deserialize using the outputs folder.
    
    Export the model to model.pkl
    Import the model from model.pkl
    New sample: [[3.0, 3.6, 1.3, 0.25]]
    Predicted class is ['Iris-setosa']
    Plotting confusion matrix...
    Confusion matrix in text:
    [[50  0  0]
     [ 1 37 12]
     [ 0  4 46]]
    Confusion matrix plotted.
    Plotting ROC curve....
    ROC curve plotted.
    Confusion matrix and ROC curve plotted. See them in Run History details page.
    
    Execution Details
    =================
    RunId: myIris_1521077190506
    ```

1. Granska utdata. Utdata och resultaten är desamma som när du tidigare använde Workbench för att köra skriptet. 

1. Gå till CLI-fönstret och kör Python-skriptet **iris_sklearn.py**, även den här gången med en Docker-körningsmiljö (om du inte redan har Docker installerat på datorn).

   + Om din behållare finns i Windows: 
     |Körnings-<br/>miljö|Kommando i Windows|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python .\iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark .\iris_spark.py 0.1`|

   + Om din behållare finns i MacOS: 
     |Körnings-<br/>miljö|Kommando i Windows|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark iris_spark.py 0.1`|

1. Gå tillbaka till Workbench och gör följande:
   1. Välj mappikonen i den vänstra panelen för att visa en lista med alla projektfiler.
   
   1. Öppna Python-skriptet med namnet **run.py**. Det här skriptet är användbart när du vill loopa över olika regulariseringshastigheter. 

   ![Gå tillbaka till körningslistan](media/tutorial-classifying-iris/2-runpy.png)

1. Kör experimentet flera gånger med dessa hastigheter. 

   Det här skriptet startar ett `iris_sklearn.py`-jobb med en regulariseringshastighet på `10.0` (löjligt hög hastighet). Skriptet halverar sedan hastigheten i följande körning och så vidare tills hastigheten är så långsam som `0.005`. 

   Skriptet innehåller följande kod:

   ![Gå tillbaka till körningslistan](media/tutorial-classifying-iris/2-runpy-code.png)

1. Kör skriptet **run.py** från kommandoraden enligt följande:

   ```cmd
   python run.py
   ```

   Det här kommandot skickar iris_sklearn.py flera gånger med olika regulariseringshastigheter.

   När `run.py` har slutförts visas ett diagram med olika värden i listvyn för körningshistoriken i Workbench.

## <a name="run-scripts-in-a-remote-docker-container"></a>Köra skript i en fjärransluten Docker-behållare
Om du vill köra skriptet i en Docker-behållare på en fjärrdator med Linux måste du ha SSH-åtkomst (användarnamn och lösenord) till fjärrdatorn. Dessutom måste datorn ha en Docker-motor installerad och igång. Det enklaste sättet att ordna en sådan Linux-dator är att skapa en Ubuntu-baserad DSVM (Data Science Virtual Machine) i Azure. Lär dig [att skapa en Ubuntu DSVM som ska användas i Azure ML Workbench](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal).

>[!NOTE] 
>CentOS-baserade DSVM:er stöds *inte*.

1. När den virtuella datorn har skapats kan du ansluta den som en körningsmiljö om du genererar ett par `.runconfig`- och `.compute`-filer. Använd följande kommando för att generera filerna. 

 Vi ger det nya beräkningsmålet namnet `myvm`.
 
   ```azurecli
   az ml computetarget attach remotedocker --name myvm --address <your-IP> --username <your-username> --password <your-password>
   ```
   
   >[!NOTE]
   >IP-adresserna kan även vara offentligt adresserbara FQDN (fullständigt kvalificerade domännamn), till exempel `vm-name.southcentralus.cloudapp.azure.com`. Det är en bra idé att lägga till FQDN för din DSVM och använda den istället för IP-adresser. Det här är en bra idé eftersom du kanske vill stänga av den virtuella datorn senare för att spara på kostnaden. Nästa gång du startar den virtuella datorn kan IP-adresserna ha ändrats.

   >[!NOTE]
   >Utöver autentisering med användarnamn och lösenord kan du ange en privat nyckel och motsvarande lösenfras (om sådan används) med hjälp av alternativen `--private-key-file` och (som tillval) `--private-key-passphrase`.

   Förbered beräkningsmålet **myvm** genom att köra det här kommandot.
   
   ```azurecli
   az ml experiment prepare -c myvm
   ```
   
   Det föregående kommandot skapar Docker-avbildningen på den virtuella datorn, så att den blir redo att köra skripten:
   
   >[!NOTE]
   >Du kan även ändra värdet för `PrepareEnvironment` i `myvm.runconfig` från standardvärdet `false` till `true`. Den här förändringen förbereder automatiskt Docker-behållaren som en del av den första körningen.

2. Redigera den genererade filen `myvm.runconfig` under `aml_config` och ändra ramverket från standardvärdet `PySpark` till `Python`:

   ```yaml
   Framework: Python
   ```
   >[!NOTE]
   >Även om PySpark också bör fungera är Python effektivare om du inte verkligen behöver en Spark-session för att köra Python-skriptet.

3. Kör samma kommando som du gjorde förut i CLI-fönstret med _myvm_ som mål för körningen av iris_sklearn.py i en fjärransluten Docker-behållare:
   ```azurecli
   az ml experiment submit -c myvm iris_sklearn.py
   ```
   Kommandot körs som om du använder en `docker-python`-miljö, förutom att körningen sker på den virtuella fjärrdatorn med Linux. Samma utdata visas i CLI-fönstret.

4. Nu ska vi prova att använda Spark i behållaren. Öppna Utforskaren. Skapa en kopia av filen `myvm.runconfig` och ge den namnet `myvm-spark.runconfig`. Redigera den nya filen och ändra inställningen `Framework` från `Python` till `PySpark`:
   ```yaml
   Framework: PySpark
   ```
   Gör inga ändringar i filen `myvm.compute`. Samma Docker-avbildning på samma virtuella dator används vid Spark-körningen. I den nya filen `myvm-spark.runconfig` pekar fältet `Target` på samma fil `myvm.compute` via namnet `myvm`.

5. Ange följande kommando för att köra skriptet **iris_spark.py** i Spark-instansen som körs i den fjärranslutna Docker-behållaren:
   ```azureli
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="run-scripts-in-hdinsight-clusters"></a>Köra skript i HDInsight-kluster
Du kan också köra skriptet i ett faktiskt HDInsight Spark-kluster. Lär dig [att skapa HDInsight Spark-kluster som ska användas i Azure ML Workbench](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal).

>[!NOTE] 
>HDInsight-klustret måste använda Azure Blob som primär lagring. Användning av Azure Data Lake-lagring stöds inte ännu.

1. Om du har tillgång till ett Spark for Azure HDInsight-kluster kan du generera ett kommando för konfiguration av HDInsight-körningen på det sätt som visas här. Ange HDInsight-klusternamnet samt ditt användarnamn och lösenord för HDInsight som parametrar. 

   Använd följande kommando för att skapa ett beräkningsmål som pekar på ett HDInsight-kluster:

   ```azurecli
   az ml computetarget attach cluster --name myhdi --address <cluster head node FQDN> --username <your-username> --password <your-password>
   ```

   Kör det här kommandot för att förbereda HDInsight-klustret:

   ```
   az ml experiment prepare -c myhdi
   ```

   Klusterhuvudnodens FQDN är vanligtvis `<your_cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >`username` är klustrets SSH-användarnamn och definieras under installationen av HDInsight. Som standard är värdet `sshuser`. Värdet är inte `admin`, som är den andra användaren som skapades under konfigurationen för att ge åtkomst till klustrets administrationswebbplats. 

2. Kör följande kommando för att köra skriptet **iris_spark.py** i HDInsight-klustret:

   ```azurecli
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >När du kör mot ett fjärranslutet HDInsight-kluster kan du även visa körningsinformation om YARN-jobbet (Yet Another Resource Negotiator) på `https://<your_cluster_name>.azurehdinsight.net/yarnui` med användarkontot `admin`.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg
I den andra delen av denna självstudiekurs i tre delar får du lära dig hur du gör följande:
> [!div class="checklist"]
> * Öppna skript och granska koden i Workbench
> * Köra skript i en lokal miljö
> * Granska körningshistorik
> * Köra skript i en lokal Docker-miljö

Nu kan du gå vidare till den tredje delen av denna självstudiekurs och lära dig distribuera den logistiska regressionsmodell som du har skapat som en webbtjänst i realtid.

> [!div class="nextstepaction"]
> [Självstudiekurs 3 – distribuera modeller](tutorial-classifying-iris-part-3.md)

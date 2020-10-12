---
title: Azure Stream Analytics integration med Azure Machine Learning
description: Den här artikeln beskriver hur du snabbt skapar ett enkelt Azure Stream Analytics jobb som integrerar Azure Machine Learning med hjälp av en användardefinierad funktion.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: 26a1208131f1d9d3df7dccd8e27bda37992f043f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88236688"
---
# <a name="do-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Gör sentiment-analys med Azure Stream Analytics och Azure Machine Learning Studio (klassisk)

Den här artikeln visar hur du konfigurerar ett enkelt Azure Stream Analytics jobb som använder Azure Machine Learning Studio (klassisk) för sentiment-analys. Du kan använda en Machine Learning sentiment Analytics-modell från Cortana Intelligence Gallery för att analysera strömmande text data och fastställa sentiment poäng.

> [!TIP]
> Vi rekommenderar starkt att du använder [Azure Machine Learning UDF: er](machine-learning-udf.md) i stället för Azure Machine Learning Studio (klassisk) UDF för bättre prestanda och tillförlitlighet.

Du kan använda det du lär dig i den här artikeln för scenarier som dessa:

* Analysera real tids sentiment på strömmande Twitter-data.
* Analysera poster för kund chattar med support personal.
* Utvärdera kommentarer om forum, Bloggar och videor.
* Många andra scenarier med förutsägande resultat i real tid.

Stream Analytics-jobbet som du skapar tillämpar sentiment Analytics-modellen som en användardefinierad funktion (UDF) på exempel text data från BLOB Store. Utdata (resultatet av sentiment-analysen) skrivs till samma blob-lagring i en annan CSV-fil. 

## <a name="prerequisites"></a>Förutsättningar

Se till att du har följande innan du börjar:

* En aktiv Azure-prenumeration.

* En CSV-fil med vissa Twitter-data. Du kan ladda ned en exempel fil från [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv), eller så kan du skapa en egen fil. I ett verkligt scenario kan du hämta data direkt från en Twitter-dataström.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Skapa en lagrings behållare och ladda upp CSV-indatafilen

I det här steget laddar du upp en CSV-fil till din lagrings behållare.

1. I Azure Portal väljer du **skapa ett resurs**  >  **lagrings**  >  **lagrings konto**.

2. Fyll i fliken *grundläggande* med följande information och lämna standardvärdena för de återstående fälten:

   |Field  |Värde  |
   |---------|---------|
   |Prenumeration|Välj din prenumeration.|
   |Resursgrupp|Välj din resurs grupp.|
   |Lagringskontonamn|Ange ett namn för lagringskontot. Namnet måste vara unikt i Azure.|
   |Plats|Välj en plats. Alla resurser bör använda samma plats.|
   |Typ av konto|BlobStorage|

   ![Ange information om lagrings konto](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

3. Välj **Granska + skapa**. Välj sedan **skapa** för att distribuera ditt lagrings konto.

4. När distributionen är klar navigerar du till ditt lagrings konto. Välj **containrar** under **Blob Service**. Välj sedan **+ container** för att skapa en ny behållare.

   ![Skapa Blob Storage-behållare för indata](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Ange ett namn för behållaren och kontrol lera att **offentlig åtkomst nivå** är inställd på **privat**. När du är färdig väljer du **Skapa**.

   ![Ange information om BLOB container](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. Navigera till den nyligen skapade behållaren och välj **Ladda upp**. Ladda upp **sampleinput.csv** -filen som du laddade ned tidigare.

   ![Knappen Ladda upp för en behållare](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Lägg till sentiment Analytics-modellen från Cortana Intelligence Gallery

Nu när exempel data finns i en BLOB kan du aktivera sentiment analys modell i Cortana Intelligence Gallery.

1. Gå till sidan för [förutsägelse sentiment Analytics-modellen](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) i Cortana Intelligence Gallery.  

2. Välj **Öppna i Studio (klassisk)**.  
   
   ![Stream Analytics Machine Learning, öppna Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Logga in för att gå till arbets ytan. Välj en plats.

4. Välj **Kör** längst ned på sidan. Processen körs, som tar ungefär en minut.

   ![köra experiment i Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. När processen har körts väljer du **distribuera webb tjänst** längst ned på sidan.

   ![distribuera experiment i Machine Learning Studio som en webb tjänst](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Om du vill kontrol lera att sentiment Analytics-modellen är redo att använda väljer du knappen **test** . Ange text ingångs värde, till exempel "Jag älskar Microsoft".

   ![testa experiment i Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

   Om testet fungerar ser du ett resultat som liknar följande exempel:

   ![test resultat i Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. I kolumnen **appar** väljer du länken **Excel 2010 eller föregående arbets bok** för att ladda ned en Excel-arbetsbok. Arbets boken innehåller API-nyckeln och den URL som du behöver senare för att kunna konfigurera Stream Analytics jobbet.

    ![Stream Analytics Machine Learning, snabbt och enkelt](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Skapa ett Stream Analytics jobb som använder Machine Learnings modellen

Nu kan du skapa ett Stream Analytics jobb som läser exempel-tweets från CSV-filen i Blob Storage.

### <a name="create-the-job"></a>Skapa jobbet

Gå till [Azure Portal](https://portal.azure.com) och skapa ett Stream Analytics jobb. Om du inte är bekant med den här processen kan du läsa [skapa ett Stream Analytics jobb med hjälp av Azure Portal](stream-analytics-quick-create-portal.md).

### <a name="configure-the-job-input"></a>Konfigurera jobb ingången

Jobbet hämtar indata från den CSV-fil som du överförde tidigare till Blob Storage.

1. Gå till Stream Analytics-jobbet. Under **jobb sto pol Ogin**väljer du alternativet **indata** . Välj **Lägg till Stream-indata**  > **Blob Storage**.

2. Fyll i **Blob Storage** information med följande värden:

   |Field  |Värde  |
   |---------|---------|
   |Inmatat alias|Ge dina inmatade namn. Kom ihåg det här aliaset när du skriver frågan.|
   |Prenumeration|Välj din prenumeration.|
   |Lagringskonto|Välj det lagrings konto som du gjorde i föregående steg.|
   |Container|Välj den behållare som du skapade i föregående steg.|
   |Händelseserialiseringsformat|CSV|

3. Välj **Spara**.

### <a name="configure-the-job-output"></a>Konfigurera jobbets utdata

Jobbet skickar resultat till samma blob-lagring där det matas in.

1. Gå till Stream Analytics-jobbet. Under **jobb sto pol Ogin**väljer du alternativet **utdata** . Välj **Lägg till**  >  **Blob Storage**.

2. Fyll i formuläret **Blob Storage** med följande värden:

   |Field  |Värde  |
   |---------|---------|
   |Inmatat alias|Ge dina inmatade namn. Kom ihåg det här aliaset när du skriver frågan.|
   |Prenumeration|Välj din prenumeration.|
   |Lagringskonto|Välj det lagrings konto som du gjorde i föregående steg.|
   |Container|Välj den behållare som du skapade i föregående steg.|
   |Händelseserialiseringsformat|CSV|

3. Välj **Spara**.

### <a name="add-the-machine-learning-function"></a>Lägg till funktionen Machine Learning

Tidigare publicerade du en Machine Learning-modell till en webb tjänst. I det här scenariot, när Stream-analys jobbet körs, skickas varje exempel tweet från indata till webb tjänsten för sentiment-analys. Machine Learning-webbtjänsten returnerar en sentiment ( `positive` , `neutral` eller `negative` ) och en sannolikhet för att tweeten ska vara positiv.

I det här avsnittet definierar du en funktion i Stream Analysis-jobbet. Funktionen kan anropas för att skicka en tweet till webb tjänsten och få tillbaka svaret.

1. Kontrol lera att du har webb tjänst-URL: en och den API-nyckel som du laddade ned tidigare i Excel-arbetsboken.

2. Gå till Stream Analytics-jobbet. Välj sedan **funktioner**  >  **+ Lägg till**  >  **Azure-ml Studio**

3. Fyll i formuläret för **Azure Machine Learning funktion** med följande värden:

   |Field  |Värde  |
   |---------|---------|
   | Funktions Ali Aset | Använd namnet `sentiment` och välj **Ange Azure Machine Learning funktions inställningar manuellt**, vilket ger dig ett alternativ för att ange URL och nyckel.      |
   | URL| Klistra in webb tjänstens URL.|
   |Tangent | Klistra in API-nyckeln. |

4. Välj **Spara**.

### <a name="create-a-query-to-transform-the-data"></a>Skapa en fråga för att transformera data

Stream Analytics använder en deklarativ, SQL-baserad fråga för att undersöka inmatade och bearbeta dem. I det här avsnittet skapar du en fråga som läser varje tweet från indatamängden och sedan anropar funktionen Machine Learning för att utföra sentiment-analys. Frågan skickar sedan resultatet till utdata som du har definierat (Blob Storage).

1. Gå tillbaka till Stream Analytics jobb översikt.

2. Under **jobb sto pol Ogin**väljer du **fråga**.

3. Ange följande fråga:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM <input>  
    )  

    SELECT text, result.[Score]  
    INTO <output>
    FROM sentiment  
    ```    

   Frågan anropar funktionen som du skapade tidigare ( `sentiment` ) för att utföra sentiment-analys på varje tweet i indatamängden.

4. Välj **Spara** för att spara frågan.

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Starta Stream Analytics-jobbet och kontrollera utdata

Nu kan du starta Stream Analytics jobbet.

### <a name="start-the-job"></a>Starta jobbet

1. Gå tillbaka till Stream Analytics jobb översikt.

2. Välj **Starta** överst på sidan.

3. I **Start jobb**väljer du **anpassad**och väljer sedan en dag innan du laddade upp CSV-filen till Blob Storage. När du är klar väljer du **Starta**.  

### <a name="check-the-output"></a>Kontrol lera utdata

1. Låt jobbet köras i några minuter tills du ser aktiviteten i rutan **övervakning** .

2. Om du har ett verktyg som du normalt använder för att undersöka innehållet i Blob Storage, använder du verktyget för att undersöka behållaren. Du kan också utföra följande steg i Azure Portal:

      1. I Azure Portal letar du reda på ditt lagrings konto och hittar behållaren i kontot. Du ser två filer i behållaren: filen som innehåller exempel-tweets och en CSV-fil som genereras av Stream Analyticss jobbet.
      2. Högerklicka på den genererade filen och välj sedan **Ladda ned**.

3. Öppna den genererade CSV-filen. Du ser något som liknar följande exempel:  

   ![Stream Analytics Machine Learning, CSV-vy](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

### <a name="view-metrics"></a>Visa mått

Du kan också Visa Azure Machine Learning Function-relaterade mått. Följande funktions relaterade mått visas i rutan **övervakning** i jobb översikten:

* **Funktions begär Anden** anger antalet begär Anden som skickats till en Machine Learning-webbtjänst.  
* **Funktions händelser** anger antalet händelser i begäran. Som standard innehåller varje begäran till en Machine Learning-webbtjänsten upp till 1 000 händelser.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Integrera REST API och Machine Learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)

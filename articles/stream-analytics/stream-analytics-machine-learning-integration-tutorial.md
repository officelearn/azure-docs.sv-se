---
title: Azure Stream Analytics integration med Azure Machine Learning
description: Den här artikeln beskriver hur du snabbt skapar ett enkelt Azure Stream Analytics jobb som integrerar Azure Machine Learning med hjälp av en användardefinierad funktion.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.custom: seodec18
ms.openlocfilehash: b33aeeee03fa57d87a60fd4c1904d5e4a86dd004
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80067079"
---
# <a name="perform-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Utföra sentiment-analys med Azure Stream Analytics och Azure Machine Learning Studio (klassisk)

Den här artikeln beskriver hur du snabbt skapar ett enkelt Azure Stream Analytics jobb som integrerar Azure Machine Learning Studio (klassisk). Du kan använda en Machine Learning sentiment Analytics-modell från Cortana Intelligence Gallery för att analysera strömmande text data och fastställa sentiment Poäng i real tid. Med hjälp av Cortana Intelligence Suite kan du utföra den här uppgiften utan att bekymra dig om erna för att skapa en sentiment Analytics-modell.

> [!TIP]
> Vi rekommenderar starkt att du använder [Azure Machine Learning UDF: er](machine-learning-udf.md) i stället för Azure Machine Learning Studio (klassisk) UDF för bättre prestanda och tillförlitlighet.

Du kan använda det du lär dig i den här artikeln för scenarier som dessa:

* Analysera real tids sentiment på strömmande Twitter-data.
* Analysera poster för kund chattar med support personal.
* Utvärdera kommentarer om forum, Bloggar och videor. 
* Många andra scenarier med förutsägande resultat i real tid.

I ett verkligt scenario kan du hämta data direkt från en Twitter-dataström. För att förenkla självstudien skrivs den så att Stream Analytics-jobbet får tweets från en CSV-fil i Azure Blob Storage. Du kan skapa en egen CSV-fil eller så kan du använda en exempel-CSV-fil, som du ser i följande bild:

![Exempel på Tweets som visas i en CSV-fil](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

Stream Analytics-jobbet som du skapar tillämpar sentiment Analytics-modellen som en användardefinierad funktion (UDF) på exempel text data från BLOB Store. Utdata (resultatet av sentiment-analysen) skrivs till samma blob-lagring i en annan CSV-fil. 

Följande bild visar den här konfigurationen. Ett mer realistiskt scenario är att du kan ersätta Blob Storage med strömmande Twitter-data från ett Azure Event Hubs-indata. Dessutom kan du bygga en [Microsoft Power BI](https://powerbi.microsoft.com/) real tids visualisering av den sammanställda sentiment.    

![Översikt över Stream Analytics Machine Learning-integrering](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Förutsättningar
Se till att du har följande innan du börjar:

* En aktiv Azure-prenumeration.
* En CSV-fil med vissa data. Du kan hämta filen som visas tidigare från [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv), eller så kan du skapa en egen fil. I den här artikeln förutsätts att du använder filen från GitHub.

För att kunna utföra de uppgifter som visas i den här artikeln på hög nivå gör du följande:

1. Skapa ett Azure Storage-konto och en Blob Storage-behållare och ladda upp en CSV-formaterad indatafil till behållaren.
3. Lägg till en sentiment Analytics-modell från Cortana Intelligence Gallery till din Azure Machine Learning Studio (klassisk) arbets yta och distribuera den här modellen som en webb tjänst i arbets ytan Machine Learning.
5. Skapa ett Stream Analytics jobb som anropar den här webb tjänsten som en funktion för att fastställa sentiment för text ingången.
6. Starta Stream Analytics jobb och kontrol lera utdata.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Skapa en lagrings behållare och ladda upp CSV-indatafilen
I det här steget kan du använda valfri CSV-fil, till exempel den som är tillgänglig från GitHub.

1. Klicka på **skapa ett resurs**  >  **lagrings**  >  **lagrings konto**i Azure Portal.

2. Ange ett namn ( `samldemo` i exemplet). Namnet får bara innehålla gemena bokstäver och siffror, och det måste vara unikt i Azure. 

3. Ange en befintlig resurs grupp och ange en plats. För plats rekommenderar vi att alla resurser som skapas i den här självstudien använder samma plats.

    ![Ange information om lagrings konto](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. I Azure Portal väljer du lagrings kontot. I bladet lagrings konto klickar du på **behållare** och sedan på ** + &nbsp; behållare** för att skapa Blob Storage.

    ![Skapa Blob Storage-behållare för indata](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Ange ett namn på behållaren ( `azuresamldemoblob` i exemplet) och kontrol lera att **åtkomst typen** är inställd på **BLOB**. Klicka på **OK** när du är klar.

    ![Ange information om BLOB container](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. På bladet **behållare** väljer du den nya behållaren som öppnar bladet för behållaren.

7. Klicka på **Överför**.

    ![Knappen Ladda upp för en behållare](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. På bladet **Ladda upp BLOB** laddar du upp **sampleinput.csvs** filen som du laddade ned tidigare. För **Blob-typ**väljer du **blockera BLOB** och ställer in block storleken på 4 MB, vilket är tillräckligt för den här självstudien.

9. Klicka på knappen **överför** längst ned på bladet.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Lägg till sentiment Analytics-modellen från Cortana Intelligence Gallery

Nu när exempel data finns i en BLOB kan du aktivera sentiment analys modell i Cortana Intelligence Gallery.

1. Gå till sidan för [förutsägelse sentiment Analytics-modellen](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) i Cortana Intelligence Gallery.  

2. Klicka på **Öppna i Studio**.  
   
   ![Stream Analytics Machine Learning, öppna Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Logga in för att gå till arbets ytan. Välj en plats.

4. Klicka på **Kör** längst ned på sidan. Processen körs, som tar ungefär en minut.

   ![köra experiment i Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. När processen har körts väljer du **distribuera webb tjänst** längst ned på sidan.

   ![distribuera experiment i Machine Learning Studio som en webb tjänst](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Om du vill kontrol lera att sentiment Analytics-modellen är redo att använda klickar du på knappen **test** . Ange text ingångs värde, till exempel "Jag älskar Microsoft". 

   ![testa experiment i Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Om testet fungerar ser du ett resultat som liknar följande exempel:

   ![test resultat i Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. I kolumnen **appar** klickar du på länken **Excel 2010 eller föregående arbets bok** för att ladda ned en Excel-arbetsbok. Arbets boken innehåller API-nyckeln och den URL som du behöver senare för att kunna konfigurera Stream Analytics jobbet.

    ![Stream Analytics Machine Learning, snabbt och enkelt](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Skapa ett Stream Analytics jobb som använder Machine Learnings modellen

Nu kan du skapa ett Stream Analytics jobb som läser exempel-tweets från CSV-filen i Blob Storage. 

### <a name="create-the-job"></a>Skapa jobbet

1. Gå till [Azure Portal](https://portal.azure.com).  

2. Klicka på **skapa en resurs**  >  **Sakernas Internet**  >  **Stream Analytics jobb**. 

3. Namnge jobbet `azure-sa-ml-demo` , ange en prenumeration, ange en befintlig resurs grupp eller skapa en ny och välj jobbets plats.

   ![Ange inställningar för nytt Stream Analytics jobb](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>Konfigurera jobb ingången
Jobbet hämtar indata från den CSV-fil som du överförde tidigare till Blob Storage.

1. När jobbet har skapats klickar du på alternativet **indata** under **jobb sto pol Ogin** på bladet jobb.    

2. På bladet **indata** klickar du på **Lägg till Stream input**  > **Blob Storage**

3. Fyll i bladet **Blob Storage** med följande värden:

   
   |Field  |Värde  |
   |---------|---------|
   |**Inmatat alias** | Använd namnet `datainput` och välj **Välj Blob Storage från din prenumeration**       |
   |**Lagringskonto**  |  Välj det lagringskonto som du skapade tidigare.  |
   |**Container**  | Välj den behållare som du skapade tidigare ( `azuresamldemoblob` )        |
   |**Händelseserialiseringsformat**  |  Välj **CSV**       |

   ![Inställningar för nya Stream Analytics jobb-ingångar](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Klicka på **Spara**.

### <a name="configure-the-job-output"></a>Konfigurera jobbets utdata
Jobbet skickar resultat till samma blob-lagring där det matas in. 

1. Under **jobb sto pol Ogin** på bladet jobb klickar du på alternativet **utdata** .  

2. Klicka på **Lägg till**blob-lagring i bladet **utdata**  > **Blob storage**och Lägg sedan till utdata med aliaset `datamloutput` . 

3. Fyll i bladet **Blob Storage** med följande värden:

   |Field  |Värde  |
   |---------|---------|
   |**Alias för utdata** | Använd namnet `datamloutput` och välj **Välj Blob Storage från din prenumeration**       |
   |**Lagringskonto**  |  Välj det lagringskonto som du skapade tidigare.  |
   |**Container**  | Välj den behållare som du skapade tidigare ( `azuresamldemoblob` )        |
   |**Händelseserialiseringsformat**  |  Välj **CSV**       |

   ![Inställningar för jobb med ny Stream Analytics utskrift](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Klicka på **Spara**.   


### <a name="add-the-machine-learning-function"></a>Lägg till funktionen Machine Learning 
Tidigare publicerade du en Machine Learning-modell till en webb tjänst. I det här scenariot, när Stream-analys jobbet körs, skickas varje exempel tweet från indata till webb tjänsten för sentiment-analys. Machine Learning-webbtjänsten returnerar en sentiment ( `positive` , `neutral` eller `negative` ) och en sannolikhet för att tweeten ska vara positiv. 

I det här avsnittet av självstudien definierar du en funktion i Stream Analysis-jobbet. Funktionen kan anropas för att skicka en tweet till webb tjänsten och få tillbaka svaret. 

1. Kontrol lera att du har webb tjänst-URL: en och den API-nyckel som du laddade ned tidigare i Excel-arbetsboken.

2. Navigera till bladet med jobb > **funktioner**  >  **+ Lägg till**  >  **azureml**

3. Fyll i bladet med **Azure Machine Learning funktionen** med följande värden:

   |Field  |Värde  |
   |---------|---------|
   | **Funktions Ali Aset** | Använd namnet `sentiment` och välj **Ange Azure Machine Learning funktions inställningar manuellt** , vilket ger dig ett alternativ för att ange URL och nyckel.      |
   | **URL**| Klistra in webb tjänstens URL.|
   |**Nyckel** | Klistra in API-nyckeln. |
  
   ![Inställningar för att lägga till Machine Learning funktion i Stream Analytics jobb](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Klicka på **Spara**.

### <a name="create-a-query-to-transform-the-data"></a>Skapa en fråga för att transformera data

Stream Analytics använder en deklarativ, SQL-baserad fråga för att undersöka inmatade och bearbeta dem. I det här avsnittet skapar du en fråga som läser varje tweet från indatamängden och sedan anropar funktionen Machine Learning för att utföra sentiment-analys. Frågan skickar sedan resultatet till utdata som du har definierat (Blob Storage).

1. Gå tillbaka till bladet jobb översikt.

2.  Under **jobb sto pol Ogin**klickar du på rutan **fråga** .

3. Ange följande fråga:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM datainput  
    )  

    SELECT text, result.[Score]  
    INTO datamloutput
    FROM sentiment  
    ```    

    Frågan anropar funktionen som du skapade tidigare ( `sentiment` ) för att utföra sentiment-analys på varje tweet i indatamängden. 

4. Spara frågan genom att klicka på **Spara**.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Starta Stream Analytics-jobbet och kontrollera utdata

Nu kan du starta Stream Analytics jobbet.

### <a name="start-the-job"></a>Starta jobbet
1. Gå tillbaka till bladet jobb översikt.

2. Klicka på **Starta** överst på bladet.

3. I **Start jobbet**väljer du **anpassad**och väljer sedan en dag innan du laddade upp CSV-filen till Blob Storage. När du är klar klickar du på **Starta**.  


### <a name="check-the-output"></a>Kontrol lera utdata
1. Låt jobbet köras i några minuter tills du ser aktiviteten i rutan **övervakning** . 

2. Om du har ett verktyg som du normalt använder för att undersöka innehållet i Blob Storage, använder du verktyget för att undersöka `azuresamldemoblob` behållaren. Du kan också utföra följande steg i Azure Portal:

    1. Leta reda på `samldemo` lagrings kontot i portalen och leta upp behållaren i kontot `azuresamldemoblob` . Du ser två filer i behållaren: filen som innehåller exempel-tweets och en CSV-fil som genereras av Stream Analyticss jobbet.
    2. Högerklicka på den genererade filen och välj sedan **Ladda ned**. 

   ![Ladda ned CSV-jobbets utdata från Blob Storage](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Öppna den genererade CSV-filen. Du ser något som liknar följande exempel:  
   
   ![Stream Analytics Machine Learning, CSV-vy](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Visa mått
Du kan också Visa Azure Machine Learning Function-relaterade mått. Följande funktions relaterade mått visas i rutan **övervakning** på bladet jobb:

* **Funktions begär Anden** anger antalet begär Anden som skickats till en Machine Learning-webbtjänst.  
* **Funktions händelser** anger antalet händelser i begäran. Som standard innehåller varje begäran till en Machine Learning-webbtjänsten upp till 1 000 händelser.  


## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Integrera REST API och Machine Learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)




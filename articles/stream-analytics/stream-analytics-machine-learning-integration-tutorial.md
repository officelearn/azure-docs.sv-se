---
title: Azure Stream Analytics-integrering med Azure Machine Learning
description: Den här artikeln beskriver hur du snabbt konfigurera ett enkelt Azure Stream Analytics-jobb som integrerar Azure Machine Learning, med hjälp av en användardefinierad funktion.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: b06fec8ab726f48e1937bae4cfbdbd9842788d0a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58109315"
---
# <a name="performing-sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Utföra attitydanalyser genom att använda Azure Stream Analytics och Azure Machine Learning
Den här artikeln beskriver hur du snabbt konfigurera ett enkelt Azure Stream Analytics-jobb som integrerar Azure Machine Learning. Du kan använda en Machine Learning sentiment analysmodell från Cortana Intelligence Gallery att analysera textdata och avgöra sentimentresultatet i realtid. Med Cortana Intelligence Suite kan du utföra den här uppgiften utan att oroa krångla för att skapa en sentiment analysmodell.

Du kan använda vad du lär dig från den här artikeln att scenarier som följande:

* Analys i realtid attitydanalys på strömmad Twitter-data.
* Analysera poster i kundens chattar med supportpersonalen.
* Utvärdera kommentarer på forum, bloggar och videor. 
* Många andra i realtid, förutsägande bedömnings scenarier.

I ett verkligt scenario skulle du få data direkt från en Twitter-dataström. För att förenkla självstudien skrivs den så att Streaming Analytics-jobbet hämtar tweets från en CSV-fil i Azure Blob storage. Du kan skapa egna CSV-fil eller använda en exempel-CSV-fil som du ser i följande bild:

![Exemplet tweets som visas i en CSV-fil](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

Streaming Analytics-jobbet som du skapar tillämpar analysmodell sentiment som en användardefinierad funktion (UDF) på text exempeldata från blob store. Utdata (resultat av attitydanalys) skrivs till samma blob store i en annan CSV-fil. 

Följande bild visar den här konfigurationen. Enligt vad som anges för ett mer realistiskt scenario kan du ersätta blob-lagring med strömning Twitter-data från Azure Event Hubs indata. Dessutom kan du bygga en [Microsoft Power BI](https://powerbi.microsoft.com/) visualisering av sammanställda åsikter i realtid.    

![Översikt över integration av Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Förutsättningar
Se till att du har följande innan du börjar:

* En aktiv Azure-prenumeration.
* En CSV-fil med vissa data i den. Du kan hämta den fil som visades tidigare från [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv), eller så kan du skapa en egen fil. I den här artikeln förutsätts att du använder filen från GitHub.

På en hög nivå för att slutföra de uppgifter som visas i den här artikeln får göra du följande:

1. Skapa ett Azure storage-konto och ett blob storage-behållare och överför en CSV-formaterad indatafilen till behållaren.
3. Lägg till en sentiment analysmodell från Cortana Intelligence-galleriet till din Azure Machine Learning-arbetsyta och distribuera den här modellen som en webbtjänst i Machine Learning-arbetsytan.
5. Skapa ett Stream Analytics-jobb som anropar den här webbtjänsten som en funktion för att kunna fastställa sentiment för texten som indata.
6. Starta Stream Analytics-jobbet och kontrollera utdata.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Skapa en lagringsbehållare och ladda upp en CSV-indatafil
För det här steget ska använda du valfri CSV-fil, till exempel det som är tillgängliga från GitHub.

1. I Azure-portalen klickar du på **skapa en resurs** > **Storage** > **lagringskonto**.

2. Ange ett namn (`samldemo` i det här exemplet). Namnet kan använda bara gemena bokstäver och siffror det måste vara unikt i Azure. 

3. Ange en befintlig resursgrupp och ange en plats. För plats rekommenderar vi att alla resurser som skapats i den här självstudien använder samma plats.

    ![Ange information om lagringskonto](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. Välj lagringskontot i Azure-portalen. I bladet storage-konto klickar du på **behållare** och klicka sedan på  **+ &nbsp;behållare** att skapa blob-lagring.

    ![Skapa blob storage-behållare för indata](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Ange ett namn för behållaren (`azuresamldemoblob` i det här exemplet) och kontrollera att **åtkomsttyp** är inställd på **Blob**. Klicka på **OK** när du är klar.

    ![Ange information om blob-behållare](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. I den **behållare** bladet välj ny behållare, vilket öppnar bladet för den behållaren.

7. Klicka på **Överför**.

    ![Överför-knappen för en behållare](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. I den **ladda upp blob** bladet, ladda upp den **sampleinput.csv** filen som du hämtade tidigare. För **typ av Blob**väljer **blockblob** och ange blockstorleken till 4 MB, vilket är tillräckligt för den här självstudien.

9. Klicka på den **överför** längst ned på bladet.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Lägg till analysmodell sentiment från Cortana Intelligence-galleriet

Nu när exempeldata är i en blob kan aktivera du sentiment analysmodell i Cortana Intelligence-galleriet.

1. Gå till den [åsikter i förutsägande analysmodell](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) sidan i Cortana Intelligence-galleriet.  

2. Klicka på **öppna i Studio**.  
   
   ![Stream Analytics Machine Learning, öppna Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Gå till arbetsytan genom att logga in. Välj en plats.

4. Klicka på **kör** längst ned på sidan. Processen körs som tar ungefär en minut.

   ![Kör experiment i Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. När processen har körts, väljer **distribuera webbtjänsten** längst ned på sidan.

   ![distribuera experiment i Machine Learning Studio som en webbtjänst](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Verifiera att sentiment analysmodell är redo att använda, klicka på den **Test** knappen. Ange textinmatning som ”jag älskar Microsoft”. 

   ![Testa experiment i Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Om testet fungerar, kan du se ett resultat liknande exemplen nedan:

   ![testresultat i Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. I den **appar** kolumnen, klickar du på den **Excel 2010 eller tidigare arbetsbok** länken för att hämta en Excel-arbetsbok. Arbetsboken innehåller API-nyckeln och den URL som du senare behöver du ställer in ett Stream Analytics-jobb.

    ![Stream Analytics Machine Learning, snabböversikten](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Skapa ett Stream Analytics-jobb som använder Machine Learning-modell

Du kan nu skapa ett Stream Analytics-jobb som läser exempel tweets från CSV-fil i blob storage. 

### <a name="create-the-job"></a>Skapa jobbet

1. Gå till [Azure-portalen](https://portal.azure.com).  

2. Klicka på **skapa en resurs** > **Internet of Things** > **Stream Analytics-jobbet**. 

3. Namnge jobbet `azure-sa-ml-demo`, ange en prenumeration, ange en befintlig resursgrupp eller skapa en ny och välj en plats för jobbet.

   ![Ange inställningar för nytt Stream Analytics-jobb](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>Konfigurera jobbindata
Jobbet hämtar indata från CSV-filen som du tidigare laddade upp till blob storage.

1. När jobbet har skapats under **Jobbtopologi** jobb-bladet klickar du på den **indata** alternativet.    

2. I den **indata** bladet klickar du på **lägga till Stream-indata** >**Blob-lagring**

3. Fyll i **Blob Storage** bladet med följande värden:

   
   |Fält  |Värde  |
   |---------|---------|
   |**Inmatat alias** | Använd namnet `datainput` och välj **Välj blob-lagring från prenumerationen**       |
   |**Lagringskonto**  |  Välj det lagringskonto du skapade tidigare.  |
   |**Behållare**  | Markera den behållare som du skapade tidigare (`azuresamldemoblob`)        |
   |**Händelseserialiseringsformat**  |  Välj **CSV**       |

   ![Inställningar för nya Stream Analytics-jobbindata](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Klicka på **Spara**.

### <a name="configure-the-job-output"></a>Konfigurera jobbutdata
Jobbet skickar resultaten till samma blob storage där den hämtar indata. 

1. Under **Jobbtopologi** jobb-bladet klickar du på den **utdata** alternativet.  

2. I den **utdata** bladet klickar du på **Lägg till** >**Blob-lagring**, och Lägg sedan till utdata med aliaset `datamloutput`. 

3. Fyll i **Blob Storage** bladet med följande värden:

   |Fält  |Värde  |
   |---------|---------|
   |**Utdataalias** | Använd namnet `datamloutput` och välj **Välj blob-lagring från prenumerationen**       |
   |**Lagringskonto**  |  Välj det lagringskonto du skapade tidigare.  |
   |**Behållare**  | Markera den behållare som du skapade tidigare (`azuresamldemoblob`)        |
   |**Händelseserialiseringsformat**  |  Välj **CSV**       |

   ![Inställningar för nya Stream Analytics-jobbutdata](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Klicka på **Spara**.   


### <a name="add-the-machine-learning-function"></a>Lägg till Machine Learning-funktion 
Tidigare publicerat du en Machine Learning-modell till en webbtjänst. I det här scenariot när Stream Analysis-jobbet körs skickas varje exempel tweet från indata till webbtjänsten för attitydanalys. Machine Learning-webbtjänst returnerar en sentiment (`positive`, `neutral`, eller `negative`) och en sannolikhet till den tweet som positivt. 

I det här avsnittet av självstudiekursen definierar du en funktion i Stream Analysis-jobbet. Funktionen kan anropas för att skicka en tweet till webbtjänsten och få ett svar. 

1. Kontrollera att du har den web service URL och API-nyckeln som du hämtade tidigare i Excel-arbetsboken.

2. Navigera till bladet för jobbet > **Functions** > **+ Lägg till** > **AzureML**

3. Fyll i **Azure Machine Learning-funktion** bladet med följande värden:

   |Fält  |Värde  |
   |---------|---------|
   | **Funktionsalias** | Använd namnet `sentiment` och välj **ger Azure Machine Learning-funktionsinställningar manuellt** som ger dig möjlighet att ange URL och nyckel.      |
   | **URL**| Klistra in URL för webbtjänsten.|
   |**Nyckel** | Klistra in API-nyckeln. |
  
   ![Inställningar för att lägga till Machine Learning-funktionen i Stream Analytics-jobb](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Klicka på **Spara**.

### <a name="create-a-query-to-transform-the-data"></a>Skapa en fråga för att omvandla data

Stream Analytics använder en deklarativt, SQL-baserad fråga för att undersöka indata och bearbeta den. I det här avsnittet skapar du en fråga som läser varje tweet från indata och anropar sedan funktionen Machine Learning för att utföra känsloanalys. Frågan kan du sedan skickar resultatet till utdata som du angav (blob storage).

1. Återgå till jobböversiktsbladet.

2.  Under **Jobbtopologi**, klickar du på den **fråga** box.

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

    Frågan anropar funktionen som du skapade tidigare (`sentiment`) för att kunna utföra attitydanalyser på varje tweet i indata. 

4. Klicka på **spara** att spara frågan.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Starta Stream Analytics-jobbet och kontrollera utdata

Du kan nu starta Stream Analytics-jobbet.

### <a name="start-the-job"></a>Starta jobbet
1. Återgå till jobböversiktsbladet.

2. Klicka på **starta** överst på bladet.

3. I den **startjobb**väljer **anpassade**, och välj sedan en dag före då du laddade upp CSV-filen till blob storage. När du är klar klickar du på **starta**.  


### <a name="check-the-output"></a>Kontrollera utdata
1. Låt jobbkörning för några minuter tills du ser att aktiviteten i den **övervakning** box. 

2. Om du har ett verktyg som du vanligtvis använder för att granska innehållet i blob-lagring, Använd den för att undersöka den `azuresamldemoblob` behållare. Du kan också göra följande steg i Azure portal:

    1. I portalen, hitta den `samldemo` storage-kontot och i kontot, hitta den `azuresamldemoblob` behållare. Du ser två filer i behållaren: den fil som innehåller exempel tweets och en CSV-fil som genererats av Stream Analytics-jobb.
    2. Högerklicka på den genererade filen och välj sedan **hämta**. 

   ![Ladda ned CSV jobbutdata från Blob storage](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Öppna den genererade CSV-filen. Det se ut ungefär som i följande exempel:  
   
   ![Stream Analytics Machine Learning, CSV-vy](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Visa mått
Du kan också visa Azure Machine Learning-funktion-relaterade mått. Följande funktion-relaterade mått visas i den **övervakning** rutan på bladet jobb:

* **Funktionen begäranden** anger antalet begäranden som skickas till en Machine Learning-webbtjänst.  
* **Funktionen händelser** anger antalet händelser i begäran. Som standard innehåller varje begäran till en Machine Learning-webbtjänst upp till 1 000 händelser.  


## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Integrera REST API och Machine Learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)




---
title: Azure Stream Analytics och Machine Learning-integrering | Microsoft Docs
description: "Hur du använder en användardefinierad funktion och Machine Learning i ett Stream Analytics-jobb"
keywords: 
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: jhubbard
editor: cgronlun
ms.assetid: cfced01f-ccaa-4bc6-81e2-c03d1470a7a2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/01/2018
ms.author: sngun
ms.openlocfilehash: 10d514aeb50dcd24f28ed879875b23b25578cebb
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="performing-sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Utföra sentiment analys med hjälp av Azure Stream Analytics och Azure Machine Learning
Den här artikeln beskriver hur du snabbt ställa in ett enkelt Azure Stream Analytics-jobb som integrerar Azure Machine Learning. Du använder en Machine Learning sentiment analytics modell från Cortana Intelligence Gallery analysera strömmande textdata och fastställa sentiment poäng i realtid. Med hjälp av Cortana Intelligence Suite kan du utföra den här uppgiften utan att oroa av bygger en sentiment analytics modell.

Du kan använda det du lära dig från den här artikeln för scenarier som dessa:

* Analysera realtid sentiment direktuppspelning Twitter-data.
* Analysera poster kunden chattar med supportpersonalen.
* Utvärderar kommentarer om forum, bloggar och videor. 
* Många andra realtid, förutsägbara bedömningsprofil scenarier.

I ett verkligt scenario kan du hämta data direkt från en Twitter-dataström. För att förenkla kursen, har vi skrivs den så att Streaming Analytics-jobbet hämtar tweets från en CSV-fil i Azure Blob storage. Du kan skapa egna CSV-fil eller du kan använda en exempel-CSV-fil som visas i följande bild:

![exempel tweets i en CSV-fil](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

Streaming Analytics-jobbet som du skapar gäller sentiment analytics modellen som en användardefinierad funktion (UDF) på text exempeldata från blobstore. Utdata (resultatet av analysen sentiment) skrivs till samma blobstore i en annan CSV-fil. 

Följande bild visar den här konfigurationen. Som anges för en mer realistisk scenario ersätta du blob-lagring med streaming Twitter-data från Azure Event Hubs indata. Dessutom kan du bygga en [Microsoft Power BI](https://powerbi.microsoft.com/) realtid visualisering av sammanställda sentiment.    

![Stream Analytics Machine Learning-integration: översikt](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar bör du kontrollera att du har följande:

* En aktiv Azure-prenumeration.
* En CSV-fil med vissa data i den. Du kan ladda ned filen som visades tidigare från [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv), eller skapa en egen fil. För den här artikeln förutsätter vi att du använder filen från GitHub.

På en hög nivå för att slutföra de uppgifter som visas i den här artikeln får göra du följande:

1. Skapa ett Azure storage-konto och en behållare för blob storage och överför en CSV-formaterad indatafilen till behållaren.
3. Lägga till en sentiment analytics modell från Cortana Intelligence Gallery i Azure Machine Learning-arbetsytan och distribuera den här modellen som en webbtjänst i Machine Learning-arbetsytan.
5. Skapa ett Stream Analytics-jobb som anropar den här webbtjänsten som en funktion för att fastställa sentiment för den text som indata.
6. Starta Stream Analytics-jobbet och kontrollera utdata.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Skapa en lagringsbehållare och ladda upp CSV indatafilen
Du kan använda CSV-fil, till exempel en tillgänglig från GitHub för det här steget.

1. I Azure-portalen klickar du på **skapar du en resurs** > **lagring** > **lagringskonto**.

2. Ange ett namn (`samldemo` i exemplet). Namnet kan använda bara gemena bokstäver och siffror, och den måste vara unikt i Azure. 

3. Ange en befintlig resursgrupp och ange en plats. För plats rekommenderar vi att alla resurser som skapats i den här kursen använder samma plats.

    ![Ange information om lagringskonto](./media/stream-analytics-machine-learning-integration-tutorial/create-sa1.png)

4. Välj lagringskonto i Azure-portalen. I bladet storage-konto klickar du på **behållare** och klicka sedan på ** + &nbsp;behållare** att skapa blob-lagring.

    ![Skapa blob-behållare](./media/stream-analytics-machine-learning-integration-tutorial/create-sa2.png)

5. Ange ett namn för behållaren (`azuresamldemoblob` i exemplet) och kontrollera att **komma åt typen** är inställd på **Blob**. Klicka på **OK** när du är klar.

    ![Ange information om blob-behållare](./media/stream-analytics-machine-learning-integration-tutorial/create-sa3.png)

6. I den **behållare** bladet välj ny behållare, vilket öppnar bladet för behållaren.

7. Klicka på **Överför**.

    ![Överför knapp för en behållare](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. I den **överför blob** bladet, överför den **sampleinput.csv** filen som du hämtade tidigare. För **Blob typen**väljer **blockblob** och Ställ in blockstorleken på 4 MB, vilket är tillräcklig för den här kursen.

9. Klicka på den **överför** längst ned på bladet.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Lägg till sentiment analytics modellen från Cortana Intelligence Gallery

Nu när exempeldata i en blob, kan du aktivera sentiment analysmodell i Cortana Intelligence Gallery.

1. Gå till den [förutsägande sentiment analytics modellen](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) sida i Cortana Intelligence Gallery.  

2. Klicka på **öppna i Studio**.  
   
   ![Strömma Analytics Machine Learning, öppna Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Logga in på Gå till arbetsytan. Välj en plats.

4. Klicka på **kör** längst ned på sidan. Processen körs som tar ungefär en minut.

   ![Kör experiment i Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. När processen har körts, Välj **distribuera webbtjänsten** längst ned på sidan.

   ![distribuera experiment i Machine Learning Studio som en webbtjänst](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Om du vill verifiera att sentiment analytics modellen är klar att använda, klickar du på den **Test** knappen. Ange text som indata som ”jag älskar Microsoft”. 

   ![Testa experiment i Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Om testet fungerar, visas ett resultat som liknar följande exempel:

   ![testresultaten i Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. I den **appar** kolumn, och klicka på den **Excel 2010 eller tidigare arbetsboken** länk för att hämta en Excel-arbetsbok. Arbetsboken innehåller den en API-nyckel och den URL som du senare måste du konfigurera Stream Analytics-jobbet.

    ![Stream Analytics Machine Learning, snabböversikten](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Skapa ett Stream Analytics-jobb som använder Machine Learning-modellen

Du kan nu skapa ett Stream Analytics-jobb som läser exempel tweets från CSV-fil i blob storage. 

### <a name="create-the-job"></a>Skapa jobbet

1. Gå till [Azure-portalen](https://portal.azure.com).  

2. Klicka på **skapar du en resurs** > **Sakernas Internet** > **Stream Analytics-jobbet**. 

3. Namnge jobbet `azure-sa-ml-demo`, ange en prenumeration, ange en befintlig resursgrupp eller skapa en ny och välj en plats för jobbet.

   ![Ange inställningar för nya Stream Analytics-jobbet](./media/stream-analytics-machine-learning-integration-tutorial/create-job-1.png)
   

### <a name="configure-the-job-input"></a>Konfigurera jobbet indata
Jobbet hämtar indata från CSV-fil som du tidigare har överförts till blob storage.

1. När jobbet har skapats under **jobbet topologi** i jobb-bladet klickar du på den **indata** alternativet.    

2. I den **indata** bladet, klickar du på **lägga till Strömindata** >**Blob storage**

3. Fyll i den **Blob Storage** bladet med dessa värden:

   
   |Fält  |Värde  |
   |---------|---------|
   |**Ett inmatat alias** | Använd namn `datainput` och välj **Välj blob storage från prenumerationen**       |
   |**Lagringskonto**  |  Välj lagringskontot som du skapade tidigare.  |
   |**Behållaren**  | Markera den behållare som du skapade tidigare (`azuresamldemoblob`)        |
   |**Händelsen serialiseringsformat**  |  Välj **CSV**       |

   ![Inställningar för nya jobb indata](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

4. Klicka på **Spara**.

### <a name="configure-the-job-output"></a>Konfigurera jobbutdata
Jobbet skickar resultaten till samma blob storage där den hämtar indata. 

1. Under **jobbet topologi** i jobb-bladet klickar du på den **utdata** alternativet.  

2. I den **utdata** bladet, klickar du på **Lägg till** >**Blob storage**, och sedan lägga till utdata med aliaset `datamloutput`. 

3. Fyll i den **Blob Storage** bladet med dessa värden:

   |Fält  |Värde  |
   |---------|---------|
   |**Kolumnalias** | Använd namn `datainput` och välj **Välj blob storage från prenumerationen**       |
   |**Lagringskonto**  |  Välj lagringskontot som du skapade tidigare.  |
   |**Behållaren**  | Markera den behållare som du skapade tidigare (`azuresamldemoblob`)        |
   |**Händelsen serialiseringsformat**  |  Välj **CSV**       |

   ![Inställningar för nya jobbutdata](./media/stream-analytics-machine-learning-integration-tutorial/create-output2.png) 

4. Klicka på **Spara**.   


### <a name="add-the-machine-learning-function"></a>Lägg till Machine Learning-funktion 
Tidigare publicerade du en Machine Learning-modell till en webbtjänst. I vårt scenario, när dataströmmen analys jobbet körs skickas varje prov tweet från angivna indata till webbtjänsten för sentiment analys. Webbtjänsten för Machine Learning returnerar en sentiment (`positive`, `neutral`, eller `negative`) och sannolikhet för tweet vara positivt. 

I det här avsnittet av kursen definierar du en funktion i dataströmmen Analysis-jobbet. Funktionen kan anropas för att skicka en tweet till webbtjänsten och hämta svaret tillbaka. 

1. Kontrollera att du har den web service URL: en och API-nyckeln som du hämtade tidigare i Excel-arbetsboken.

2. Gå till jobb-bladet > **funktioner** > **+ Lägg till** > **AzureML**

3. Fyll i den **Azure Machine Learning-funktionen** bladet med dessa värden:

   |Fält  |Värde  |
   |---------|---------|
   | **Funktionen alias** | Använd namn `sentiment` och välj **ger Azure Machine Learning-funktionsinställningar manuellt** som ger dig ett alternativ för att ange URL och nyckel.      |
   | **URL**| Klistra in URL för webbtjänsten.|
   |**Nyckel** | Klistra in API-nyckeln. |
  
   ![Inställningar för att lägga till en Machine Learning-funktionen i Stream Analytics-jobbet](./media/stream-analytics-machine-learning-integration-tutorial/add-function.png)  
    
4. Klicka på **Spara**.

### <a name="create-a-query-to-transform-the-data"></a>Skapa en fråga för att omvandla data

Stream Analytics använder en deklarativ, SQL-baserade frågan för att kontrollera indata och bearbetar den. I det här avsnittet skapar du en fråga som läser varje tweet från indata och anropar sedan Machine Learning-funktionen för att utföra analyser av sentiment. Frågan kan du sedan skickar resultatet till utdata som du definierat (blobblagring).

1. Gå tillbaka till bladet jobbet Översikt.

2.  Under **jobbet topologi**, klicka på den **frågan** rutan.

3. Ange följande fråga:

    ```
    WITH sentiment AS (  
    SELECT text, sentiment(text) as result from datainput  
    )  

    Select text, result.[Score]  
    Into datamloutput
    From sentiment  
    ```    

    Frågan anropar funktionen som du skapade tidigare (`sentiment`) för att kunna utföra sentiment analys på varje tweet i indata. 

4. Klicka på **spara** att spara frågan.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Starta Stream Analytics-jobbet och kontrollera utdata

Du kan nu starta Stream Analytics-jobbet.

### <a name="start-the-job"></a>Starta jobbet
1. Gå tillbaka till bladet jobbet Översikt.

2. Klicka på **starta** längst upp på bladet.

3. I den **startjobb**väljer **anpassade**, och välj sedan en dag före när du har överfört CSV-filen till blob storage. När du är klar klickar du på **starta**.  


### <a name="check-the-output"></a>Kontrollera utdata
1. Att köra några minuter tills du ser aktivitet i jobbet den **övervakning** rutan. 

2. Om du har ett verktyg som du vanligtvis använder för att undersöka innehållet i blob-lagring, Använd den för att undersöka den `azuresamldemoblob` behållare. Du kan också göra följande steg i Azure-portalen:

    1. I portalen, söka efter den `samldemo` lagring kontot och i kontot, hitta den `azuresamldemoblob` behållare. Du ser två filer i behållaren: filen som innehåller exempel tweets och en CSV-fil som genererats av Stream Analytics-jobbet.
    2. Högerklicka på filen genererade och välj sedan **hämta**. 

   ![Ladda ned CSV jobbutdata från Blob storage](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Öppna den genererade CSV-filen. Du ser något som liknar följande exempel:  
   
   ![Visa Stream Analytics, Machine Learning, CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Visa mått
Du kan också visa Azure Machine Learning-funktionen-relaterade mått. Följande funktion-relaterade mått visas i den **övervakning** rutan i bladet jobb:

* **Funktionen begäranden** anger antalet förfrågningar som skickas till en Machine Learning-webbtjänst.  
* **Funktionen händelser** anger antalet händelser i begäran. Som standard innehåller varje begäran till en Machine Learning-webbtjänst upp till 1 000 händelser.  


## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Integrera REST-API och Maskininlärning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)




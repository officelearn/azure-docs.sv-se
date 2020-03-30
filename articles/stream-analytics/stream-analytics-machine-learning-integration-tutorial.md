---
title: Azure Stream Analytics-integrering med Azure Machine Learning
description: I den här artikeln beskrivs hur du snabbt konfigurerar ett enkelt Azure Stream Analytics-jobb som integrerar Azure Machine Learning med hjälp av en användardefinierad funktion.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.custom: seodec18
ms.openlocfilehash: b33aeeee03fa57d87a60fd4c1904d5e4a86dd004
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067079"
---
# <a name="perform-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Utföra sentimentanalys med Azure Stream Analytics och Azure Machine Learning Studio (klassisk)

I den här artikeln beskrivs hur du snabbt konfigurerar ett enkelt Azure Stream Analytics-jobb som integrerar Azure Machine Learning Studio (klassisk). Du använder en machine learning sentiment analytics-modell från Cortana Intelligence Gallery för att analysera strömmande textdata och bestämma sentimentpoängen i realtid. Med hjälp av Cortana Intelligence Suite kan du utföra denna uppgift utan att oroa dig för krångligheter att bygga en sentimentanalysmodell.

> [!TIP]
> Vi rekommenderar starkt att du använder [Azure Machine Learning UDFs](machine-learning-udf.md) i stället för Azure Machine Learning Studio (klassisk) UDF för förbättrad prestanda och tillförlitlighet.

Du kan använda det du lär dig av den här artikeln på scenarier som dessa:

* Analysera sentiment i realtid på strömmande Twitter-data.
* Analysera register över kundchattar med supportpersonal.
* Utvärdera kommentarer på forum, bloggar och videor. 
* Många andra scenarier för förutsägande bedömning i realtid.

I ett verkligt scenario, skulle du få data direkt från en Twitter dataström. För att förenkla självstudien skrivs den så att Streaming Analytics-jobbet får tweets från en CSV-fil i Azure Blob-lagring. Du kan skapa en egen CSV-fil eller använda en CSV-exempelfil, som visas i följande bild:

![Exempel på tweets som visas i en CSV-fil](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

Det Streaming Analytics-jobb som du skapar tillämpar sentimentanalysmodellen som en användardefinierad funktion (UDF) på exempeltextdata från blob-arkivet. Utdata (resultatet av sentimentanalysen) skrivs till samma blob-arkivet i en annan CSV-fil. 

Följande bild visar den här konfigurationen. Som nämnts, för ett mer realistiskt scenario, kan du ersätta blob lagring med strömmande Twitter-data från en Azure Event Hubs indata. Dessutom kan du skapa en [Microsoft Power BI-visualisering](https://powerbi.microsoft.com/) i realtid av den samlade sentimentet.    

![Översikt över integrering av Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Krav
Se till att du har följande innan du börjar:

* En aktiv Azure-prenumeration.
* En CSV-fil med vissa data i den. Du kan hämta filen som visas tidigare från [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)eller skapa en egen fil. I den här artikeln antas det att du använder filen från GitHub.

På en hög nivå, för att slutföra de uppgifter som visas i den här artikeln, gör du följande:

1. Skapa ett Azure-lagringskonto och en blob-lagringsbehållare och ladda upp en CSV-formaterad indatafil till behållaren.
3. Lägg till en sentimentanalysmodell från Cortana Intelligence Gallery i din klassiska Azure Machine Learning Studio-arbetsyta och distribuera den här modellen som en webbtjänst på arbetsytan Machine Learning.
5. Skapa ett Stream Analytics-jobb som anropar den här webbtjänsten som en funktion för att fastställa sentimentet för textinmatningen.
6. Starta Stream Analytics-jobbet och kontrollera utdata.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Skapa en lagringsbehållare och ladda upp CSV-indatafilen
I det här steget kan du använda valfri CSV-fil, till exempel den som är tillgänglig från GitHub.

1. Klicka på Skapa **ett** > **resurslagringslagringskonto** > **Storage account**i Azure-portalen .

2. Ange ett`samldemo` namn ( i exemplet). Namnet kan bara använda gemener och siffror, och det måste vara unikt i Azure. 

3. Ange en befintlig resursgrupp och ange en plats. För plats rekommenderar vi att alla resurser som skapas i den här självstudien använder samma plats.

    ![tillhandahålla information om lagringskonto](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. Välj lagringskontot i Azure-portalen. Klicka på **Behållare** i lagringskontobladet och klicka sedan på ** + &nbsp;Behållare** för att skapa blob-lagring.

    ![Skapa blob-lagringsbehållare för indata](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Ange ett namn för`azuresamldemoblob` behållaren (i exemplet) och kontrollera att **åtkomsttypen** är inställd på **Blob**. Klicka på **OK** när du är klar.

    ![ange information om blob-behållare](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. I **behållarebladet** väljer du den nya behållaren, som öppnar bladet för den behållaren.

7. Klicka på **Överför**.

    ![Knappen "Ladda upp" för en behållare](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. Ladda upp filen **sampleinput.csv** som du hämtade tidigare i bladet Ladda upp **blob.** För **Blob-typ**väljer du **Blockera blob** och ställer in blockstorleken på 4 MB, vilket är tillräckligt för den här självstudien.

9. Klicka på **knappen Ladda upp** längst ned på bladet.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Lägga till sentimentanalysmodellen från Cortana Intelligence Gallery

Nu när exempeldata finns i en blob kan du aktivera sentimentanalysmodellen i Cortana Intelligence Gallery.

1. Gå till [modellsidan för prediktiv sentimentanalys](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) i Cortana Intelligence Gallery.  

2. Klicka på **Öppna i Studio**.  
   
   ![Stream Analytics Machine Learning, öppna Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Logga in för att gå till arbetsytan. Välj en plats.

4. Klicka på **Kör** längst ned på sidan. Processen körs, vilket tar ungefär en minut.

   ![köra experiment i Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. När processen har körts väljer du **Distribuera webbtjänst** längst ned på sidan.

   ![distribuera experiment i Machine Learning Studio som en webbtjänst](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Om du vill verifiera att sentimentanalysmodellen är klar att användas klickar du på knappen **Testa.** Ge textinmatning som "Jag älskar Microsoft". 

   ![testexperiment i Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Om testet fungerar visas ett resultat som liknar följande exempel:

   ![testresultat i Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Klicka på excel **2010 eller tidigare arbetsbokslänken** i kolumnen **Appar** om du vill hämta en Excel-arbetsbok. Arbetsboken innehåller API-nyckeln och webbadressen som du behöver senare för att konfigurera Stream Analytics-jobbet.

    ![Stream Analytics Maskininlärning, snabb blick](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Skapa ett Stream Analytics-jobb som använder machine learning-modellen

Du kan nu skapa ett Stream Analytics-jobb som läser exempeltweeterna från CSV-filen i blob-lagring. 

### <a name="create-the-job"></a>Skapa jobbet

1. Gå till [Azure-portalen](https://portal.azure.com).  

2. Klicka på Skapa ett > **resurs-Internet för saker** > **Stream Analytics-jobb**. **Create a resource** 

3. Namnge jobbet `azure-sa-ml-demo`, ange en prenumeration, ange en befintlig resursgrupp eller skapa en ny och välj plats för projektet.

   ![ange inställningar för nytt Stream Analytics-jobb](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>Konfigurera jobbindata
Jobbet hämtar indata från CSV-filen som du laddade upp tidigare till blob-lagring.

1. När jobbet har skapats klickar du på alternativet **Indata** under **Jobbtopologi** i jobbbladet.    

2. Klicka på **Lägg till indatablobblagring** >**i** bladet **Ingångar**

3. Fyll i **bladet Blob Storage** med följande värden:

   
   |Field  |Värde  |
   |---------|---------|
   |**Indataalias** | Använd namnet `datainput` och välj **Välj blob-lagring från din prenumeration**       |
   |**Lagringskonto**  |  Välj det lagringskonto som du skapade tidigare.  |
   |**Container**  | Markera behållaren som`azuresamldemoblob`du skapade tidigare ( )        |
   |**Händelseserialiseringsformat**  |  Välj **CSV**       |

   ![Inställningar för nya Stream Analytics-jobbindata](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Klicka på **Spara**.

### <a name="configure-the-job-output"></a>Konfigurera jobbutdata
Jobbet skickar resultat till samma blob-lagring där det får indata. 

1. Klicka på alternativet **Utdata** under **Jobbtopologi** i jobbbladet.  

2. Klicka på **Lägg till** >**Blob-lagring**i bladet `datamloutput` **Utdata** och lägg sedan till en utdata med aliaset . 

3. Fyll i **bladet Blob Storage** med följande värden:

   |Field  |Värde  |
   |---------|---------|
   |**Utdataalias** | Använd namnet `datamloutput` och välj **Välj blob-lagring från din prenumeration**       |
   |**Lagringskonto**  |  Välj det lagringskonto som du skapade tidigare.  |
   |**Container**  | Markera behållaren som`azuresamldemoblob`du skapade tidigare ( )        |
   |**Händelseserialiseringsformat**  |  Välj **CSV**       |

   ![Inställningar för nya Stream Analytics-jobbutdata](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Klicka på **Spara**.   


### <a name="add-the-machine-learning-function"></a>Lägg till funktionen Maskininlärning 
Tidigare har du publicerat en Machine Learning-modell till en webbtjänst. I det här fallet när streamanalys-jobbet körs, skickas varje exempeltweet från indata till webbtjänsten för sentimentanalys. Machine Learning-webbtjänsten returnerar`positive` `neutral`ett `negative`sentiment ( , eller ) och en sannolikhet för att tweeten är positiv. 

I det här avsnittet i självstudien definierar du en funktion i jobbet Strömanalys. Funktionen kan anropas för att skicka en tweet till webbtjänsten och få svaret tillbaka. 

1. Kontrollera att du har webbtjänstens URL och API-nyckel som du hämtade tidigare i Excel-arbetsboken.

2. Navigera till jobbbladet > **funktioner** > **+ Lägg till** > **AzureML**

3. Fyll i **funktionsbladet Azure Machine Learning** med följande värden:

   |Field  |Värde  |
   |---------|---------|
   | **Funktionsalias** | Använd namnet `sentiment` och välj **Ange funktionsinställningar för Azure Machine Learning manuellt,** vilket ger dig möjlighet att ange URL och nyckel.      |
   | **URL**| Klistra in webbadressen till webbtjänsten.|
   |**Nyckel** | Klistra in API-nyckeln. |
  
   ![Inställningar för att lägga till machine learning-funktion i Stream Analytics-jobbet](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Klicka på **Spara**.

### <a name="create-a-query-to-transform-the-data"></a>Skapa en fråga för att omvandla data

Stream Analytics använder en deklarativ, SQL-baserad fråga för att undersöka indata och bearbeta den. I det här avsnittet skapar du en fråga som läser varje tweet från indata och sedan anropar machine learning-funktionen för att utföra sentimentanalys. Frågan skickar sedan resultatet till utdata som du definierade (blob storage).

1. Återgå till jobböversiktsbladet.

2.  Klicka på rutan **Fråga** under **Jobbtopologi.**

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

    Frågan anropar den funktion som`sentiment`du skapade tidigare ( ) för att utföra sentimentanalys på varje tweet i indata. 

4. Spara frågan genom att klicka på **Spara**.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Starta Stream Analytics-jobbet och kontrollera utdata

Nu kan du starta Stream Analytics-jobbet.

### <a name="start-the-job"></a>Starta jobbet
1. Återgå till jobböversiktsbladet.

2. Klicka på **Start** högst upp på bladet.

3. I **Start-jobbet**väljer du **Anpassad**och väljer sedan en dag före när du laddade upp CSV-filen till blob-lagring. När du är klar klickar du på **Start**.  


### <a name="check-the-output"></a>Kontrollera utdata
1. Låt jobbet köras i några minuter tills aktiviteten visas i rutan **Övervakning.** 

2. Om du har ett verktyg som du normalt använder för att undersöka `azuresamldemoblob` innehållet i blob-lagring använder du det verktyget för att undersöka behållaren. Du kan också göra följande i Azure-portalen:

    1. Hitta `samldemo` lagringskontot i portalen och leta `azuresamldemoblob` upp behållaren i kontot. Du ser två filer i behållaren: filen som innehåller exempeltweets och en CSV-fil som genereras av Stream Analytics-jobbet.
    2. Högerklicka på den genererade filen och välj sedan **Hämta**. 

   ![Hämta CSV-jobbutdata från Blob-lagring](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Öppna den genererade CSV-filen. Du ser något i stil med följande exempel:  
   
   ![Stream Analytics Maskininlärning, CSV-vy](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Visa mått
Du kan också visa funktionsrelaterade mätvärden för Azure Machine Learning. Följande funktionsrelaterade mått visas i rutan **Övervakning** i jobbbladet:

* **Funktionsbegäranden** anger antalet begäranden som skickas till en machine learning-webbtjänst.  
* **Funktionshändelser** anger antalet händelser i begäran. Som standard innehåller varje begäran till en machine learning-webbtjänst upp till 1 000 händelser.  


## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Integrera REST API och maskininlärning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)




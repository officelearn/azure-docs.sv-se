---
title: "Strömma data från Stream Analytics i Data Lake Store | Microsoft Docs"
description: "Använd Azure Stream Analytics att sända data till Azure Data Lake Store"
services: data-lake-store,stream-analytics
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/30/2018
ms.author: nitinme
ms.openlocfilehash: 8ced5aff33ed23aee3f3399d876c1ed62d2b5707
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics"></a>Strömma data från Azure Storage Blob till Data Lake Store med Azure Stream Analytics
I den här artikeln får du lära dig hur du använder Azure Data Lake Store som utdata för ett Azure Stream Analytics-jobb. Den här artikeln visar ett enkelt scenario som läser data från ett Azure Storage blob (indata) och skriver data till Data Lake Store (utdata).

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Storage-konto**. Du använder en blob-behållare från detta konto för att mata in data för ett Stream Analytics-jobb. Den här självstudiekursen förutsätter att du har ett lagringskonto som kallas **storageforasa** och en behållare i kontot kallas **storageforasacontainer**. När du har skapat behållaren måste du ladda upp en exempeldatafil till den. 
  
* **Azure Data Lake Store-konto**. Följ instruktionerna i [Kom igång med Azure Data Lake Store med hjälp av Azure Portal](data-lake-store-get-started-portal.md). Antar vi att du har ett Data Lake Store-konto som kallas **asadatalakestore**. 

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb
Börja med att skapa ett Stream Analytics-jobb som innehåller ingen källa och ett mål för utdata. I den här självstudien källan är en Azure blob-behållaren och målet är Data Lake Store.

1. Logga in på [Azure Portal](https://portal.azure.com).

2. I den vänstra rutan klickar du på **Stream Analytics-jobb**, och klicka sedan på **Lägg till**.

    ![Skapa ett Stream Analytics-jobbet](./media/data-lake-store-stream-analytics/create.job.png "skapa ett Stream Analytics-jobb")

    > [!NOTE]
    > Kontrollera att du skapar jobbet i samma region som lagringskontot eller du kan innebära ytterligare kostnader för att flytta data mellan regioner.
    >

## <a name="create-a-blob-input-for-the-job"></a>Skapa en Blob-inmatning för jobbet

1. Öppna den för Stream Analytics-jobbet från den vänstra rutan klickar du på den **indata** fliken och klicka sedan på **Lägg till**.

    ![Lägga till indata till jobbet](./media/data-lake-store-stream-analytics/create.input.1.png "lägga till indata till dina jobb")

2. På den **nya indata** bladet, ange följande värden.

    ![Lägga till indata till jobbet](./media/data-lake-store-stream-analytics/create.input.2.png "lägga till indata till dina jobb")

    * För **indata alias**, ange ett unikt namn för det jobb som indata.
    * För **typ av datakälla**väljer **dataströmmen**.
    * För **källa**väljer **Blob storage**.
    * För **prenumeration**väljer **använda blob storage från aktuell prenumeration**.
    * För **lagringskonto**, Välj lagringskonto som du har skapat som en del av förutsättningarna. 
    * För **behållare**, markera den behållare som du skapade i det valda lagringskontot.
    * För **händelse serialiseringsformat**väljer **CSV**.
    * För **avgränsare**väljer **fliken**.
    * För **kodning**väljer **UTF-8**.

    Klicka på **Skapa**. Portalen nu lägger till indata och testar anslutningen till den.


## <a name="create-a-data-lake-store-output-for-the-job"></a>Skapa ett Data Lake Store-utdata för jobbet

1. Öppna sidan för Stream Analytics-jobb, klicka på den **utdata** fliken och klicka sedan på **Lägg till**.

    ![Lägga till utdata till jobbet](./media/data-lake-store-stream-analytics/create.output.1.png "lägga till utdata till dina jobb")

2. På den **nya utdata** bladet, ange följande värden.

    ![Lägga till utdata till jobbet](./media/data-lake-store-stream-analytics/create.output.2.png "lägga till utdata till dina jobb")

    * För **kolumnalias**, ange ett unikt namn utdata för jobbet. Detta är ett eget namn som används i frågor för att dirigera utdata till denna Data Lake Store.
    * För **Sink**väljer **Datasjölager**.
    * Du uppmanas att godkänna åtkomst till Data Lake Store-konto. Klicka på **auktorisera**.

3. På den **nya utdata** bladet fortsätta att tillhandahålla följande värden.

    ![Lägga till utdata till jobbet](./media/data-lake-store-stream-analytics/create.output.3.png "lägga till utdata till dina jobb")

    * För **kontonamn**, Välj Data Lake Store-konto som du redan skapat där du vill att jobbet utdata skickas till.
    * För **prefix sökvägar**, ange en filsökväg som används för att skriva filer inom det angivna Data Lake Store-kontot.
    * För **datumformat**, om du har använt en datumtoken i sökvägen prefix, kan du välja datumformat där filerna ordnas.
    * För **tidsformat**, om du har använt en tid token i prefix-sökvägen, ange tidsformat där filerna ordnas.
    * För **händelse serialiseringsformat**väljer **CSV**.
    * För **avgränsare**väljer **fliken**.
    * För **kodning**väljer **UTF-8**.
    
    Klicka på **Skapa**. Portalen nu lägger till utdata och testar anslutningen till den.
    
## <a name="run-the-stream-analytics-job"></a>Kör Stream Analytics-jobbet

1. Om du vill köra ett Stream Analytics-jobb, måste du köra en fråga från den **frågan** fliken. För den här självstudiekursen, du kan köra exempelfråga genom att ersätta platshållarna med jobbet indata och utdata alias, som visas i skärmdumpen nedan.

    ![Kör frågan](./media/data-lake-store-stream-analytics/run.query.png "kör frågan")

2. Klicka på **spara** högst upp på skärmen och sedan från den **översikt** klickar du på **starta**. Dialogrutan Välj **anpassad tid**, och sedan ange aktuellt datum och tid.

    ![Ange jobbtiden för](./media/data-lake-store-stream-analytics/run.query.2.png "ange jobbtiden för")

    Klicka på **starta** att starta jobbet. Det kan ta ett par minuter att starta jobbet.

3. Kopiera en exempeldatafil till blob-behållaren för att starta jobbet för att hämta data från blob. Du kan få en exempeldatafil från den [Azure Data Lake Git-lagringsplatsen](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Den här självstudiekursen kommer vi kopiera filen **vehicle1_09142014.csv**. Du kan använda olika klienter som [Azure Lagringsutforskaren](http://storageexplorer.com/), för att överföra data till en blob-behållare.

4. Från den **översikt** fliken, under **övervakning**, se hur data bearbetades.

    ![Övervakningsjobb](./media/data-lake-store-stream-analytics/run.query.3.png "Övervakningsjobb")

5. Slutligen kan du kontrollera att utdata för jobbet är tillgänglig i Data Lake Store-konto. 

    ![Kontrollera utdata](./media/data-lake-store-stream-analytics/run.query.4.png "Kontrollera utdata")

    I fönstret Data Explorer utgående meddelande som utdata skrivs till en sökväg som anges i Data Lake Store inställningar (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Se också
* [Skapa ett HDInsight-kluster om du vill använda Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

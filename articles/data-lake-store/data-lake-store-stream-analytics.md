---
title: Stream data från Stream Analytics i Azure Data Lake Storage Gen1 | Microsoft Docs
description: Använd Azure Stream Analytics för att strömdata till Azure Data Lake Storage Gen1
services: data-lake-store,stream-analytics
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: d3dbacd58b3bda3fbf8ee8ad5f175eccc2cb2a24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60194951"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Stream-data från Azure Storage Blob till Azure Data Lake Storage Gen1 med Azure Stream Analytics
I den här artikeln får du lära dig hur du använder Azure Data Lake Storage Gen1 som utdata för Azure Stream Analytics-jobb. Den här artikeln visar ett enkelt scenario som läser data från en Azure Storage blob (indata) och skriver data till Data Lake Storage Gen1 (utdata).

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Storage-konto**. Du använder en blob-behållare från det här kontot för att mata in data för ett Stream Analytics-jobb. Den här självstudien antar vi att du har ett lagringskonto med namnet **storageforasa** och en behållare i kontot kallas **storageforasacontainer**. När du har skapat behållaren måste du ladda upp en exempeldatafil till den. 
  
* **Ett konto för Data Lake Storage Gen1**. Följ anvisningarna på [Kom igång med Azure Data Lake Storage Gen1 med hjälp av Azure-portalen](data-lake-store-get-started-portal.md). Vi antar att du har ett Data Lake Storage Gen1 konto med namnet **myadlsg1**. 

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb
Börja med att skapa ett Stream Analytics-jobb som innehåller en indatakälla och ett mål för utdata. Den här självstudien källan är en Azure blob-behållare och målet är Data Lake Storage Gen1.

1. Logga in på [Azure Portal](https://portal.azure.com).

2. I den vänstra rutan klickar du på **Stream Analytics-jobb**, och klicka sedan på **Lägg till**.

    ![Skapa ett Stream Analytics-jobb](./media/data-lake-store-stream-analytics/create.job.png "skapa ett Stream Analytics-jobb")

    > [!NOTE]
    > Kontrollera att du skapar jobbet i samma region som lagringskontot eller tillkommer ytterligare kostnader för att flytta data mellan regioner.
    >

## <a name="create-a-blob-input-for-the-job"></a>Skapa en Blob som indata för jobbet

1. Öppna den för Stream Analytics-jobbet i den vänstra rutan klickar du på den **indata** fliken och klicka sedan på **Lägg till**.

    ![Lägg till indata för jobbet](./media/data-lake-store-stream-analytics/create.input.1.png "Lägg till indata för jobbet")

2. På den **nya indata** bladet anger du följande värden.

    ![Lägg till indata för jobbet](./media/data-lake-store-stream-analytics/create.input.2.png "Lägg till indata för jobbet")

   * För **indata alias**, ange ett unikt namn för jobbet som indata.
   * För **källtyp**väljer **dataströmmen**.
   * För **källa**väljer **Blob-lagring**.
   * För **prenumeration**väljer **använda blob storage från aktuell prenumeration**.
   * För **lagringskonto**, Välj det lagringskonto som du har skapat som en del av förutsättningarna. 
   * För **behållare**, markera den behållare som du skapade i det valda lagringskontot.
   * För **händelseserialiseringsformat**väljer **CSV**.
   * För **avgränsare**väljer **fliken**.
   * För **Encoding**väljer **UTF-8**.

     Klicka på **Skapa**. Portalen nu lägger till indata och testar anslutningen till den.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Skapa ett Data Lake Storage Gen1 utdata för jobbet

1. Öppna den för Stream Analytics-jobbet klickar du på den **utdata** fliken **Lägg till**, och välj **Data Lake Storage Gen1**.

    ![Lägg till utdata för jobbet](./media/data-lake-store-stream-analytics/create.output.1.png "Lägg till utdata för jobbet")

2. På den **nya utdata** bladet anger du följande värden.

    ![Lägg till utdata för jobbet](./media/data-lake-store-stream-analytics/create.output.2.png "Lägg till utdata för jobbet")

    * För **utdataaliaset**, ange ett unikt namn för utdata för jobbet. Det här är ett eget namn som används i frågor för att dirigera utdata till det här Data Lake Storage Gen1-kontot.
    * Du uppmanas att godkänna åtkomst till Data Lake Storage Gen1-konto. Klicka på **auktorisera**.

3. På den **nya utdata** bladet fortsätta att tillhandahålla följande värden.

    ![Lägg till utdata för jobbet](./media/data-lake-store-stream-analytics/create.output.3.png "Lägg till utdata för jobbet")

   * För **kontonamn**, väljer du det Data Lake Storage Gen1-konto som du redan skapat där du vill att jobbet utdata skickas till.
   * För **prefixmönster för sögväg**, ange en filsökväg som används för att skriva dina filer inom det angivna Gen1 för Data Lake Storage-kontot.
   * För **datumformat**, om du använde en datumtoken i prefixsökvägen kan du välja datumformat där filerna ordnas.
   * För **tidsformat**, om du använde en time-token i prefixsökvägen, ange tidsformatet där filerna ordnas.
   * För **händelseserialiseringsformat**väljer **CSV**.
   * För **avgränsare**väljer **fliken**.
   * För **Encoding**väljer **UTF-8**.
    
     Klicka på **Skapa**. Portalen nu lägger till utdata och testar anslutningen till den.
    
## <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

1. Om du vill köra ett Stream Analytics-jobb, måste du köra en fråga från den **fråga** fliken. För den här självstudien får du kan köra exemplet frågan genom att ersätta platshållarna med de jobb som indata och utdata alias, som visas i skärmdumpen nedan.

    ![Kör fråga](./media/data-lake-store-stream-analytics/run.query.png "Kör fråga")

2. Klicka på **spara** högst upp på skärmen och klicka sedan på **översikt** klickar du på **starta**. Dialogrutan Välj **anpassad tid**, och sedan ange aktuellt datum och tid.

    ![Ange jobbtid](./media/data-lake-store-stream-analytics/run.query.2.png "ange jobbtid")

    Klicka på **starta** att starta jobbet. Det kan ta upp till ett par minuter att starta jobbet.

3. Kopiera en exempeldatafil till blob-behållaren för att utlösa jobbet så att den hämtar data från blob. Du kan få en exempeldatafil från den [Azure Data Lake Git-lagringsplats](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Den här självstudien ska vi kopiera filen **vehicle1_09142014.csv**. Du kan använda olika klienter, till exempel [Azure Storage Explorer](https://storageexplorer.com/), för att ladda upp data till en blob-behållare.

4. Från den **översikt** fliken, under **övervakning**, se hur data bearbetades.

    ![Övervakningsjobb](./media/data-lake-store-stream-analytics/run.query.3.png "Övervakningsjobb")

5. Slutligen kan kontrollera du att utdata för jobbet är tillgänglig i Data Lake Storage Gen1-konto. 

    ![Verifiera utdata](./media/data-lake-store-stream-analytics/run.query.4.png "verifiera utdata")

    I Data Explorer-fönstret Lägg märke till att utdata skrivs till en sökväg som anges i Data Lake Storage Gen1 utdatainställningar (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Se också
* [Skapa ett HDInsight-kluster om du vill använda Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

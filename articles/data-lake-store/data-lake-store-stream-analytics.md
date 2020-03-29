---
title: Strömma data från Stream Analytics till Azure Data Lake Storage Gen1 | Microsoft-dokument
description: Använda Azure Stream Analytics för att strömma data till Azure Data Lake Storage Gen1
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60194951"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Strömma data från Azure Storage Blob till Azure Data Lake Storage Gen1 med Azure Stream Analytics
I den här artikeln får du lära dig hur du använder Azure Data Lake Storage Gen1 som en utdata för ett Azure Stream Analytics-jobb. Den här artikeln visar ett enkelt scenario som läser data från en Azure Storage-blob (indata) och skriver data till Data Lake Storage Gen1 (utdata).

## <a name="prerequisites"></a>Krav
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Storage-konto**. Du kommer att använda en blob-behållare från det här kontot för att mata in data för ett Stream Analytics-jobb. För den här självstudien antar du att du har ett lagringskonto som heter **storageforasa** och en behållare i kontot som kallas **storageforasacontainer**. När du har skapat behållaren överför du en exempeldatafil till den. 
  
* **Ett Data Lake Storage Gen1-konto**. Följ instruktionerna på [Kom igång med Azure Data Lake Storage Gen1 med Hjälp av Azure Portal](data-lake-store-get-started-portal.md). Låt oss anta att du har ett Data Lake Storage Gen1-konto som heter **myadlsg1**. 

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb
Du börjar med att skapa ett Stream Analytics-jobb som innehåller en indatakälla och ett utdatamål. För den här självstudien är källan en Azure blob-behållare och målet är Data Lake Storage Gen1.

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Klicka på Stream **Analytics-jobb**i den vänstra rutan och klicka sedan på **Lägg till**.

    ![Skapa ett Stream Analytics-jobb](./media/data-lake-store-stream-analytics/create.job.png "Skapa ett Stream Analytics-jobb")

    > [!NOTE]
    > Se till att du skapar jobb i samma region som lagringskontot eller så ådrar du dig extra kostnader för att flytta data mellan regioner.
    >

## <a name="create-a-blob-input-for-the-job"></a>Skapa en Blob-inmatning för jobbet

1. Öppna sidan för Stream Analytics-jobbet, från den vänstra rutan klicka på fliken **Indata** och klicka sedan på **Lägg till**.

    ![Lägga till en inmatning till jobbet](./media/data-lake-store-stream-analytics/create.input.1.png "Lägga till en inmatning till jobbet")

2. Ange följande värden på det **nya ingångsbladet.**

    ![Lägga till en inmatning till jobbet](./media/data-lake-store-stream-analytics/create.input.2.png "Lägga till en inmatning till jobbet")

   * För **Indataalias**anger du ett unikt namn för jobbindata.
   * För **källtyp**väljer du **Dataström**.
   * För **Källa**väljer du **Blob-lagring**.
   * För **Prenumeration**väljer du **Använd blob-lagring från aktuell prenumeration**.
   * För **Lagringskonto**väljer du det lagringskonto som du skapade som en del av förutsättningarna. 
   * För **Behållare**väljer du den behållare som du skapade i det valda lagringskontot.
   * Välj **CSV**för **serielliseringsformat för händelseserieformat**.
   * För **Avgränsare**väljer du **fliken**.
   * För **kodning**väljer du **UTF-8**.

     Klicka på **Skapa**. Portalen lägger nu till indata och testar anslutningen till den.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Skapa en Data Lake Storage Gen1-utdata för jobbet

1. Öppna sidan för Stream Analytics-jobbet, klicka på fliken **Utdata,** klicka på **Lägg till**och välj **Betan för datasjölagring1**.

    ![Lägga till en utdata till jobbet](./media/data-lake-store-stream-analytics/create.output.1.png "Lägga till en utdata till jobbet")

2. Ange följande värden på det **nya utmatningsbladet.**

    ![Lägga till en utdata till jobbet](./media/data-lake-store-stream-analytics/create.output.2.png "Lägga till en utdata till jobbet")

    * För **Utdataalias**anger du ett unikt namn för projektutdata. Detta är ett eget namn som används i frågor för att dirigera frågeutdata till det här Data Lake Storage Gen1-kontot.
    * Du uppmanas att auktorisera åtkomst till Data Lake Storage Gen1-kontot. Klicka på **Auktorisera**.

3. Fortsätt att ange följande värden på det **nya utmatningsbladet.**

    ![Lägga till en utdata till jobbet](./media/data-lake-store-stream-analytics/create.output.3.png "Lägga till en utdata till jobbet")

   * För **kontonamn**väljer du det DataSjölagringsgenm1-konto som du redan har skapat där du vill att projektutdata ska skickas till.
   * För **path prefix-mönster**anger du en filsökväg som används för att skriva dina filer i det angivna Data Lake Storage Gen1-kontot.
   * För **Datumformat**kan du välja det datumformat som filerna är ordnade i om du har använt en datumtoken i prefixsökvägen.
   * Om du använder en tidstoken i prefixsökvägen för **tidsformat**anger du det tidsformat som filerna är ordnade i.
   * Välj **CSV**för **serielliseringsformat för händelseserieformat**.
   * För **Avgränsare**väljer du **fliken**.
   * För **kodning**väljer du **UTF-8**.
    
     Klicka på **Skapa**. Portalen lägger nu till utdata och testar anslutningen till den.
    
## <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

1. Om du vill köra ett Stream Analytics-jobb måste du köra en fråga från fliken **Fråga.** För den här självstudien kan du köra exempelfrågan genom att ersätta platshållarna med jobbinmatnings- och utdataalias, som visas i skärmfånget nedan.

    ![Kör fråga](./media/data-lake-store-stream-analytics/run.query.png "Kör frågan")

2. Klicka på **Spara** högst upp på skärmen och klicka sedan på **Start**på fliken **Översikt** . Välj **Anpassad tid**i dialogrutan och ange sedan aktuellt datum och aktuell tid.

    ![Ange jobbtid](./media/data-lake-store-stream-analytics/run.query.2.png "Ange jobbtid")

    Klicka på **Start** för att starta jobbet. Det kan ta upp till ett par minuter att starta jobbet.

3. Om du vill utlösa jobbet för att plocka data från blobben kopierar du en exempeldatafil till blob-behållaren. Du kan hämta en exempeldatafil från [Azure Data Lake Git Repository](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). För den här guiden, låt oss kopiera filen **vehicle1_09142014.csv**. Du kan använda olika klienter, till exempel [Azure Storage Explorer,](https://storageexplorer.com/)för att överföra data till en blob-behållare.

4. Se hur data har bearbetats under **Övervakning**på fliken **Översikt.**

    ![Övervaka jobb](./media/data-lake-store-stream-analytics/run.query.3.png "Övervaka jobb")

5. Slutligen kan du kontrollera att jobbutdata är tillgängliga i datasjölagringgenm1-kontot. 

    ![Verifiera utdata](./media/data-lake-store-stream-analytics/run.query.4.png "Verifiera utdata")

    I fönstret Data Explorer lägger du märke till att utdata skrivs till en`streamanalytics/job/output/{date}/{time}`mappsökväg enligt vad som anges i utdatainställningarna för Lagringsgenm1 för Datasjö ( ).  

## <a name="see-also"></a>Se även
* [Skapa ett HDInsight-kluster för att använda Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

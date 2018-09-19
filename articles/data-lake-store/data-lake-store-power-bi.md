---
title: Analysera data i Azure Data Lake Storage Gen1 med hjälp av Power BI | Microsoft Docs
description: Använd Power BI för att analysera data som lagras i Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57d19d27-e135-49d9-a7ea-46c48ef4e3bd
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 5db9d18a31af4d6b407fcd9172ac80fc6f93f085
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46297187"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Analysera data i Azure Data Lake Storage Gen1 med hjälp av Power BI
I den här artikeln får du lära dig hur du använder Power BI Desktop att analysera och visualisera data som lagras i Azure Data Lake Storage Gen1.

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett konto för Data Lake Storage Gen1**. Följ anvisningarna på [Kom igång med Azure Data Lake Storage Gen1 med Azure portal](data-lake-store-get-started-portal.md). Den här artikeln förutsätter att du redan har skapat ett konto med Data Lake Storage Gen1, namnet **myadlsg1**, och överföra en exempeldatafil (**Drivers.txt**) till den. Den här exempelfilen är tillgänglig för nedladdning från [Azure Data Lake Git-lagringsplats](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Du kan ladda ned det från [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Skapa en rapport i Power BI Desktop
1. Starta Power BI Desktop på din dator.
2. Från den **Start** menyfliksområdet, klickar du på **hämta Data**, och klicka sedan på mer. I den **hämta Data** dialogrutan klickar du på **Azure**, klickar du på **Azure Data Lake Store**, och klicka sedan på **Connect**.
   
    ![Ansluta till Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account.png "ansluta till Data Lake Storage Gen1")
3. Om du ser en dialogruta om connector i en utvecklingsfas kan du välja om du vill fortsätta.
4. I den **Azure Data Lake Store** dialogrutan Ange URL: en till ditt Data Lake Storage Gen1-konto och klicka sedan på **OK**.
   
    ![URL: en för Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL: en för Data Lake Storage Gen1")
5. I dialogrutan nästa klickar du på **logga in** att logga in på Data Lake Storage Gen1-kontot. Du omdirigeras till din organisations inloggningssida. Följ anvisningarna för att logga in på kontot.
   
    ![Logga in på Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "logga in på Data Lake Storage Gen1")
6. När du har loggat in klickar du på **Connect**.
   
    ![Ansluta till Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "ansluta till Data Lake Storage Gen1")
7. Nästa dialogruta visar den fil som du överfört till ditt Data Lake Storage Gen1-konto. Kontrollera informationen och klicka sedan på **belastningen**.
   
    ![Läsa in data från Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "läsa in data från Data Lake Storage Gen1")
8. När data har lästs in på Power BI, ser du följande fält i den **fält** fliken.
   
    ![Importerade fält](./media/data-lake-store-power-bi/imported-fields.png "importeras fält")
   
    Om du vill visualisera och analysera data och föredrar vi emellertid att data kan tillgängliga per följande fält
   
    ![Önskade fält](./media/data-lake-store-power-bi/desired-fields.png "önskade fält")
   
    I nästa steg ska kan vi uppdatera frågan om du vill konvertera den importerade informationen i det önskade formatet.
9. Från den **Start** menyfliksområdet, klickar du på **redigera frågor**.
   
    ![Redigera frågor](./media/data-lake-store-power-bi/edit-queries.png "redigera frågor")
10. I frågeredigeraren under den **innehåll** kolumnen, klickar du på **binära**.
    
    ![Redigera frågor](./media/data-lake-store-power-bi/convert-query1.png "redigera frågor")
11. Du ser en filikon som representerar den **Drivers.txt** filen du laddade upp. Högerklicka på filen och klicka på **CSV**.    
    
    ![Redigera frågor](./media/data-lake-store-power-bi/convert-query2.png "redigera frågor")
12. Du bör se utdata som visas nedan. Dina data är nu tillgänglig i ett format som du kan använda för att skapa visualiseringar.
    
    ![Redigera frågor](./media/data-lake-store-power-bi/convert-query3.png "redigera frågor")
13. Från den **Start** menyfliksområdet, klickar du på **Stäng och tillämpa**, och klicka sedan på **Stäng och tillämpa**.
    
    ![Redigera frågor](./media/data-lake-store-power-bi/load-edited-query.png "redigera frågor")
14. När frågan har uppdaterats kan den **fält** fliken visas de nya fält som är tillgängliga för visualisering.
    
    ![Uppdatera fält](./media/data-lake-store-power-bi/updated-query-fields.png "uppdatera fält")
15. Låt oss skapa ett cirkeldiagram för att representera drivrutinerna i varje stad för ett visst land. Om du vill göra det, gör du följande val.
    
    1. Klicka på symbolen för ett cirkeldiagram från fliken visualiseringar.
       
        ![Skapa cirkeldiagram](./media/data-lake-store-power-bi/create-pie-chart.png "skapa cirkeldiagram")
    2. De kolumner som vi ska använda är **kolumn 4** (namn på ort) och **kolumnen 7** (namnet på landet). Dra dessa kolumner från **fält** fliken **visualiseringar** fliken enligt nedan.
       
        ![Skapa visualiseringar](./media/data-lake-store-power-bi/create-visualizations.png "skapa visualiseringar")
    3. Cirkeldiagrammet bör nu se ut ungefär som den som visas nedan.
       
        ![Cirkeldiagram](./media/data-lake-store-power-bi/pie-chart.png "skapa visualiseringar")
16. Genom att välja ett visst land på sidnivå, kan du nu se antalet drivrutiner i varje stad för det valda landet. Under exempelvis den **visualiseringar** fliken, under **på sidnivå**väljer **Brasilien**.
    
    ![Välj ett land](./media/data-lake-store-power-bi/select-country.png "Välj ett land")
17. Cirkeldiagrammet uppdateras automatiskt för att visa drivrutinerna i Brasilien städerna.
    
    ![Drivrutiner i ett land](./media/data-lake-store-power-bi/driver-per-country.png "drivrutiner per land")
18. Från den **filen** -menyn klickar du på **spara** att spara visualiseringen som en Power BI Desktop-fil.

## <a name="publish-report-to-power-bi-service"></a>Publicera rapporten till Power BI-tjänsten
När du har skapat visualiseringar i Power BI Desktop kan dela du den med andra genom att publicera den till Power BI-tjänsten. Anvisningar för hur du gör finns i [publicera från Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Se också
* [Analysera data i Data Lake Storage Gen1 med Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)


---
title: Analysera data i Data Lake Store med hjälp av Power BI | Microsoft Docs
description: Använd Power BI för att analysera data som lagras i Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57d19d27-e135-49d9-a7ea-46c48ef4e3bd
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 08660bd1c434c9404fa53a33be209b7a3c4d9372
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="analyze-data-in-data-lake-store-by-using-power-bi"></a>Analysera data i Data Lake Store med hjälp av Power BI
I den här artikeln får du lära dig hur du använder Power BI Desktop att analysera och visualisera data som lagras i Azure Data Lake Store.

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Data Lake Store-konto**. Följ instruktionerna i [Kom igång med Azure Data Lake Store med hjälp av Azure Portal](data-lake-store-get-started-portal.md). Den här artikeln förutsätter att du redan har skapat ett Data Lake Store-konto, som kallas **mybidatalakestore**, och överföra en exempeldatafil (**Drivers.txt**) till den. Den här exempelfilen är tillgängliga för nedladdning från [Azure Data Lake Git-lagringsplatsen](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Du kan ladda ned det från [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Skapa en rapport i Power BI Desktop
1. Starta Power BI Desktop på datorn.
2. Från den **Start** band klickar du på **hämta Data**, och klicka sedan på mer. I den **hämta Data** dialogrutan klickar du på **Azure**, klickar du på **Azure Data Lake Store**, och klicka sedan på **Anslut**.
   
    ![Anslut till Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account.png "ansluta till Data Lake Store")
3. Om du ser en dialogruta om att den finns i en utvecklingsfasen connector, välja för att fortsätta.
4. I den **Microsoft Azure Data Lake Store** dialogrutan Ange URL till ditt Data Lake Store-konto och klicka sedan på **OK**.
   
    ![URL för Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL för Data Lake Store")
5. Klicka på nästa dialogrutan **inloggning** att logga in på Data Lake Store-konto. Du omdirigeras till inloggningssidan för din organisation. Följ anvisningarna för att logga in på kontot.
   
    ![Logga in på Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "logga in på Data Lake Store")
6. När du har loggat in, klickar du på **Anslut**.
   
    ![Anslut till Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "ansluta till Data Lake Store")
7. Nästa dialogruta visar den fil som du överfört till ditt Data Lake Store-konto. Kontrollera informationen och klicka sedan på **belastningen**.
   
    ![Läs in data från Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "läsa in data från Data Lake Store")
8. När data har lästs in Power BI, visas följande fält i den **fält** fliken.
   
    ![Importera fält](./media/data-lake-store-power-bi/imported-fields.png "importeras fält")
   
    Om du vill visualisera och analysera data, föredra vi dock data ska vara tillgängliga per följande fält
   
    ![Önskade fält](./media/data-lake-store-power-bi/desired-fields.png "önskade fält")
   
    I nästa steg ska vi kommer att uppdatera frågan om du vill konvertera den importerade informationen i det önskade formatet.
9. Från den **Start** band klickar du på **redigera frågor**.
   
    ![Redigera frågor](./media/data-lake-store-power-bi/edit-queries.png "redigera frågor")
10. I frågeredigeraren under den **innehåll** kolumnen, klickar du på **binär**.
    
    ![Redigera frågor](./media/data-lake-store-power-bi/convert-query1.png "redigera frågor")
11. Du ser en filikon som representerar den **Drivers.txt** -fil som du har överfört. Högerklicka på filen och klicka på **CSV**.    
    
    ![Redigera frågor](./media/data-lake-store-power-bi/convert-query2.png "redigera frågor")
12. Du bör se utdata som visas nedan. Dina data finns nu i ett format som du kan använda för att skapa visualiseringar.
    
    ![Redigera frågor](./media/data-lake-store-power-bi/convert-query3.png "redigera frågor")
13. Från den **Start** band klickar du på **stängs och**, och klicka sedan på **stängs och**.
    
    ![Redigera frågor](./media/data-lake-store-power-bi/load-edited-query.png "redigera frågor")
14. När frågan uppdateras den **fält** fliken visas de nya fält som är tillgängliga för visualisering.
    
    ![Uppdatera fält](./media/data-lake-store-power-bi/updated-query-fields.png "uppdatera fält")
15. Låt oss skapa ett cirkeldiagram som representerar drivrutinerna i varje ort för ett visst land. Om du vill göra det, gör du följande val.
    
    1. Klicka på symbolen för ett cirkeldiagram från fliken visualiseringar.
       
        ![Skapa cirkeldiagram](./media/data-lake-store-power-bi/create-pie-chart.png "skapa cirkeldiagram")
    2. De kolumner som vi ska använda är **kolumner 4** (namnet på staden) och **kolumn 7** (namnet på landet). Dra dessa kolumner från **fält** fliken **visualiseringar** fliken enligt nedan.
       
        ![Skapa grafik](./media/data-lake-store-power-bi/create-visualizations.png "skapa grafik")
    3. Cirkeldiagrammet bör nu se ut ungefär som visas nedan.
       
        ![Cirkeldiagram](./media/data-lake-store-power-bi/pie-chart.png "skapa grafik")
16. Du kan nu se antalet drivrutiner i varje ort i det valda landet genom att välja ett visst land från nivån sidfilter. Under den **visualiseringar** fliken, under **sidan nivå filter**väljer **Brasilien**.
    
    ![Välj ett land](./media/data-lake-store-power-bi/select-country.png "Välj land")
17. Cirkeldiagrammet uppdateras automatiskt för att visa drivrutinerna i Brasilien städer.
    
    ![Drivrutiner i ett land](./media/data-lake-store-power-bi/driver-per-country.png "drivrutiner per land")
18. Från den **filen** -menyn klickar du på **spara** spara visualiseringen som en Power BI Desktop-fil.

## <a name="publish-report-to-power-bi-service"></a>Publicera rapporten till Power BI-tjänsten
När du har skapat visualiseringar i Power BI Desktop kan dela du den med andra genom att publicera Power BI-tjänsten. Instruktioner om hur du gör finns [publicera från Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Se också
* [Analysera data i Data Lake Store med hjälp av Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)


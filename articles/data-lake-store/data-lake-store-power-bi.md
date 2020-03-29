---
title: Analysera data i Azure Data Lake Storage Gen1 med Power BI | Microsoft-dokument
description: Använda Power BI för att analysera data som lagras i Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 57d19d27-e135-49d9-a7ea-46c48ef4e3bd
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: d8717b8f365e692b5f27bf8a04d65c5147b8f31b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65603206"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Analysera data i Azure Data Lake Storage Gen1 med hjälp av Power BI
I den här artikeln får du lära dig hur du använder Power BI Desktop för att analysera och visualisera data som lagras i Azure Data Lake Storage Gen1.

## <a name="prerequisites"></a>Krav
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Data Lake Storage Gen1-konto**. Följ instruktionerna på [Kom igång med Azure Data Lake Storage Gen1 med Hjälp av Azure-portalen](data-lake-store-get-started-portal.md). Den här artikeln förutsätter att du redan har skapat ett Data Lake Storage Gen1-konto, kallat **myadlsg1,** och överfört en exempeldatafil **(Drivers.txt)** till den. Den här exempelfilen är tillgänglig för hämtning från [Azure Data Lake Git Repository](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI-skrivbordet**. Du kan hämta den från [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Skapa en rapport i Power BI Desktop
1. Starta Power BI Desktop på datorn.
2. Klicka på Hämta **data**i menyfliksområdet **Start** och klicka sedan på Mer. Klicka på **Azure**i dialogrutan **Hämta data,** klicka på **Azure Data Lake Store**och klicka sedan på **Anslut**.
   
    ![Ansluta till DataSjölagring Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Ansluta till DataSjölagring Gen1")
3. Om du ser en dialogruta om att kopplingen är i en utvecklingsfas väljer du att fortsätta.
4. I dialogrutan **Azure Data Lake Store** anger du URL:en till ditt DataSjölagringsgenm1-konto och klickar sedan på **OK**.
   
    ![URL för DataSjölagring Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL för DataSjölagring Gen1")
5. Klicka på **Logga in i** kontot Data Lake Storage Gen1 i nästa dialogruta. Du omdirigeras till organisationens inloggningssida. Följ anvisningarna för att logga in på kontot.
   
    ![Logga in i Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Logga in i Data Lake Storage Gen1")
6. När du har loggat in klickar du på **Anslut**.
   
    ![Ansluta till DataSjölagring Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Ansluta till DataSjölagring Gen1")
7. I nästa dialogruta visas filen som du har laddat upp till ditt Data Lake Storage Gen1-konto. Kontrollera informationen och klicka sedan på **Läs in**.
   
    ![Läsa in data från Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Läsa in data från Data Lake Storage Gen1")
8. När data har lästs in i Power BI visas följande fält på fliken **Fält.**
   
    ![Importerade fält](./media/data-lake-store-power-bi/imported-fields.png "Importerade fält")
   
    Men för att visualisera och analysera data, föredrar vi att data ska vara tillgängliga enligt följande fält
   
    ![Önskade fält](./media/data-lake-store-power-bi/desired-fields.png "Önskade fält")
   
    I nästa steg uppdaterar vi frågan för att konvertera importerade data i önskat format.
9. Klicka på Redigera **frågor**i menyfliksområdet **Start.**
   
    ![Redigera frågor](./media/data-lake-store-power-bi/edit-queries.png "Redigera frågor")
10. Klicka på **Binär**under kolumnen **Innehåll** i frågeredigeraren.
    
    ![Redigera frågor](./media/data-lake-store-power-bi/convert-query1.png "Redigera frågor")
11. Du kommer att se en filikon som representerar **filen Drivers.txt** som du har laddat upp. Högerklicka på filen och klicka på **CSV**.    
    
    ![Redigera frågor](./media/data-lake-store-power-bi/convert-query2.png "Redigera frågor")
12. Du bör se en utdata som visas nedan. Dina data är nu tillgängliga i ett format som du kan använda för att skapa visualiseringar.
    
    ![Redigera frågor](./media/data-lake-store-power-bi/convert-query3.png "Redigera frågor")
13. Klicka på Stäng **och Använd**i menyfliksområdet **Start** och klicka sedan på **Stäng och Använd**.
    
    ![Redigera frågor](./media/data-lake-store-power-bi/load-edited-query.png "Redigera frågor")
14. När frågan har uppdaterats visar fliken **Fält** de nya fält som är tillgängliga för visualisering.
    
    ![Uppdaterade fält](./media/data-lake-store-power-bi/updated-query-fields.png "Uppdaterade fält")
15. Låt oss skapa ett cirkeldiagram för att representera drivrutinerna i varje stad för ett visst land/en viss region. Gör följande val för att göra detta.
    
    1. Klicka på symbolen för ett cirkeldiagram på fliken Visualiseringar.
       
        ![Skapa cirkeldiagram](./media/data-lake-store-power-bi/create-pie-chart.png "Skapa cirkeldiagram")
    2. De kolumner som vi ska använda är **kolumn 4** (namnet på staden) och **kolumn 7** (namnet på landet/regionen). Dra dessa kolumner från fliken **Fält** till fliken **Visualiseringar** enligt nedan.
       
        ![Skapa visualiseringar](./media/data-lake-store-power-bi/create-visualizations.png "Skapa visualiseringar")
    3. Cirkeldiagrammet ska nu likna det som visas nedan.
       
        ![Cirkeldiagram](./media/data-lake-store-power-bi/pie-chart.png "Skapa visualiseringar")
16. Genom att välja ett visst land/en viss region från sidnivåfiltren kan du nu se antalet drivrutiner i varje stad i det valda landet/regionen. Välj till exempel **Brasilien**under **Filter på sidnivå**under fliken **Visualiseringar.**
    
    ![Välj ett land](./media/data-lake-store-power-bi/select-country.png "Välj ett land eller en region")
17. Cirkeldiagrammet uppdateras automatiskt för att visa drivrutinerna i städerna i Brasilien.
    
    ![Förare i ett land](./media/data-lake-store-power-bi/driver-per-country.png "Förare per land/region")
18. På **Arkiv-menyn** klickar du på **Spara** för att spara visualiseringen som en Power BI-skrivbordsfil.

## <a name="publish-report-to-power-bi-service"></a>Publicera rapport till Power BI-tjänsten
När du har skapat visualiseringarna i Power BI Desktop kan du dela den med andra genom att publicera den i Power BI-tjänsten. Instruktioner om hur du gör det finns i [Publicera från Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Se även
* [Analysera data i Data Lake Storage Gen1 med Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)


---
title: Analysera data i Azure Data Lake Storage Gen2 med Power BI | Microsoft Docs
description: Använd Power BI för att analysera data som lagras i Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: normesta
ms.reviewer: bensack
ms.openlocfilehash: d76ea317271ae0e8eb0d54fcfee5dc005d836fc1
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984948"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen2-by-using-power-bi"></a>Analysera data i Azure Data Lake Storage Gen2 med Power BI

I den här artikeln får du lära dig hur du använder Power BI Desktop för att analysera och visualisera data som lagras i ett lagrings konto med ett hierarkiskt namn område (Azure Data Lake Storage Gen2).

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar de här självstudierna måste du ha:

> [!div class="checklist"]
> * En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Ett lagrings konto som har ett hierarkiskt namn område. Följ [de här](data-lake-storage-quickstart-create-account.md) anvisningarna för att skapa en.
> Den här artikeln förutsätter att du har skapat ett `myadlsg2`konto med namnet.
> * En exempel data fil med `Drivers.txt` namnet som finns i ditt lagrings konto.
> Du kan hämta det här exemplet från [Azure Data Lake git-lagringsplatsen](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)och sedan överföra filen till ditt lagrings konto.
> * **Power BI Desktop**. Du kan ladda ned det från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Skapa en rapport i Power BI Desktop

1. Starta Power BI Desktop på datorn.
2. Klicka på **Hämta data**på fliken **Start** i menyfliksområdet och klicka sedan på **mer**.
3. I dialog rutan **Hämta data** klickar du på **Azure**, klickar på **Azure Data Lake Store Gen2 (beta)** och klickar sedan på **Anslut**.

    ![Sidan hämta data](media/data-lake-storage-use-power-bi/get-data-page.png)

4. I dialog rutan **Azure Data Lake Storage Gen2** kan du ange URL: en för ditt Azure Data Lake Storage Gen2 konto, fil system eller undermappar med hjälp av behållarens slut punkts format. URL: er för data Lake Storage Gen2 har följande `https://<accountname>.dfs.core.windows.net/<filesystemname>/<subfolder>` mönster och klicka sedan på **OK**.

    ![URL](media/data-lake-storage-use-power-bi/adls-url.png)

5. I nästa dialog ruta klickar du på **Logga** in för att logga in på ditt lagrings konto. Du omdirigeras till din organisations inloggnings sida. Följ anvisningarna för att logga in på kontot.

    ![Inloggningssida](media/data-lake-storage-use-power-bi/sign-in.png)

6. När du har loggat in klickar du på **Anslut**.

    ![Inloggad sida](media/data-lake-storage-use-power-bi/signed-in.png)

7. I nästa dialog ruta visas alla filer under den URL som du angav i steg 4 ovan, inklusive den fil som du laddade upp till ditt lagrings konto. Kontrol lera informationen och klicka sedan på **Läs in**.

    ![Fil system](media/data-lake-storage-use-power-bi/file-systems.png)

8. När data har lästs in i Power BI visas följande fält på fliken **fält** .

    ![Fält-fliken](media/data-lake-storage-use-power-bi/fields.png)

    Men för att visualisera och analysera data, föredrar vi att data är tillgängliga enligt följande fält.

    ![Fält](media/data-lake-storage-use-power-bi/preferred-fields.png)

    I nästa steg ska vi uppdatera frågan för att konvertera importerade data i det önskade formatet.

9. På fliken **Start** i menyfliksområdet klickar du på **Redigera frågor**.

    ![Frågor](media/data-lake-storage-use-power-bi/queries.png)

10. I **Frågeredigeraren**, under kolumnen **innehåll** , klickar du på **binär**. Filen identifieras automatiskt som CSV och du bör se utdata som visas nedan. Dina data är nu tillgängliga i ett format som du kan använda för att skapa visualiseringar.

    ![Output](media/data-lake-storage-use-power-bi/binary.png)

11. På fliken **Start** i menyfliksområdet klickar du på **Stäng** och **Verkställ**och sedan på **Stäng** och **Använd**.

    ![Stäng och tillämpa](media/data-lake-storage-use-power-bi/close-apply.png)

12. När frågan har uppdaterats visas de nya fält som är tillgängliga för visualisering på fliken **fält** .

    ![Nya fält](media/data-lake-storage-use-power-bi/new-fields.png)

13. Låt oss skapa ett cirkel diagram som visar driv rutinerna i varje stad för ett specifikt land. Det gör du genom att göra följande val.

    På fliken **visualiseringar** klickar du på symbolen för ett cirkel diagram.

    ![Visualiseringar](media/data-lake-storage-use-power-bi/visualizations.png)

    Kolumnerna som vi ska använda är kolumn 4 (namnet på staden) och kolumn 7 (landets namn). Dra de här kolumnerna från fliken **fält** till fliken **visualiseringar** som visas nedan.

    ![Dra fält](media/data-lake-storage-use-power-bi/visualizations-drag-fields.png)

    Cirkel diagrammet bör nu likna det som visas nedan.

    ![Cirkeldiagram](media/data-lake-storage-use-power-bi/pie-chart.png)

14. Genom att välja ett särskilt land från sid nivå filtren kan du nu se antalet driv rutiner i varje stad i det valda landet. Välj exempelvis **Brasilien**under fliken **visualiseringar** under **sid nivå filter**.

    ![Sid filter](media/data-lake-storage-use-power-bi/page-filters.png)

15. Cirkel diagrammet uppdateras automatiskt så att det visar driv rutinerna i städerna för Brasilien.

    ![Brasilien](media/data-lake-storage-use-power-bi/pie-chart-updated.png)

16. I menyn **Arkiv** klickar du på **Spara** för att spara visualiseringen som en Power BI Desktop-fil.

## <a name="publish-report-to-power-bi-service"></a>Publicera rapporten till Power BI-tjänst

När du har skapat visualiseringarna i Power BI Desktop kan du dela den med andra genom att publicera den på Power BI-tjänst. Instruktioner för hur du gör finns i [publicera från Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

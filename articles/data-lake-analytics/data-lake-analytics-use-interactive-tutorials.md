---
title: "Lär dig Data Lake Analytics och U-SQL med hjälp av interaktiva självstudier i Azure Portal | Microsoft Docs"
description: "Snabbstart för att lära dig Data Lake Analytics och U-SQL. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 31399d47-e937-4c43-ab0a-6aea8875378d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c2a9bd7f78afc77f72236d0204f39798f0388362


---
# <a name="use-azure-data-lake-analytics-interactive-tutorials"></a>Använd interaktiva Azure Data Lake Analytics-självstudier
Azure Portal tillhandahåller interaktiva självstudier för att komma igång med Data Lake Analytics. Den här artikeln visar hur du går igenom självstudierna för att analysera webbplatsloggar.

> [!NOTE]
> Om du vill gå igenom samma självstudier med hjälp av Visual Studio finns information i avsnittet [Analysera webbplatsloggar med hjälp av Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
> Fler interaktiva självstudier kommer att läggas till på portalen.
> 
> 

Andra självstudier finns i:

* [Kom igång med Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Kom igång med Data Lake Analytics med hjälp av NET SDK](data-lake-analytics-get-started-net-sdk.md)
* [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) 

**Krav**

Innan du påbörjar de här självstudierna måste du ha:

* **Ett Data Lake Analytics-konto**.  Se [Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="create-data-lake-analytics-account"></a>Skapa ett Data Lake Analytics-konto
Du måste ha ett Data Lake Analytics-konto innan du kan köra några jobb.

Varje Data Lake Analytics-konto har ett kontoberoende för [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).  Det här kontot kallas Data Lake Store-standardkonto.  Du kan skapa Data Lake Store-kontot i förväg eller när du skapar ditt Data Lake Analytics-konto. I de här självstudierna skapar du Data Lake Store-kontot med Analytics-kontot.

**Om du vill skapa ett Data Lake Analytics-konto**

1. Logga in på [Azure Portal](https://portal.azure.com/signin/index/?Microsoft_Azure_Kona=true&Microsoft_Azure_DataLake=true&hubsExtension_ItemHideKey=AzureDataLake_BigStorage%2cAzureKona_BigCompute).
2. Klicka på **Microsoft Azure** i det övre vänstra hörnet så öppnas Startsidan.
3. Klicka på panelen **Marketplace**.  
4. Skriv **Azure Data Lake Analytics** i sökrutan på bladet **Allt** och tryck på **RETUR**. Du ska se **Azure Data Lake Analytics** i listan.
5. Klicka på **Azure Data Lake Analytics** från listan.
6. Klicka på **Skapa** längst ned på bladet.
7. Ange eller välj följande:
   
    ![Azure Data Lake Analytics-portalblad](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)
   
   * **Namn**: Namnge Analytics-kontot.
   * **Data Lake Store**: Varje Data Lake Analytics-konto har ett beroende Data Lake Store-konto. Data Lake Analytics-kontot och det beroende Data Lake Store-kontot måste finnas i samma Azure-datacenter. Följ anvisningarna för att skapa ett nytt Data Lake Store-konto eller välj ett befintligt.
   * **Prenumeration**: Välj den Azure-prenumeration som används för Analytics-kontot.
   * **Resursgrupp**. Välj en befintlig Azure-resursgrupp eller skapa en ny. Program består vanligtvis av flera komponenter, till exempel en webbapp, databas, databasserver, lagring och tredjepartstjänster. Med Azure Resource Manager (ARM) kan du arbeta med resurserna i ditt program som en grupp som kallas en Azure-resursgrupp. Du kan distribuera, uppdatera, övervaka eller ta bort alla resurser i programmet i en enda, samordnad åtgärd. Du använder en mall för distributionen. Mallen kan användas i olika miljöer, till exempel för testning, mellanlagring och produktion. Du kan tydliggöra fakturering för din organisation genom att visa upplyfta kostnader för hela gruppen. Mer information finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 
   * **Plats**. Välj ett Azure-datacenter för Data Lake Analytics-kontot. 
8. Välj **Fäst på Startsidan**. Detta krävs för att följa de här självstudierna.
9. Klicka på **Skapa**. Det tar dig till Startsidan i portalen. En ny panel har lagts till på hemsidan med etiketten "Distribuera Azure Data Lake Analytics". Det tar en stund att skapa ett Data Lake Analytics-konto. När kontot skapas öppnar portalen kontot på ett nytt blad.
   
    ![Azure Data Lake Analytics-portalblad](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

## <a name="run-website-log-analysis-interactive-tutorial"></a>Kör de interaktiva självstudierna för analys av webbplatsloggar
**Öppna de interaktiva självstudierna för Log Analytics för webbplatser**

1. Klicka på **Microsoft Azure** i den vänstra menyn i portalen för att öppna Startsidan.
2. Klicka på panelen som är kopplad till ditt Data Lake Analytics-konto.
3. Klicka på **Utforska interaktiva självstudier** från stapeln **Essentials**.
   
    ![Interaktiva Data Lake Analytics-självstudier](./media/data-lake-analytics-use-interactive-tutorials/data-lake-analytics-explore-interactive-tutorials.png)
4. Om du ser en orange varning som säger "Exempel inte inställda, klicka...", klicka på **Kopiera exempeldata** för att kopiera exempeldata till Data Lake Store-standardkontot. De interaktiva självstudierna behöver dessa data för att köras.
5. I bladet **Interaktiva självstudier** klickar du på **Log Analytics för webbplatser**. Portalen öppnar självstudierna i ett nytt portalblad.
6. Klicka på **1 introduktion** och följ sedan instruktionerna

## <a name="see-also"></a>Se även
* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)




<!--HONumber=Nov16_HO2-->



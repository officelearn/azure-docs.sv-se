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
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: ca88b355416b4ae4785bc69244185a67756ca916
ms.contentlocale: sv-se
ms.lasthandoff: 05/08/2017


---
# <a name="use-azure-data-lake-analytics-interactive-tutorials"></a>Använd interaktiva Azure Data Lake Analytics-självstudier
Azure Portal tillhandahåller interaktiva självstudier som hjälper dig att komma igång med Data Lake Analytics. Den här artikeln visar hur du med hjälp av självstudierna lär dig att analysera webbplatsloggar.

Andra självstudier finns i:

* [Kom igång med Data Lake Analytics med hjälp av Azure-portalen](data-lake-analytics-get-started-portal.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Kom igång med Data Lake Analytics med hjälp av NET SDK](data-lake-analytics-get-started-net-sdk.md)
* [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) 

**Krav**

Innan du påbörjar de här självstudierna måste du ha:

* **Ett Data Lake Analytics-konto**.  Se [Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="create-data-lake-analytics-account"></a>Skapa ett Data Lake Analytics-konto
Du måste ha ett Data Lake Analytics-konto innan du kan köra några jobb.

Varje Data Lake Analytics-konto har ett kontoberoende för [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md), vilket är standardkontot för Data Lake Store.  I den här självstudien skapar du Data Lake Store-standardkontot med Analytics-kontot, men du kan även skapa det i förväg om du vill.

**Skapa ett Data Lake Analytics-konto**

1. Logga in på [Azure Portal](https://portal.azure.com/signin/index/?Microsoft_Azure_Kona=true&Microsoft_Azure_DataLake=true&hubsExtension_ItemHideKey=AzureDataLake_BigStorage%2cAzureKona_BigCompute).
2. Klicka på **Microsoft Azure** i det övre vänstra hörnet så öppnas Startsidan.
3. Klicka på panelen **Marketplace**.  
4. Skriv **Azure Data Lake Analytics** i sökrutan på bladet **Allt** och tryck på **RETUR**. Du ska se **Azure Data Lake Analytics** i listan.
5. Klicka på **Azure Data Lake Analytics** från listan.
6. Klicka på **Skapa** längst ned på bladet.
7. Skriv eller välj:
   
    ![Azure Data Lake Analytics-portalblad](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)
   
   * **Namn**: Namnge Analytics-kontot.
   * **Data Lake Store**: Varje Data Lake Analytics-konto har ett beroende Data Lake Store-konto. Data Lake Analytics-kontot och det beroende Data Lake Store-kontot måste finnas i samma Azure-datacenter. Följ anvisningarna om hur du skapar ett Data Lake Store-konto, eller välj ett befintligt konto.
   * **Prenumeration**: Välj den Azure-prenumeration som används för Analytics-kontot.
   * **Resursgrupp**. Välj en befintlig Azure-resursgrupp eller skapa en ny. Programmen består vanligtvis av flera komponenter, t.ex. webbapp, databas, databasserver, lagring och tredjepartstjänster. Med Azure Resource Manager (ARM) kan du arbeta med resurserna i ditt program som en grupp som kallas en Azure-resursgrupp. Du kan distribuera, uppdatera, övervaka eller ta bort ditt programs resurser i en enda samordnad åtgärd. Du använder en mall för distributionen, och den mallen kan användas i olika miljöer, t.ex. för testning, mellanlagring och produktion. Du kan tydliggöra fakturering för din organisation genom att visa upplyfta kostnader för hela gruppen. Mer information finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 
   * **Plats**. Välj ett Azure-datacenter för Data Lake Analytics-kontot. 
8. Välj **Fäst på Startsidan**. Detta krävs för att följa de här självstudierna.
9. Klicka på **Skapa**. Det tar dig till Startsidan i portalen. En ny ikon har lagts till på hemsidan med etiketten Distribuera Azure Data Lake Analytics. Det tar en stund att skapa ett Data Lake Analytics-konto. När kontot skapas öppnar portalen kontot på ett nytt blad.
   
    ![Azure Data Lake Analytics-portalblad](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

## <a name="run-website-log-analysis-interactive-tutorial"></a>Kör de interaktiva självstudierna för analys av webbplatsloggar
**Öppna de interaktiva självstudierna för Log Analytics för webbplatser**

1. Klicka på **Microsoft Azure** i den vänstra menyn i portalen för att öppna Startsidan.
2. Klicka på panelen som är kopplad till ditt Data Lake Analytics-konto.
3. Klicka på **Utforska interaktiva självstudier** från stapeln **Essentials**.
   
    ![Interaktiva Data Lake Analytics-självstudier](./media/data-lake-analytics-use-interactive-tutorials/data-lake-analytics-explore-interactive-tutorials.png)
4. Om du ser en orange varning som säger "Exempel inte inställda, klicka...", klicka på **Kopiera exempeldata** för att kopiera exempeldata till Data Lake Store-standardkontot. De interaktiva självstudierna behöver dessa data för att köras.
5. I bladet **Interaktiva självstudier** klickar du på **Log Analytics för webbplatser**. Portalen öppnar självstudierna i ett nytt portalblad.
6. Klicka på **Introduktion** och följ sedan anvisningarna

## <a name="see-also"></a>Se även
* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure-portalen](data-lake-analytics-get-started-portal.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)



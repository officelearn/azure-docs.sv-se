---
title: "Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal | Microsoft Docs"
description: "Lär dig att skapa ett Data Lake Analytics-konto med hjälp av Azure Portal samt skapa ett Data Lake Analytics-jobb med hjälp av U-SQL och skicka jobbet. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: edmaca
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: 2722a2d72ed90ea0005362563ecaee30750c040a
ms.contentlocale: sv-se
ms.lasthandoff: 08/05/2017

---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-portal"></a>Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Lär dig hur du använder Azure Portal för att skapa Azure Data Lake Analytics-konton, definiera jobb i [U-SQL](data-lake-analytics-u-sql-get-started.md) och skicka jobb till Data Lake Analytics-tjänsten. Mer information om Data Lake Analytics finns i [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Krav

Innan du börjar följa de här självstudierna måste du ha en **Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-analytics-account"></a>Skapa ett Data Lake Analytics-konto

Nu ska du skapa ett Data Lake Analytics och ett Data Lake Store-konto på samma gång.  Det här steget är enkelt och tar bara ungefär 60 sekunder att slutföra.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **Nytt** >  **Data och analys** > **Data Lake Analytics**.
3. Välj värden för följande objekt:
   * **Namn**: Ange ett namn på ditt Data Lake Analytics-konto (endast gemena bokstäver och siffror tillåts).
   * **Prenumeration**: Välj den Azure-prenumeration som används för Analytics-kontot.
   * **Resursgrupp**. Välj en befintlig Azure-resursgrupp eller skapa en ny.
   * **Plats**. Välj ett Azure-datacenter för Data Lake Analytics-kontot.
   * **Data Lake Store**: Följ anvisningarna för att skapa ett nytt Data Lake Store-konto eller välj ett befintligt. 
4. Alternativt,kan du välja en prisnivå för ditt Data Lake Analytics-konto.
5. Klicka på **Skapa**. 


## <a name="your-first-u-sql-script"></a>Skriv ditt första U-SQL-skript

Följande text är ett enkelt U-SQL-skript. Allt den gör är att definiera en liten datamängd i skriptet och sedan skriva datauppsättningen till standard Data Lake Store som en fil med namnet `/data.csv`.

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-u-sql-job"></a>Skicka ett U-SQL-jobb

1. Välj Data Lake Analytics-konto och klicka på **Nytt jobb**.
2. Klistra in texten med U-SQL-skriptet från tidigare. 
3. Klicka på **Skicka jobb**.   
4. Vänta tills jobbstatusen har ändrats till **Klar**.
5. Om jobbet misslyckades, se [Övervaka och felsöka Data Lake Analytics-jobb](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).
6. Klicka på fliken **Utdata** och klicka sedan på `data.csv`. 

## <a name="see-also"></a>Se även

* Information om att utveckla U-SQL-program finns i [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Information om U-SQL finns i [Kom igång med U-SQL-språk i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Information om hanteringsuppgifter finns i [Hantera Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-manage-use-portal.md).


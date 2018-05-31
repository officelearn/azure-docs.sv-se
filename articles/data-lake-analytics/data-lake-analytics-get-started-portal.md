---
title: Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal
description: Lär dig att skapa ett Data Lake Analytics-konto med hjälp av Azure Portal samt skapa ett Data Lake Analytics-jobb med hjälp av U-SQL och skicka jobbet.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.service: data-lake-analytics
ms.topic: hero-article
ms.workload: big-data
ms.date: 03/21/2017
ms.openlocfilehash: 63fc4272dc4b26e9eb4846118bc57cd6aa5bf361
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358293"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-portal"></a>Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Lär dig hur du använder Azure Portal för att skapa Azure Data Lake Analytics-konton, definiera jobb i [U-SQL](data-lake-analytics-u-sql-get-started.md) och skicka jobb till Data Lake Analytics-tjänsten. Mer information om Data Lake Analytics finns i [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar följa de här självstudierna måste du ha en **Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-analytics-account"></a>Skapa ett Data Lake Analytics-konto

Nu ska du skapa ett Data Lake Analytics och ett Data Lake Store-konto på samma gång.  Det här steget är enkelt och tar bara ungefär 60 sekunder att slutföra.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **Skapa en resurs** >  **Data och analys** > **Data Lake Analytics**.
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

1. Välj **Nytt jobb** från Data Lake Analytics-kontot.
2. Klistra in texten från U-SQL-skriptet ovan. Ge jobbet ett namn. 
3. Starta jobbet genom att välja **Skicka**.   
4. Övervaka jobbets **status** och vänta tills statusen ändras till **Lyckades**.
5. Välj fliken **Data** och välj sedan fliken **Utdata**. Välj utdatafilen med namnet `data.csv` och visa filens utdata.

## <a name="see-also"></a>Se även

* Information om att utveckla U-SQL-program finns i [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Information om U-SQL finns i [Kom igång med U-SQL-språk i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Information om hanteringsuppgifter finns i [Hantera Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-manage-use-portal.md).

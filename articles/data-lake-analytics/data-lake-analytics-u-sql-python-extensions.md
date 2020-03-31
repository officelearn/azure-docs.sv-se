---
title: Utöka U-SQL-skript med Python i Azure Data Lake Analytics
description: Lär dig hur du kör Python-kod i U-SQL-skript med Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 0a49cbdb4caf474d0628fea3679ce712d37886e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60813415"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>Utöka U-SQL-skript med Python-kod i Azure Data Lake Analytics

## <a name="prerequisites"></a>Krav

Innan du börjar kontrollerar du att Python-tilläggen är installerade i ditt Azure Data Lake Analytics-konto.

* Navigera till dig Data Lake Analytics-konto i Azure-portalen
* I den vänstra menyn, under **KOMMA IGÅNG** klicka på **Exempel skript**
* Klicka på **Installera U-SQL-tillägg** och sedan **OK**

## <a name="overview"></a>Översikt 

Python-tillägg för U-SQL gör det möjligt för utvecklare att utföra massivt parallell körning av Python-kod. I följande exempel visas de grundläggande stegen:

* Använd `REFERENCE ASSEMBLY` uttrycket för att aktivera Python-tillägg för U-SQL Script
* Använda `REDUCE` åtgärden för att partitionera indata på en nyckel
* Python-tilläggen för U-SQL innehåller en inbyggd`Extension.Python.Reducer`reducer () som kör Python-kod på varje hörn som tilldelats reducer
* U-SQL-skriptet innehåller den inbäddade Python-koden som har en funktion som kallas `usqlml_main` som accepterar en pandas DataFrame som indata och returnerar en pandas DataFrame som utdata.

--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-python-integrates-with-u-sql"></a>Så här integreras Python med U-SQL

### <a name="datatypes"></a>Datatyper

* Sträng och numeriska kolumner från U-SQL konverteras som-är mellan Pandor och U-SQL
* U-SQL Nulls konverteras till och `NA` från Pandas-värden

### <a name="schemas"></a>Scheman

* Indexvektorer i Pandor stöds inte i U-SQL. Alla indataramar i Python-funktionen har alltid ett 64-bitars numeriskt index från 0 till antalet rader minus 1. 
* U-SQL-datauppsättningar kan inte ha dubblettkolumnnamn
* U-SQL-datauppsättningar kolumnnamn som inte är strängar. 

### <a name="python-versions"></a>Python-versioner
Endast Python 3.5.1 (kompilerad för Windows) stöds. 

### <a name="standard-python-modules"></a>Standard Python-moduler
Alla standardModuler för Python ingår.

### <a name="additional-python-modules"></a>Ytterligare Python-moduler
Förutom standardktona Python-bibliotek ingår flera vanliga pythonbibliotek:

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>Undantagsmeddelanden
För närvarande visas ett undantag i Python-kod som allmänt hörnfel. I framtiden kommer U-SQL Job felmeddelanden att visa Python undantagsmeddelande.

### <a name="input-and-output-size-limitations"></a>Begränsningar för in- och utdatastorlek
Varje hörn har en begränsad mängd minne som tilldelats den. För närvarande är den gränsen 6 GB för en AU. Eftersom dataramarna för in- och utdata måste finnas i minnet i Python-koden får den totala storleken för indata och utdata inte överstiga 6 GB.

## <a name="see-also"></a>Se även
* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Använda U-SQL-fönsterfunktioner för Azure Data Lake Analytics-jobb](data-lake-analytics-use-window-functions.md)
* [Använda Azure Data Lake Tools för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)

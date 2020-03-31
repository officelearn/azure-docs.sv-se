---
title: Kör Apache Base-frågor i Azure HDInsight med Apache Phoenix
description: Lär dig hur du använder Apache Zeppelin för att köra Apache Base-frågor med Phoenix.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 28eeb446e55213f1ffa0a638878f6432fd15a05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72392246"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Använd Apache Zeppelin för att köra Apache Phoenix-frågor via Apache HBase i Azure HDInsight

Apache Phoenix är en öppen källkod, massivt parallella relationella databaslager byggt på HBase. Phoenix kan du använda SQL som frågor över HBase. Phoenix använder JDBC-drivrutiner under för att du ska kunna skapa, ta bort, ändra SQL-tabeller, index, vyer och sekvenser.  Du kan också använda Phoenix för att uppdatera rader individuellt och i grupp. Phoenix använder en NOSQL native kompilering i stället för att använda MapReduce för att kompilera frågor, vilket gör det möjligt att skapa program med låg latens ovanpå HBase.

Apache Zeppelin är en webbaserad bärbar dator med öppen källkod som gör att du kan skapa datadrivna, samarbetsdokument med hjälp av interaktiva dataanalyser och språk som SQL och Scala. Det hjälper datautvecklare & dataforskare utveckla, organisera, köra och dela kod för datamanipulering. Det gör att du kan visualisera resultat utan att hänvisa till kommandoraden eller behöver klusterinformation.

HDInsight-användare kan använda Apache Zeppelin för att fråga Phoenix-tabeller. Apache Zeppelin är integrerad med HDInsight-kluster och det finns inga ytterligare steg för att använda det. Skapa bara en Zeppelin Notebook med JDBC-tolk och börja skriva dina Phoenix SQL-frågor

## <a name="prerequisites"></a>Krav

Ett Apache HBase-kluster på HDInsight. Se [Kom igång med Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Skapa en Apache Zeppelin-anteckning

1. Ersätt `CLUSTERNAME` med namnet på klustret `https://CLUSTERNAME.azurehdinsight.net/zeppelin`i följande URL . Ange sedan webbadressen i en webbläsare. Ange användarnamn och lösenord för klusterinloggning.

1. På Zeppelin-sidan väljer du **Skapa ny anteckning**.

    ![HDInsight interaktiv fråga zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. Skriv eller välj följande värden i dialogrutan **Skapa ny anteckning:**

    - Namn: Ange ett namn på anteckningen.
    - Standardtolk: Välj **jdbc** i listrutan.

    Välj sedan **Skapa anteckning**.

1. Kontrollera att anteckningsbokshuvudet visar en ansluten status. Det betecknas med en grön prick i det övre högra hörnet.

    ![Zeppelin-status för anteckningsbok](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Zeppelin-status för anteckningsbok")

1. Skapa en HBase-tabell. Ange följande kommando och tryck sedan på **Skift + Retur:**

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    **%jdbc(phoenix)** i den första raden talar om för anteckningsboken att använda Phoenix JDBC-tolken.

1. Visa skapade tabeller.

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. Infoga värden i tabellen.

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. Fråga tabellen.

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. Ta bort en post.

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. Släpp bordet.

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>Nästa steg

- [Apache Phoenix stöder nu Zeppelin i Azure HDInsight](https://blogs.msdn.microsoft.com/ashish/2018/08/17/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight/)
- [Apache Phoenix grammatik](https://phoenix.apache.org/language/index.html)

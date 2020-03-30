---
title: Utforska data i Hive-tabeller med Hive-frågor - Team Data Science Process
description: Använd exempel på Hive-skript som används för att utforska data i Hive-tabeller i ett HDInsight Hadoop-kluster.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 75dce2b5a83d13fe4a7d166595456e9a8d6324ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722177"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Utforska data i Hive-tabeller med Hive-frågor

Den här artikeln innehåller exempel på Hive-skript som används för att utforska data i Hive-tabeller i ett HDInsight Hadoop-kluster.

Den här uppgiften är ett steg i [teamdatavetenskapsprocessen](overview.md).

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du har:

* Skapade ett Azure-lagringskonto. Om du behöver instruktioner läser du [Skapa ett Azure Storage-konto](../../storage/common/storage-account-create.md)
* Etablerat ett anpassat Hadoop-kluster med HDInsight-tjänsten. Om du behöver instruktioner läser du [Anpassa Azure HDInsight Hadoop-kluster för avancerad analys](customize-hadoop-cluster.md).
* Data har överförts till Hive-tabeller i Azure HDInsight Hadoop-kluster. Om den inte har gjort det följer du instruktionerna i [Skapa och läser in data till Hive-tabeller](move-hive-tables.md) för att ladda upp data till Hive-tabeller först.
* Aktiverad fjärråtkomst till klustret. Om du behöver instruktioner läser du [Komma åt huvudnoden för Hadoop-kluster](customize-hadoop-cluster.md).
* Om du behöver instruktioner om hur du skickar Hive-frågor läser du [Så här skickar du Hive-frågor](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Exempel på Hive-frågeskript för datautforskning
1. Få antalet observationer per partition`SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Få antalet observationer per dag`SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Hämta nivåerna i en kategorisk kolumn  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Hämta antalet nivåer i kombination med två kategoriska kolumner`SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Hämta fördelningen för numeriska kolumner  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Extrahera poster från att sammanfoga två tabeller
   
        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Ytterligare frågeskript för datascenarier för taxiresor
Exempel på frågor som är specifika för [NYC Taxi Trip Data](https://chriswhong.com/open-data/foil_nyc_taxi/) scenarier finns också i [GitHub-databasen](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Dessa frågor har redan dataschemat angivet och är redo att skickas för att köras.


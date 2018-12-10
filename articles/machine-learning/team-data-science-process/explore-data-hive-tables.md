---
title: Utforska data i Hive-tabeller med Hive-frågor – Team Data Science Process
description: Använd Hive exempelskript som används för att utforska data i Hive-tabeller i ett HDInsight Hadoop-kluster.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c4d731ad2b94ca662e23e7e48442e396900d68ec
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53132302"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Utforska data i Hive-tabeller med Hive-frågor

Den här artikeln innehåller exempel Hive-skript som används för att utforska data i Hive-tabeller i ett HDInsight Hadoop-kluster.

Den här uppgiften är ett steg i den [Team Data Science Process](overview.md).

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har:

* Skapa ett Azure storage-konto. Om du behöver anvisningar läser [skapa ett Azure Storage-konto](../../storage/common/storage-quickstart-create-account.md)
* Etablerat en anpassade Hadoop-kluster med HDInsight-tjänsten. Om du behöver mer information, se [anpassa Azure HDInsight Hadoop-kluster för Advanced Analytics](customize-hadoop-cluster.md).
* Data har överförts till Hive-tabeller i Azure HDInsight Hadoop-kluster. Om den inte har det, följ instruktionerna i [skapa och läsa in data till Hive-tabeller](move-hive-tables.md) först överföra data till Hive-tabeller.
* Aktivera fjärråtkomst till klustret. Om du behöver mer information, se [åt Head noden av Hadoop-klustret](customize-hadoop-cluster.md).
* Om du behöver mer information om hur du gör Hive-frågor, se [så skicka Hive-frågor](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Exempelskript Hive-fråga för datautforskning
1. Få ett värde för observationer per partition  `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Få ett värde för observationer per dag  `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Hämta nivåer i en kategoriska kolumn  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Hämta antalet nivåer i kombination med två kategoriska kolumner  `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Hämta fördelningen för numeriska kolumner  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Extrahera poster från två tabeller
   
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

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Ytterligare skript för taxi resans datascenarier
Exempel på frågor som är specifika för [NYC Taxi Resedata](http://chriswhong.com/open-data/foil_nyc_taxi/) scenarier finns också i [GitHub-lagringsplatsen](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). De här frågorna har dataschema som angetts redan och är redo att skickas för att köra.


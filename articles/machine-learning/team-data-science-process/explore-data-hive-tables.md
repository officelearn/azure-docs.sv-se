---
title: "Utforska data i Hive-tabeller med Hive-frågor | Microsoft Docs"
description: "Utforska data i Hive-tabeller med hjälp av Hive-frågor."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 0d46cea5-2b4c-4384-9bfa-fa20f6f75148
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: dc5cbbf8db46607179e8b0e8657462afac21f7da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Utforska data i Hive-tabeller med Hive-frågor
Det här dokumentet innehåller exempel på Hive-skript som används för att utforska data i Hive-tabeller i ett HDInsight Hadoop-kluster.

Följande **menyn** länkar till avsnitt som beskriver hur du använder Verktyg för att utforska data från olika miljöer för lagring.

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du har:

* Skapa ett Azure storage-konto. Om du behöver mer information, se [skapa ett Azure Storage-konto](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Etablera ett anpassat Hadoop-kluster med HDInsight-tjänst. Om du behöver mer information, se [anpassa Azure HDInsight Hadoop-kluster för Advanced Analytics](customize-hadoop-cluster.md).
* Data har överförts till Hive-tabeller i Azure HDInsight Hadoop-kluster. Om det inte har det, följ instruktionerna i [skapa och läsa in data till Hive-tabeller](move-hive-tables.md) först överföra data till Hive-tabeller.
* Aktivera fjärråtkomst till klustret. Om du behöver mer information, se [komma åt det Head nod för Hadoop-kluster](customize-hadoop-cluster.md#headnode).
* Om du behöver information om hur du skicka Hive-frågor finns [så skicka Hive-frågor](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Exempelskript Hive-fråga för datagranskning
1. Hämta antal observationer per partition`SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Hämta antal observationer per dag`SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Hämta nivåerna i en kategoriska kolumn  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Hämta antalet nivåer i kombination med två kategoriska kolumner`SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Hämta distribution för numeriska kolumner  
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

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Ytterligare fråga skript för taxi resa datascenarier
Exempel på frågor som är specifika för [NYC Taxi resa Data](http://chriswhong.com/open-data/foil_nyc_taxi/) scenarier finns också i [GitHub-lagringsplatsen](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). De här frågorna har angetts dataschemat redan och är redo att skickas till kör.


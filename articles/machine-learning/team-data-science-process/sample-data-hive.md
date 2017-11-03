---
title: "Exempel på data i Azure HDInsight Hive-tabeller | Microsoft Docs"
description: Ned provtagning data i Azure HDInsight (Hadopop) Hive-tabeller
services: machine-learning,hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: f31e8d01-0fd4-4a10-b1a7-35de3c327521
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: hangzh;bradsev
ms.openlocfilehash: 357307a034b277e8c37e99bda1ed6a9a76e13f41
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Exempeldata i Azure HDInsight Hive-tabeller
I den här artikeln beskrivs vi hur du ned-sample data som lagras i Azure HDInsight Hive-tabeller med hjälp av Hive-frågor. Det omfattar tre provtagningsmetoder för vilket populärt används:

* Enhetlig slumpmässig provtagning
* Slumpmässigt urval av grupper
* Stratified provtagning

Följande **menyn** länkar till avsnitt som beskriver hur du exempeldata från olika miljöer för lagring.

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**Varför exempel dina data?**
Om datamängden som du planerar att analysera är stort, men det är vanligtvis en bra idé att ned-sample data för att minska det till en mindre men representativt och mer användarvänlig storlek. Detta underlättar data förstå undersökning och funktionen tekniker. Roll i teamet datavetenskap processen är att aktivera snabb prototyper för databearbetning funktions- och maskininlärning modeller.

Sampling uppgiften är ett steg i den [Team Data vetenskap processen (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="how-to-submit-hive-queries"></a>Hur du skickar in Hive-frågor
Du kan skicka hive-frågor från kommandoraden för Hadoop-konsolen på huvudnod Hadoop-kluster. Gör detta genom att logga in huvudnod Hadoop-kluster, öppna konsolen Hadoop kommandoraden och skicka Hive-frågor därifrån. Anvisningar för att skicka Hive-frågor i konsolen Hadoop kommandoraden finns [så skicka Hive-frågor](move-hive-tables.md#submit).

## <a name="uniform"></a>Enhetlig slumpmässig provtagning
Enhetligt slumpmässiga urval innebär att varje rad i datamängden som har ett lika risken för att sampla. Detta kan implementeras genom att lägga till ett extra fält rand() datauppsättningen i frågan ”Välj” inre och yttre ”Välj” frågan villkoret på slumpmässiga fältet.

Här är en exempelfråga:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

Här kan `<sample rate, 0-1>` anger andelen poster som användare vill använda som exempel.

## <a name="group"></a>Slumpmässigt urval av grupper
När provtagning kategoriska data, du kanske vill inkludera eller exkludera alla instanser av vissa specifika värdet för en kategoriska variabel. Detta är begreppet ”sampling av grupp”.
Om du har en kategoriska variabel ”tillstånd” som har värden NY, MA, CA, NJ, PA osv, önskade poster för samma tillstånd alltid vara tillsammans, om de samplas eller inte.

Här är en exempelfråga som exempel av grupp:

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified"></a>Stratified provtagning
Slumpmässig provtagning är stratified med avseende på en kategoriska variabel när de prov som erhålls har värden för att kategoriska som finns i samma förhållandet som överordnade populationen fick exemplen. I det här exemplet som ovan, anta att dina data har underordnade population av tillstånd, säg NJ har 100 observationer, NY har 60 observationer och WA har 300 observationer. Om du anger mängden stratified provtagning vara 0,5 bör sedan exemplet fick ha ungefär 50, 30 och 150 observationer av Dr, NY och WA respektive.

Här är en exempelfråga:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
              rank() over (partition by state order by rand()) as state_rank
          from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Mer information om avancerade provtagningsmetoder som är tillgängliga i Hive finns [LanguageManual provtagning](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).


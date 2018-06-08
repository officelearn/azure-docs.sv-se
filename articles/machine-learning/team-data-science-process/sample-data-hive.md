---
title: Exempel på data i Azure HDInsight Hive-tabeller | Microsoft Docs
description: Ned provtagning data i Azure HDInsight (Hadopop) Hive-tabeller
services: machine-learning,hdinsight
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: f31e8d01-0fd4-4a10-b1a7-35de3c327521
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 558f7d684453c8b5040f586820bd2a8a9ac0f9c8
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838440"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Exempeldata i Azure HDInsight Hive-tabeller
Den här artikeln beskriver hur du ned-sample data som lagras i Azure HDInsight Hive-tabeller som använder Hive-frågor för att minska det till en mer användarvänlig för analys storlek. Den omfattar tre provtagningsmetoder för vilket populärt används:

* Enhetlig slumpmässig provtagning
* Slumpmässigt urval av grupper
* Stratified provtagning

Följande **menyn** länkar till avsnitt som beskriver hur du exempeldata från olika miljöer för lagring.

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**Varför exempel dina data?**
Om datamängden som du planerar att analysera är stort, men det är vanligtvis en bra idé att ned-sample data för att minska det till en mindre men representativt och mer användarvänlig storlek. Ned provtagning underlättar data förstå undersökning och funktionen tekniker. Roll i teamet datavetenskap processen är att aktivera snabb prototyper för databearbetning funktions- och maskininlärning modeller.

Sampling uppgiften är ett steg i den [Team Data vetenskap processen (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="how-to-submit-hive-queries"></a>Hur du skickar in Hive-frågor
Du kan skicka hive-frågor från Hadoop kommandoradskonsol i huvudnod i Hadoop-kluster. Gör detta genom att logga in huvudnod Hadoop-kluster, öppna Hadoop kommandoradskonsol och skicka Hive-frågor därifrån. Anvisningar för att skicka Hive-frågor i Hadoop kommandoradskonsol finns [så skicka Hive-frågor](move-hive-tables.md#submit).

## <a name="uniform"></a> Enhetlig slumpmässig provtagning
Enhetligt slumpmässiga urval innebär att varje rad i datamängden som har ett lika risken för att sampla. Den kan implementeras genom att lägga till ett extra fält rand() datauppsättningen i frågan ”Välj” inre och yttre ”Välj” frågan villkoret på slumpmässiga fältet.

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

## <a name="group"></a> Slumpmässigt urval av grupper
När provtagning kategoriska data, du kanske vill inkludera eller exkludera alla instanser för vissa kategoriska variabelns värde. Den här typen av provtagning kallas ”sampling av grupp”. Om du har en kategoriska variabel till exempel ”*tillstånd*”, som innehåller värden som NY, MA, CA, NJ och PA, om du vill att posterna från varje tillstånd så att förekomma tillsammans, om de samplas eller inte.

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
Slumpmässig provtagning stratified med avseende på en kategoriska variabel när de prov som erhålls har kategoriska värden som finns i samma förhållandet som de var i överordnade populationen. Med det här exemplet som ovan, anta att dina data har anmärkningar utfärda: NJ har 100 observationer, NY har 60 observationer och WA har 300 observationer. Om du anger mängden stratified provtagning vara 0,5 bör sedan exemplet fick ha ungefär 50, 30 och 150 observationer av Dr, NY och WA respektive.

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


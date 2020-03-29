---
title: Exempel på data i Azure HDInsight Hive-tabeller – Team Data Science Process
description: Ned-exempeldata som lagras i Azure HDInsight Hive-tabeller med Hive-frågor för att minska data till en storlek som kan hanteras mer för analys.
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
ms.openlocfilehash: df85edc3de00e2b0342bc3102fe9e85564a9835b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720001"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Exempeldata i Azure HDInsight Hive-tabeller
I den här artikeln beskrivs hur du nedextpelsera data som lagras i Azure HDInsight Hive-tabeller med Hive-frågor för att minska den till en storlek som kan hanteras mer för analys. Det omfattar tre populära provtagningsmetoder:

* Enhetlig slumpmässig provtagning
* Slumpmässigt urval efter grupper
* Stratifierad provtagning

**Varför ta prov på dina data?**
Om datauppsättningen som du planerar att analysera är stor är det oftast en bra idé att nedexpför att ta bort data för att minska dem till en mindre men representativ och mer hanterbar storlek. Ned-provtagning underlättar dataförståelse, utforskning och funktionsteknik. Dess roll i Team Data Science Process är att möjliggöra snabb prototyper av databehandlingsfunktioner och maskininlärningsmodeller.

Den här samplingsuppgiften är ett steg i [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="how-to-submit-hive-queries"></a>Så här skickar du Hive-frågor
Hive-frågor kan skickas från Hadoop Command-Line-konsolen på huvudnoden för Hadoop-klustret.  Logga in i huvudnoden för Hadoop-klustret, öppna Hadoop Command-Line-konsolen och skicka Hive-frågorna därifrån. Instruktioner om hur du skickar Hive-frågor i Hadoop Command-Line-konsolen finns i [Så här skickar du Hive-frågor](move-hive-tables.md#submit).

## <a name="uniform-random-sampling"></a><a name="uniform"></a>Enhetlig slumpmässig provtagning
Enhetlig slumpmässig provtagning innebär att varje rad i datauppsättningen har lika stor chans att provtas. Det kan implementeras genom att lägga till ett extra fält rand() till datauppsättningen i den inre "välj"-frågan och i den yttre "välj"-frågan som villkorar det slumpmässiga fältet.

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

Här `<sample rate, 0-1>` anger andelen poster som användarna vill prova.

## <a name="random-sampling-by-groups"></a><a name="group"></a>Slumpmässigt urval efter grupper
När du tar kategoriska data för sampling kanske du vill inkludera eller utesluta alla instanser för något värde i den kategoriska variabeln. Denna typ av provtagning kallas "provtagning per grupp". Om du till exempel har en kategorisk variabel "*Tillstånd*", som har värden som NY, MA, CA, NJ och PA, vill du att poster från varje tillstånd ska vara tillsammans, oavsett om de samplas eller inte.

Här är en exempelfråga som exempel efter grupp:

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

## <a name="stratified-sampling"></a><a name="stratified"></a>Stratifierad provtagning
Slumpmässigt urval stratifieras med avseende på en kategorisk variabel när de erhållna proverna har kategoriska värden som finns i samma förhållande som i den överordnade populationen. Med samma exempel som ovan, anta att dina data har följande observationer av stater: NJ har 100 observationer, NY har 60 observationer och WA har 300 observationer. Om du anger hur frekvensen stratifierad provtagning är 0,5, bör det erhållna provet ha cirka 50, 30 respektive 150 observationer av NJ, NY respektive WA.

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


Information om mer avancerade urvalsmetoder som finns i Hive finns i [LanguageManual Sampling](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).


---
title: Exempeldata i Azure HDInsight Hive-tabeller - Team Data Science Process
description: Nedåtsampla data som lagras i Azure HDInsight Hive-tabeller som använder Hive-frågor för att minska data till en storlek som är mer hanterbara för analys.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c417950e07ae3c6922aa260a3ef40d862870aa1e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55452336"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Exempeldata i Azure HDInsight Hive-tabeller
Den här artikeln beskriver hur du nedåtsampla data som lagras i Azure HDInsight Hive-tabeller som använder Hive-frågor för att minska det till en storlek som är mer hanterbara för analys. Det omfattar tre metoder för vilket populärt används sampling:

* Enhetligt slumpmässigt urval
* Slumpmässigt urval av grupper
* Stratified sampling

**Varför prov på dina data?**
Om datauppsättningen som du planerar att analysera är stor, men det är oftast en bra idé att nedåtsampla data för att minska det till en mindre men representativa och mer hanterbara storlek. Ned sampling underlättar förståelse av data, utforskning och funktioner. Dess roll i Team Data Science Process är att snabbt skapa prototyper för bearbetning av funktions- och machine learning-modeller.

Den här aktiviteten för sampling är ett steg i den [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="how-to-submit-hive-queries"></a>Hur du skickar in Hive-frågor
Du kan skicka hive-frågor från Hadoop kommandoradskonsol på huvudnoden för Hadoop-kluster. Gör detta genom att logga in huvudnoden för Hadoop-kluster, öppna Hadoop kommandoradskonsol och skicka Hive-frågor därifrån. Anvisningar för att skicka Hive-frågor i Hadoop kommandoradskonsol finns i [så skicka Hive-frågor](move-hive-tables.md#submit).

## <a name="uniform"></a> Enhetligt slumpmässigt urval
Enhetligt slumpmässigt urval innebär att varje rad i datauppsättningen har samma chans urvalet. Det kan implementeras genom att lägga till ett extra fält SLUMP() datauppsättningen i den inre ”Välj” frågan och i yttre ”Välj” frågan villkoret slumpmässiga fältet.

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

Här kan `<sample rate, 0-1>` anger andelen av poster som användare vill ska samlas in.

## <a name="group"></a> Slumpmässigt urval av grupper
När kategoriska sampling av data kan du antingen inkludera eller exkludera alla instanser för vissa kategoriska variabelns värde. Den här typen av sampling kallas ”sampling av grupp”. Exempel: Om du har en variabel som kategoriska ”*tillstånd*”, som har värden som NY, MA, CA, NJ och PA kan du ha poster från varje tillstånd är tillsammans, oavsett om de samplas eller inte.

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

## <a name="stratified"></a>Stratified sampling
Stickprov stratified med avseende på en kategoriska variabel när de exempel som fick har kategoriska värden som finns i samma förhållandet som de var i överordnade populationen. Om du använder samma exempel som ovan, anta att dina data har följande observationer per tillstånd: NJ har 100 observationer, NY har 60 observationer och WA har 300 observationer. Om du anger mängden stratified sampling är 0,5 bör sedan exemplet fick ha ungefär 50, 30 och 150 observationer av NJ och NY WA respektive.

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


Information om mer avancerade sampling metoder som är tillgängliga i Hive finns i [LanguageManual Sampling](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).


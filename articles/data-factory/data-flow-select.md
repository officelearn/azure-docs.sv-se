---
title: Välj omformning i mappning av dataflöde
description: Azure Data Factory-mappningsdataflöde Välj omvandling
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/18/2020
ms.openlocfilehash: a90a2def874c7f081f83a34aea956083eb72879a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686503"
---
# <a name="select-transformation-in-mapping-data-flow"></a>Välj omformning i mappning av dataflöde

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Använd markeringsomformningen för att byta namn på, släppa eller ändra ordning på kolumner. Den här omvandlingen ändrar inte raddata, men väljer vilka kolumner som sprids nedströms. 

I en urvalsomvandling kan användare ange fasta mappningar, använda mönster för att utföra regelbaserad mappning eller aktivera automatisk mappning. Fasta och regelbaserade mappningar kan båda användas i samma urvalsomvandling. Om en kolumn inte matchar en av de definierade mappningarna tas den bort.

## <a name="fixed-mapping"></a>Fast mappning

Om färre än 50 kolumner har definierats i projektionen har alla definierade kolumner en fast mappning som standard. En fast mappning tar en definierad, inkommande kolumn och mappar den till ett exakt namn.

![Fast mappning](media/data-flow/fixedmapping.png "Fast mappning")

> [!NOTE]
> Du kan inte mappa eller byta namn på en borttuffad kolumn med hjälp av en fast mappning

### <a name="mapping-hierarchical-columns"></a>Mappa hierarkiska kolumner

Fasta mappningar kan användas för att mappa en underkolumn i en hierarkisk kolumn till en kolumn på den översta nivån. Om du har en definierad hierarki använder du kolumnrullgardermenyn för att välja en underkolumn. Urvalsomvandlingen skapar en ny kolumn med underkolumnens värde och datatyp.

![hierarkisk mappning](media/data-flow/select-hierarchy.png "hierarkisk mappning")

## <a name="rule-based-mapping"></a>Regelbaserad mappning

Om du vill mappa många kolumner samtidigt eller skicka bortdrivna kolumner nedströms använder du regelbaserad mappning för att definiera mappningar med hjälp av kolumnmönster. Matcha baserat `name`på `type` `stream`kolumnerna `position` , , och. Du kan ha valfri kombination av fasta och regelbaserade mappningar. Som standard kommer alla prognoser med större än 50 kolumner som standard att en regelbaserad mappning som matchar på varje kolumn och matar ut det indataade namnet. 

Om du vill lägga till en regelbaserad mappning klickar du på **Lägg till mappning** och väljer **Regelbaserad mappning**.

![regelbaserad mappning](media/data-flow/rule2.png "Regelbaserad mappning")

Varje regelbaserad mappning kräver två indata: villkoret som ska matchas efter och vad som ska namnges varje mappad kolumn. Båda värdena matas in via [uttrycksverktyget](concepts-data-flow-expression-builder.md). Ange ditt booleska matchningsvillkor i rutan vänster uttryck. I rutan för rätt uttryck anger du vad den matchade kolumnen ska mappas till.

![regelbaserad mappning](media/data-flow/rule-based-mapping.png "Regelbaserad mappning")

Använd `$$` syntaxen för att referera till indatanamnet för en matchad kolumn. Använd ovanstående bild som exempel, säg att en användare vill matcha på alla strängkolumner vars namn är kortare än sex tecken. Om en inkommande `test`kolumn har `$$ + '_short'` fått namnet `test_short`byter uttrycket namn på kolumnen . Om det är den enda mappningen som finns, kommer alla kolumner som inte uppfyller villkoret att tas bort från utdata.

Mönster matchar både drivoch definierade kolumner. Om du vill se vilka definierade kolumner som mappas av en regel klickar du på glasögonikonen bredvid regeln. Verifiera utdata med hjälp av förhandsgranskning av data.

### <a name="regex-mapping"></a>Regex-mappning

Om du klickar på ikonen nedåt kan du ange ett villkor för regex-mappning. Ett regex-mappningsvillkor matchar alla kolumnnamn som matchar det angivna regex-villkoret. Detta kan användas i kombination med standardregelbaserade mappningar.

![regelbaserad mappning](media/data-flow/regex-matching.png "Regelbaserad mappning")

Exemplet ovan matchar på `(r)` regex-mönstret eller ett kolumnnamn som innehåller ett gemener r. I likhet med standardregelbaserad mappning ändras alla matchade kolumner `$$` av villkoret till höger med syntaxen.

Om du har flera regex-matchningar i kolumnnamnet `$n` kan du referera till specifika matchningar med var 'n' refererar till vilken matchning. Till exempel refererar '$2' till den andra matchningen i ett kolumnnamn.

### <a name="rule-based-hierarchies"></a>Regelbaserade hierarkier

Om den definierade projektionen har en hierarki kan du använda regelbaserad mappning för att mappa underkolumnerna för hierarkier. Ange ett matchande villkor och den komplexa kolumn vars underkolumner du vill mappa. Varje matchad underkolumn matas ut med regeln "Namn som" som anges till höger.

![regelbaserad mappning](media/data-flow/rule-based-hierarchy.png "Regelbaserad mappning")

Exemplet ovan matchar på alla underkolumner i komplex kolumn `a`. `a`innehåller två `b` underkolumner `c`och . Utdataschemat kommer `b` att `c` innehålla två kolumner och `$$`som villkoret "Namn som" är .

### <a name="parameterization"></a>Parameterisering

Du kan parametriera kolumnnamn med hjälp av regelbaserad mappning. Använd nyckelordet ```name``` för att matcha inkommande kolumnnamn mot en parameter. Om du till exempel har ```mycolumn```en parameter för dataflöde kan du skapa ```mycolumn```en regel som matchar alla kolumnnamn som är lika med . Du kan byta namn på den matchade kolumnen till en hårdkodad sträng som "affärsnyckel" och referera till den uttryckligen. I det här exemplet ```name == $mycolumn``` är det matchande villkoret och namnvillkoret är "affärsnyckel". 

## <a name="auto-mapping"></a>Automatisk mappning

När du lägger till en markera omvandling kan **automatisk mappning** aktiveras genom att växla skjutreglaget Automatisk mappning. Med automatisk mappning mappar välja omformningen alla inkommande kolumner, exklusive dubbletter, med samma namn som indata. Detta inkluderar frånfallna kolumner, vilket innebär att utdata kan innehålla kolumner som inte har definierats i schemat. Mer information om bortträngda kolumner finns i [schemadrift](concepts-data-flow-schema-drift.md).

![Automatisk mappning](media/data-flow/automap.png "Automatisk mappning")

När automatisk mappning är aktiverat kommer den valda omvandlingen att hedra inställningarna för hoppa över dubblett och ge ett nytt alias för de befintliga kolumnerna. Aliasing är användbart när du gör flera kopplingar eller sökninger på samma ström och i självkopplingsscenarier. 

## <a name="duplicate-columns"></a>Duplicera kolumner

Som standard tappar välja omformningen dubblettkolumner i både indata- och utdataprojektionen. Dubblettindatakolumner kommer ofta från kopplings- och uppslagsomformningar där kolumnnamn dupliceras på varje sida av kopplingen. Dubblettutdatakolumner kan uppstå om du mappar två olika indatakolumner till samma namn. Välj om du vill släppa eller skicka in dubblettkolumner genom att växla kryssrutan.

![Hoppa över dubbletter](media/data-flow/select-skip-dup.png "Hoppa över dubbletter")

## <a name="ordering-of-columns"></a>Beställning av kolumner

Ordningen på mappningarna bestämmer ordningen på utdatakolumnerna. Om en indatakolumn mappas flera gånger kommer endast den första mappningen att uppfyllas. För en dubblettkolumn som släpps kommer den första matchen att behållas.

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    select(mapColumn(
        each(<hierarchicalColumn>, match(<matchCondition>), <nameCondition> = $$), ## hierarchical rule-based matching
        <fixedColumn>, ## fixed mapping, no rename
        <renamedFixedColumn> = <fixedColumn>, ## fixed mapping, rename
        each(match(<matchCondition>), <nameCondition> = $$), ## rule-based mapping
        each(patternMatch(<regexMatching>), <nameCondition> = $$) ## regex mapping
    ),
    skipDuplicateMapInputs: { true | false },
    skipDuplicateMapOutputs: { true | false }) ~> <selectTransformationName>
```

### <a name="example"></a>Exempel

Nedan är ett exempel på en urvalsmappning och dess dataflödesskript:

![Välj skriptexempel](media/data-flow/select-script-example.png "Välj skriptexempel")

```
DerivedColumn1 select(mapColumn(
        each(a, match(true())),
        movie,
        title1 = title,
        each(match(name == 'Rating')),
        each(patternMatch(`(y)`),
            $1 + 'regex' = $$)
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> Select1
```

## <a name="next-steps"></a>Nästa steg
* När du har använt Kolumnerna Välj för att byta namn på, ändra ordning och alias använder du [sink-omvandlingen](data-flow-sink.md) för att landa dina data i ett datalager.

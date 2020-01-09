---
title: Koppla omvandling i data flöde för mappning
description: Kombinera data från två data källor med hjälp av kopplings omvandlingen i Azure Data Factory mappa data flöde
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/02/2020
ms.openlocfilehash: 10149c6eb06e6d2994233aa365f237e6d9330c48
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644772"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Koppla omvandling i data flöde för mappning

Använd kopplings omvandlingen för att kombinera data från två källor eller strömmar i ett data flöde för mappning. Utdataströmmen tar med alla kolumner från båda källorna som matchas baserat på ett kopplings villkor. 

## <a name="join-types"></a>Kopplings typer

Mappning av data flöden stöder för närvarande fem olika kopplings typer.

### <a name="inner-join"></a>Inre koppling

Inre koppling matar bara ut rader som har matchande värden i båda tabellerna.

### <a name="left-outer"></a>Vänster yttre

Vänster yttre koppling returnerar alla rader från den vänstra strömmen och matchade poster från den högra data strömmen. Om en rad från den vänstra data strömmen inte har någon matchning, anges utdatakolumner från den högra data strömmen till NULL. Utdata blir de rader som returneras av en inre koppling och de omatchade raderna från den vänstra data strömmen.

> [!NOTE]
> Spark-motorn som används av data flöden kommer ibland att kunna använda kartesiska-produkter i dina kopplings villkor. I så fall kan du växla till en anpassad kors koppling och manuellt ange kopplings villkoret. Detta kan resultera i sämre prestanda i dina data flöden eftersom körnings motorn kan behöva beräkna alla rader från båda sidor i relationen och sedan filtrera rader.

### <a name="right-outer"></a>Höger yttre

Höger yttre koppling returnerar alla rader från den högra strömmen och matchade poster från den vänstra data strömmen. Om en rad från den högra data strömmen saknar matchning, anges utdatakolumner från den vänstra data strömmen till NULL. Utdata blir de rader som returneras av en inre koppling och de omatchade raderna från den högra strömmen.

### <a name="full-outer"></a>Fullständig yttre

Fullständig yttre koppling matar ut alla kolumner och rader från båda sidor med NULL-värden för kolumner som inte matchar.

### <a name="custom-cross-join"></a>Anpassad kors koppling

Kors koppling matar ut den kors produkten av de två strömmarna baserat på ett villkor. Om du använder ett villkor som inte är lika anger du ett anpassat uttryck som kors kopplings villkor. Utdataströmmen är alla rader som uppfyller kopplings villkoret.

Du kan använda den här anslutnings typen för icke-Equi kopplingar och ```OR``` villkor.

Om du vill skapa en fullständig kartesiska-produkt explicit använder du den härledda kolumn omvandlingen i var och en av de två oberoende strömmarna innan du börjar med att skapa en syntetisk nyckel att matcha på. Du kan till exempel skapa en ny kolumn i en härledd kolumn i varje data ström som heter ```SyntheticKey``` och ange den som lika med ```1```. Använd sedan ```a.SyntheticKey == b.SyntheticKey``` som ditt anpassade kopplings uttryck.

> [!NOTE]
> Se till att inkludera minst en kolumn från varje sida av din vänstra och högra relation i en anpassad kors koppling. Om du kör kors kopplingar med statiska värden i stället för kolumner från varje sida leder det till en fullständig genomsökning av hela data uppsättningen, vilket leder till att data flödet fungerar dåligt.

## <a name="configuration"></a>Konfiguration

1. Välj vilken data ström som du ansluter till med i list rutan till **höger ström** .
1. Välj **Anslutnings typ**
1. Välj vilka nyckel kolumner som du vill matcha på för dig som kopplings villkor. Som standard söker data flödet efter likhet mellan en kolumn i varje data ström. Om du vill jämföra via ett beräknat värde hovrar du över kolumn List rutan och väljer **beräknad kolumn**.

![Koppla omvandling](media/data-flow/join.png "Slå ihop")

## <a name="optimizing-join-performance"></a>Optimera anslutningens prestanda

Till skillnad från sammanfognings koppling i verktyg som SSIS är Join-omvandlingen inte en obligatorisk sammanfognings kopplings åtgärd. Kopplings nycklarna behöver inte sorteras. Join-åtgärden sker baserat på den optimala kopplings åtgärden i Spark, antingen broadcast eller koppling på kopplings sidan.

![Optimering av kopplings omvandling](media/data-flow/joinoptimize.png "Delta i optimering")

Om en eller båda data strömmarna passar in i arbetsnodens minne kan du optimera prestanda ytterligare genom att aktivera **sändning** på fliken optimera. Du kan också partitionera om dina data på kopplings åtgärden så att de passar bättre i minnet per arbets kraft.

## <a name="self-join"></a>Själv koppling

Om du själv vill ansluta en data ström med sig själv kan du ange ett alias för en befintlig ström med en SELECT-omvandling. Skapa en ny gren genom att klicka på plus ikonen bredvid en omvandling och välja **ny gren**. Lägg till en SELECT-omvandling för att ange alias för den ursprungliga strömmen. Lägg till en kopplings omvandling och välj den ursprungliga data strömmen som **vänster ström** och välj omvandlingen som **rätt data ström**.

![Själv koppling](media/data-flow/selfjoin.png "Själv koppling")

## <a name="testing-join-conditions"></a>Testa kopplings villkor

När du testar kopplings Transformationerna med data förhands granskning i fel söknings läge, använder du en liten uppsättning kända data. När du samplar rader från en stor data uppsättning kan du inte förutsäga vilka rader och nycklar som ska läsas för testning. Resultatet är icke-deterministiskt, vilket innebär att dina kopplings villkor inte kan returnera några träffar.

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>Exempel på inre koppling

Exemplet nedan är en JOIN-omvandling med namnet `JoinMatchedData` som tar vänster ström `TripData` och direkt uppspelnings `TripFare`.  Kopplings villkoret är det uttryck `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` som returnerar true om kolumnerna `hack_license`, `medallion`, `vendor_id`och `pickup_datetime` i varje data ström matchar. `joinType` är `'inner'`. Vi aktiverar sändning i endast den vänstra strömmen så `broadcast` har ett värde `'left'`.

I Data Factory UX ser den här omvandlingen ut som på bilden nedan:

![Exempel på koppling](media/data-flow/join-script1.png "Exempel på koppling")

Data flödes skriptet för den här omvandlingen är i kodfragmentet nedan:

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="custom-cross-join-example"></a>Exempel på anpassat kors koppling

Exemplet nedan är en JOIN-omvandling med namnet `JoiningColumns` som tar vänster ström `LeftStream` och direkt uppspelnings `RightStream`. Den här omvandlingen tar i två strömmar och kopplas ihop alla rader där kolumn `leftstreamcolumn` är större än kolumn `rightstreamcolumn`. `joinType` är `cross`. Sändning har inte Aktiver ATS `broadcast` har ett värde `'none'`.

I Data Factory UX ser den här omvandlingen ut som på bilden nedan:

![Exempel på koppling](media/data-flow/join-script2.png "Exempel på koppling")

Data flödes skriptet för den här omvandlingen är i kodfragmentet nedan:

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>Nästa steg

När du har anslutit till data skapar du en [härledd kolumn](data-flow-derived-column.md) och [sinkar](data-flow-sink.md) dina data till ett mål data lager.

---
title: Koppla omvandling i Azure Data Factory mappa data flöde | Microsoft Docs
description: Kombinera data från två data källor med hjälp av kopplings omvandlingen i Azure Data Factory mappa data flöde
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 78de9f2bedfc36add567053e1de47e8893bfaf3c
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597047"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Koppla omvandling i data flöde för mappning

Använd kopplings omvandlingen för att kombinera data från två källor eller strömmar i ett data flöde för mappning. Utdataströmmen tar med alla kolumner från båda källorna som matchas baserat på ett kopplings villkor. 

## <a name="join-types"></a>Kopplings typer

Mappning av data flöden stöder för närvarande fem olika kopplings typer.

### <a name="inner-join"></a>Inre koppling

Inre koppling matar bara ut rader som har matchande värden båda tabellerna.

### <a name="left-outer"></a>Vänster yttre

Vänster yttre koppling returnerar alla rader från den vänstra strömmen och matchade poster från den högra data strömmen. Om en rad från den vänstra data strömmen inte har någon matchning, anges utdatakolumner från den högra data strömmen till NULL. Utdata blir de rader som returneras av en inre koppling och de omatchade raderna från den vänstra data strömmen.

### <a name="right-outer"></a>Höger yttre

Vänster yttre koppling returnerar alla rader från den högra strömmen och matchade poster från den vänstra data strömmen. Om en rad från den högra data strömmen saknar matchning, anges utdatakolumner från den högra data strömmen till NULL. Utdata blir de rader som returneras av en inre koppling och de omatchade raderna från den högra strömmen.

### <a name="full-outer"></a>Fullständig yttre

Fullständig yttre koppling visar alla kolumner och rader från båda sidor med NULL-värden för kolumner som inte matchar.

### <a name="cross-join"></a>Kors koppling

Kors koppling matar ut den kors produkten av de två strömmarna baserat på ett villkor. Om du använder ett villkor som inte är lika anger du ett anpassat uttryck som kors kopplings villkor. Utdataströmmen är alla rader som uppfyller kopplings villkoret. Om du vill skapa en kartesiska-produkt som matar ut varje rad kombination anger du `true()` som kopplings villkor.

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

## <a name="data-flow-script"></a>Skript för data flöde

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

Exemplet nedan är en JOIN-omvandling med namnet `JoinMatchedData` som tar vänster ström `TripData` och direkt uppspelnings `TripFare`.  Kopplings villkoret är det uttryck `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` som returnerar true om kolumnerna `hack_license`, `medallion`, `vendor_id` och `pickup_datetime` i varje data ström matchar. @No__t_0 är `'inner'`. Vi aktiverar sändning i endast den vänstra strömmen så `broadcast` har ett värde `'left'`.

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

### <a name="cross-join-example"></a>Exempel på kors koppling

Exemplet nedan är en JOIN-omvandling med namnet `CartesianProduct` som tar vänster ström `TripData` och direkt uppspelnings `TripFare`. Den här omvandlingen tar i två strömmar och returnerar en kartesiska-produkt av sina rader. Kopplings villkoret är `true()` eftersom en fullständig kartesiska-produkt matas ut. @No__t_0 i `cross`. Vi aktiverar sändning i endast den vänstra strömmen så `broadcast` har ett värde `'left'`.

I Data Factory UX ser den här omvandlingen ut som på bilden nedan:

![Exempel på koppling](media/data-flow/join-script2.png "Exempel på koppling")

Data flödes skriptet för den här omvandlingen är i kodfragmentet nedan:

```
TripData, TripFare
    join(
        true(),
        joinType:'cross',
        broadcast: 'left'
    )~> CartesianProduct
```

## <a name="next-steps"></a>Nästa steg

När du har anslutit till data skapar du en [härledd kolumn](data-flow-derived-column.md) och [sinkar](data-flow-sink.md) dina data till ett mål data lager.

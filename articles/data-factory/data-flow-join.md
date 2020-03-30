---
title: Gå med i omvandlingen i mappning av dataflöde
description: Kombinera data från två datakällor med hjälp av kopplingsomvandlingen i Azure Data Factory-mappningsdataflödet
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/02/2020
ms.openlocfilehash: 32100e9cad86f12dc8111ee8a0282a515540a4db
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346612"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Gå med i omvandlingen i mappning av dataflöde

Använd kopplingsomvandlingen för att kombinera data från två källor eller strömmar i ett mappningsdataflöde. Utdataströmmen innehåller alla kolumner från båda källorna som matchas baserat på ett kopplingsvillkor. 

## <a name="join-types"></a>Kopplingstyper

Mappning av dataflöden stöder för närvarande fem olika kopplingstyper.

### <a name="inner-join"></a>Inre join

Inre koppling endast utdata rader som har matchande värden i båda tabellerna.

### <a name="left-outer"></a>Vänster yttre

Vänster yttre koppling returnerar alla rader från den vänstra strömmen och matchade poster från den högra strömmen. Om en rad från den vänstra strömmen inte har någon matchning ställs utdatakolumnerna från den högra strömmen till NULL. Utdata blir de rader som returneras av en inre koppling plus de omatchade raderna från den vänstra strömmen.

> [!NOTE]
> Spark-motorn som används av dataflöden kommer ibland att misslyckas på grund av möjliga cartesianska produkter i dina kopplingsförhållanden. Om detta inträffar kan du växla till en anpassad korskoppling och manuellt ange ditt kopplingsvillkor. Detta kan resultera i långsammare prestanda i dina dataflöden eftersom körningsmotorn kan behöva beräkna alla rader från båda sidor av relationen och sedan filtrera rader.

### <a name="right-outer"></a>Höger yttre

Höger yttre koppling returnerar alla rader från den högra strömmen och matchade poster från den vänstra strömmen. Om en rad från den högra strömmen inte har någon matchning ställs utdatakolumnerna från den vänstra strömmen till NULL. Utdata blir de rader som returneras av en inre koppling plus de omatchade raderna från den högra strömmen.

### <a name="full-outer"></a>Fullständig yttre

Fullständig yttre koppling matar ut alla kolumner och rader från båda sidor med NULL-värden för kolumner som inte matchas.

### <a name="custom-cross-join"></a>Anpassad korskoppling

Korskoppling utgångar korsprodukten av de två strömmarna baserat på ett villkor. Om du använder ett villkor som inte är lika, anger du ett anpassat uttryck som villkor för korskoppling. Utdataströmmen kommer att vara alla rader som uppfyller kopplingsvillkoret.

Du kan använda den här kopplingstypen för kopplingar och ```OR``` villkor som inte är likvärdiga.

Om du uttryckligen vill producera en fullständig kartesisk produkt använder du omvandlingen härledd kolumn i var och en av de två oberoende strömmarna innan kopplingen skapa en syntetisk nyckel som ska matchas på. Skapa till exempel en ny kolumn i Härledd kolumn i varje flöde som anropas ```SyntheticKey``` och ange den lika med ```1```. Använd ```a.SyntheticKey == b.SyntheticKey``` sedan som ditt anpassade kopplingsuttryck.

> [!NOTE]
> Se till att inkludera minst en kolumn från varje sida av vänster och höger relation i en anpassad korskoppling. Om du kör korskopplingar med statiska värden i stället för kolumner från varje sida resulterar det i fullständiga genomsökningar av hela datauppsättningen, vilket gör att dataflödet fungerar dåligt.

## <a name="configuration"></a>Konfiguration

1. Välj vilken dataström du ansluter till i listrutan **Högerström.**
1. Välj din **kopplingstyp**
1. Välj vilka nyckelkolumner du vill matcha på för att du ska gå med i villkoret. Som standard söker dataflödet efter likhet mellan en kolumn i varje flöde. Om du vill jämföra via ett beräknat värde hovrar du över kolumnrullgardermenyn och väljer **Beräknad kolumn**.

![Gå med i transformation](media/data-flow/join.png "Slå ihop")

## <a name="optimizing-join-performance"></a>Optimera anslutningsprestanda

Till skillnad från kopplingskoppling i verktyg som SSIS är kopplingens omvandling inte en obligatorisk kopplingsåtgärd. Kopplingsnycklarna kräver inte sortering. Kopplingsåtgärden sker baserat på den optimala kopplingsåtgärden i Spark, antingen broadcast eller map-side join.

![Optimera för omvandlingstimering](media/data-flow/joinoptimize.png "Gå med i optimering")

Om en eller båda dataströmmarna passar in i arbetsnodens minne optimerar du prestanda ytterligare genom att aktivera **Broadcast** på fliken Optimera. Du kan också partitionera om dina data om kopplingsåtgärden så att de passar bättre in i minnet per anställd.

## <a name="self-join"></a>Gå med själv

Om du vill ansluta en dataström med sig själv kan du aliasa en befintlig ström med en utvald omvandling. Skapa en ny gren genom att klicka på plusikonen bredvid en omvandling och välja **Ny gren**. Lägg till en välj omformning i alias den ursprungliga strömmen. Lägg till en kopplingsförvandling och välj den ursprungliga strömmen som **vänsterström** och välja omvandling som **högerström**.

![Gå med själv](media/data-flow/selfjoin.png "Gå med själv")

## <a name="testing-join-conditions"></a>Testa anslutningsvillkor

När du testar kopplingsomvandlingarna med förhandsgranskning av data i felsökningsläge använder du en liten uppsättning kända data. När du provar rader från en stor datauppsättning kan du inte förutsäga vilka rader och nycklar som ska läsas för testning. Resultatet är icke-deterministiskt, vilket innebär att dina kopplingsvillkor kanske inte returnerar några matchningar.

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

Exemplet nedan är en `JoinMatchedData` kopplingsförvandling som tar vänster ström `TripData` och höger ström `TripFare`.  Kopplingsvillkoret är `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` det uttryck `hack_license`som `medallion` `vendor_id`returnerar `pickup_datetime` sant om kolumnerna , , och i varje ström matchar. Den `joinType` `'inner'`är . Vi aktiverar sändningar i endast den `broadcast` vänstra `'left'`strömmen så har värde .

I Data Factory UX ser den här omvandlingen ut som bilden nedan:

![Exempel på koppling](media/data-flow/join-script1.png "Exempel på koppling")

Dataflödesskriptet för den här omvandlingen finns i kodavsnittet nedan:

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

### <a name="custom-cross-join-example"></a>Exempel på anpassad korskoppling

Exemplet nedan är en `JoiningColumns` kopplingsförvandling som tar vänster ström `LeftStream` och höger ström `RightStream`. Den här omvandlingen sker i två strömmar `leftstreamcolumn` och sammanfogar alla rader där kolumnen är större än kolumnen `rightstreamcolumn`. Den `joinType` `cross`är . Sändning är inte `broadcast` aktiverat `'none'`har värde .

I Data Factory UX ser den här omvandlingen ut som bilden nedan:

![Exempel på koppling](media/data-flow/join-script2.png "Exempel på koppling")

Dataflödesskriptet för den här omvandlingen finns i kodavsnittet nedan:

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>Nästa steg

När du har sammanfogat data skapar du en [härledd kolumn](data-flow-derived-column.md) och [sänkr](data-flow-sink.md) dina data till ett måldatalager.

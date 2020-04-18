---
title: Schemadrift i mappning av dataflöde
description: Skapa elastiska dataflöden i Azure Data Factory med Schema Drift
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 6e361d23860ce8f40abba5c246242cf345bb974c
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606103"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Schemadrift i mappning av dataflöde

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Schema drift är fallet där dina källor ofta ändrar metadata. Fält, kolumner och typer kan läggas till, tas bort eller ändras i farten. Utan hantering för schemadrift blir dataflödet sårbart för ändringar i uppströmsdatakällan. Typiska ETL-mönster misslyckas när inkommande kolumner och fält ändras eftersom de tenderar att vara knutna till dessa källnamn.

För att skydda mot schemadrift är det viktigt att ha anläggningarna i ett dataflödesverktyg så att du som datatekniker kan:

* Definiera källor som har föränderliga fältnamn, datatyper, värden och storlekar
* Definiera omvandlingsparametrar som kan arbeta med datamönster i stället för hårdkodade fält och värden
* Definiera uttryck som förstår mönster som matchar inkommande fält i stället för att använda namngivna fält

Azure Data Factory stöder inbyggt flexibelt scheman som ändras från körning till körning så att du kan skapa allmän dataomvandlingslogik utan att du behöver kompilera om dina dataflöden.

Du måste fatta ett arkitektoniskt beslut i ditt dataflöde för att acceptera schemadrift i hela flödet. När du gör detta kan du skydda mot schemaändringar från källorna. Du förlorar dock tidig bindning av kolumner och typer i hela dataflödet. Azure Data Factory behandlar schemadriftflöden som sena bindningsflöden, så när du skapar dina omvandlingar är de drifted kolumnnamnen inte tillgängliga för dig i schemavyerna i hela flödet.

Den här videon ger en introduktion till några av de komplexa lösningar som du enkelt kan skapa i ADF med dataflödets schemadriftfunktion. I det här exemplet skapar vi återanvändbara mönster baserat på flexibla databasscheman:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tyx7]

## <a name="schema-drift-in-source"></a>Schema drift i källa

Kolumner som kommer in i dataflödet från källdefinitionen definieras som "drifted" när de inte finns i källprojektionen. Du kan visa källprojektionen från projektionsfliken i källomformningen. När du väljer en datauppsättning för källan tar ADF automatiskt schemat från datauppsättningen och skapar ett projekt från den schemadefinitionen för datauppsättningen.

I en källomvandling definieras schemaavdrift som läskolumner som inte har definierats i datauppsättningsschemat. Om du vill aktivera schemadrift kontrollerar du **Tillåt schemaavdrift** i källomvandlingen.

![Bortkälla för schemaavdrift](media/data-flow/schemadrift001.png "Bortkälla för schemaavdrift")

När schemadrift är aktiverat läs avser alla inkommande fält från källan under körningen och skickas genom hela flödet till sink. Som standard anländer alla nyligen identifierade kolumner, så kallade *drifterade kolumner,* som en strängdatatyp. Om du vill att dataflödet automatiskt ska sluta sig till datatyper av borttaviga kolumner kontrollerar du **infer-borttjänad kolumntyper** i källinställningarna.

## <a name="schema-drift-in-sink"></a>Schema drift i diskbänken

I en sink-omformning är schemadrift när du skriver ytterligare kolumner ovanpå vad som definieras i sink-dataschemat. Om du vill aktivera schemadrift kontrollerar du **Tillåt schemaavdrift** i diskhonomformningen.

![Schema drift sink](media/data-flow/schemadrift002.png "Schema drift sink")

Om schemaavdrift är aktiverat kontrollerar du att skjutreglaget **Automatisk mappning** på fliken Mappning är aktiverat. Med det här skjutreglaget aktiverat skrivs alla inkommande kolumner till din destination. Annars måste du använda regelbaserad mappning för att skriva borttrevda kolumner.

![Diskänka automatisk mappning](media/data-flow/automap.png "Diskänka automatisk mappning")

## <a name="transforming-drifted-columns"></a>Omvandla drivna kolumner

När dataflödet har drivit kolumner kan du komma åt dem i dina omvandlingar med följande metoder:

* Använd `byPosition` uttrycken och `byName` för att uttryckligen referera till en kolumn efter namn eller befattningsnummer.
* Lägga till ett kolumnmönster i en härledd kolumn eller aggregerad omvandling som matchar en kombination av namn, ström, position eller typ
* Lägga till regelbaserad mappning i en Select- eller Sink-omformning för att matcha bortförda kolumner med kolumneralias via ett mönster

Mer information om hur du implementerar kolumnmönster finns [i Kolumnmönster i mappning av dataflöde](concepts-data-flow-column-pattern.md).

### <a name="map-drifted-columns-quick-action"></a>Snabb åtgärd för kartdrivna kolumner

Om du uttryckligen vill referera till borttappade kolumner kan du snabbt generera mappningar för dessa kolumner via en snabbåtgärd för förhandsversionen av data. När [felsökningsläget](concepts-data-flow-debug-mode.md) är aktiverat går du till fliken Förhandsgranskning av data och klickar på **Uppdatera** för att hämta en förhandsgranskning av data. Om det finns en datafabriksidentifiering som det finns bortrepelter kan du klicka på **Karta drifted** och generera en härledd kolumn som gör att du kan referera till alla bortfallna kolumner i schemavyer nedströms.

![Karta drev](media/data-flow/mapdrifted1.png "Karta drev")

I den genererade derived kolumnomvandlingen mappas varje bortfallen kolumn till dess identifierade namn och datatyp. I ovanstående förhandsgranskning av data identifieras kolumnen "movieId" som ett heltal. När Du har klickat på **Map Drifted** definieras `toInteger(byName('movieId'))` movieId i den härledda kolumnen som och inkluderas i schemavyer i underordnade omvandlingar.

![Karta drev](media/data-flow/mapdrifted2.png "Karta drev")

## <a name="next-steps"></a>Nästa steg
I [dataflödesuttrycksspråket](data-flow-expression-functions.md)hittar du ytterligare resurser för kolumnmönster och schemadrift, inklusive "byName" och "byPosition".

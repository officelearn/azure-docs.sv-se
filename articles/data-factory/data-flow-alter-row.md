---
title: Ändra radomvandling i mappning av dataflöde
description: Så här uppdaterar du databasmålet med hjälp av ändringsradsomvandlingen i mappningsdataflödet
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/20/2020
ms.openlocfilehash: 6b353967c9b9c7517f1a42581717c6394c0e6374
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729145"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>Ändra radomvandling i mappning av dataflöde

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Använd omvandlingen Ändra rad för att ange principer för infogning, borttagning, uppdatering och upsert på rader. Du kan lägga till 1:1:00 villkor som uttryck. Dessa villkor bör anges i prioritetsordning, eftersom varje rad markeras med den princip som motsvarar det första matchande uttrycket. Vart och ett av dessa villkor kan resultera i att en rad (eller rader) infogas, uppdateras, tas bort eller upserted. Alter Row kan producera både DDL-& DML-åtgärder mot databasen.

![Ändra radinställningar](media/data-flow/alter-row1.png "Ändra radinställningar")

Alter Row transformationer fungerar bara på databas eller CosmosDB sänkor i ditt dataflöde. De åtgärder som du tilldelar rader (infoga, uppdatera, ta bort, upsert) inträffar inte under felsökningssessioner. Kör en körningsdataflödesaktivitet i en pipeline för att anta ändringsradsprinciperna i databastabellerna.

## <a name="specify-a-default-row-policy"></a>Ange en standardradprincip

Skapa en Alter Row-omformning och `true()`ange en radprincip med villkoret . Varje rad som inte matchar något av de tidigare definierade uttrycken markeras för den angivna radprincipen. Som standard markeras varje rad som inte matchar `Insert`något villkorsuttryck för .

![Ändra radprincip](media/data-flow/alter-row4.png "Ändra radprincip")

> [!NOTE]
> Om du vill markera alla rader med en princip kan du `true()`skapa ett villkor för den principen och ange villkoret som .

## <a name="view-policies-in-data-preview"></a>Visa principer i förhandsgranskning av data

Använd [felsökningsläge](concepts-data-flow-debug-mode.md) för att visa resultatet av dina ändringsradsprinciper i förhandsgranskningsfönstret för data. En dataförhandsvisning av en ändringsradsomvandling ger inte DDL- eller DML-åtgärder mot ditt mål.

![Ändra radprinciper](media/data-flow/alter-row3.png "Ändra radprinciper")

Varje ändringsradsprincip representeras av en ikon som anger om en infoga, uppdatera, upsert eller borttagen åtgärd ska utföras. Det övre huvudet visar hur många rader som påverkas av varje princip i förhandsgranskningen.

## <a name="allow-alter-row-policies-in-sink"></a>Tillåt ändring av radprinciper i diskhon

För att ändringsradsprinciperna ska fungera måste dataströmmen skriva till en databas eller Cosmos-diskho. Aktivera vilka ändringsradsprinciper som tillåts för diskbänken på fliken **Inställningar** i diskhon.

![Ändra radfländning](media/data-flow/alter-row2.png "Ändra radfländning")

Standardbeteendet är att endast tillåta infogningar. Om du vill tillåta uppdateringar, upserts eller borttagningar markerar du rutan i diskhon som motsvarar det villkoret. Om uppdateringar, upserts eller borttagningar är aktiverade måste du ange vilka nyckelkolumner i diskhon som ska matchas.

> [!NOTE]
> Om dina infogningar, uppdateringar eller upserts ändrar schemat för måltabellen i diskhon, misslyckas dataflödet. Om du vill ändra målschemat i databasen väljer du **Återskapa tabell** som tabellåtgärd. Detta kommer att släppa och återskapa tabellen med den nya schemadefinitionen.

Sink-omvandlingen kräver antingen en enda nyckel eller en serie nycklar för unik radidentifiering i måldatabasen. För SQL-diskhoar anger du tangenterna på fliken sink-inställningar. För CosmosDB ställer du in partitionsnyckeln i inställningarna och ställer även in CosmosDB-systemfältet "id" i diskhonmappningen. För CosmosDB är det obligatoriskt att inkludera systemkolumnen "id" för uppdateringar, upserts och deletes.

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>Exempel

Exemplet nedan är en ändringsradsomvandling som tar `CleanData` en inkommande ström `SpecifyUpsertConditions` och skapar tre ändra radvillkor. I föregående omformning `alterRowCondition` beräknas en kolumn med namnet som avgör om en rad infogas, uppdateras eller tas bort i databasen. Om värdet för kolumnen har ett strängvärde som matchar ändringsradsregeln tilldelas den principen.

I Data Factory UX ser den här omvandlingen ut som bilden nedan:

![Ändra radexempel](media/data-flow/alter-row4.png "Ändra radexempel")

Dataflödesskriptet för den här omvandlingen finns i kodavsnittet nedan:

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>Nästa steg

Efter omvandlingen Ändra rad kanske du vill [sänka data till ett måldatalager](data-flow-sink.md).

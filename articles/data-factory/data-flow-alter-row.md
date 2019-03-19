---
title: Azure Data Factory mappning Dataomvandling Flow Alter rad
description: Så här uppdaterar du databasen mål med hjälp av Azure Data Factory mappning Flow Alter rad Dataomvandling
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: d842898ca700490ae99b46140be6609622a144df
ms.sourcegitcommit: 5f41e855d415cfa741d8f710792ea486480df5cf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58133169"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Azure Data Factory Alter rad-transformering

Använd Alter rad-transformering för att ange insert-, delete-, update- och upsert principer för rader. Du kan lägga till en-till-många-villkor som uttryck. Var och en av dessa villkor kan resultera i en rad (eller rader) som infogas, uppdaterad, borttagen eller upsert. ALTER rad kan producera både DDL & DML-åtgärder mot din databas.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Ändra inställningar för rad](media/data-flow/alter-row1.png "Alter rad inställningar")

> [!NOTE]
> ALTER rad transformationer ska endast användas i databasen mottagare i ditt dataflöde. De åtgärder som du tilldelar rader (Infoga, uppdatera, ta bort, upsert) utförs inte under debug-sessioner. Du måste lägga till en aktivitet som kör dataflöde i en pipeline och använder pipeline felsökningen eller utlösare för att införa alter rad principerna på databastabeller.

## <a name="view-policies"></a>Visa principer

Växla den Data flöda felsökningsläge till på och visa resultatet av dina principer för alter-raden i förhandsgranskningsfönstret för Data. Kör en alter-rad i Data flöda felsökningsläge kommer inte att generera DDL eller DML-åtgärder mot dina mål. För att få dessa åtgärder inträffar, köra dataflödet i en köra dataflödet aktivitet i en pipeline.

![Ändra raden principer](media/data-flow/alter-row3.png "Alter rad principer")

Detta kommer att du kan kontrollera och visa status för varje rad baserat på dina villkor. Det finns ikonen visar för varje infoga, uppdatera och ta bort upsert åtgärd som ska utföras i ditt dataflöde, som anger vilken åtgärd som sker när du kör dataflödet i en pipeline.

## <a name="sink-settings"></a>Inställningar för mottagare

Du måste ha en databas som mottagare typ för Alter raden ska fungera. Du måste ange varje åtgärd som ska tillåtas i kanalmottagare inställningar.

![Ändra raden mottagare](media/data-flow/alter-row2.png "Alter rad mottagare")

Standardbeteendet i ADF dataflöde med databasen mottagare är att infoga rader. Om du vill tillåta uppdateringar, upsertar och borttagningar samt måste du också kontrollera dessa rutor i mottagare så att åtgärderna.

> [!NOTE]
> Om din infogningar, uppdateringar eller upsertar ändra schemat för måltabellen i mottagaren kan inte ditt dataflöde. För att kunna ändra målschemat i din databas, måste du välja alternativet ”Skapa tabellen” i mottagaren. Detta kommer att ta bort och återskapa din tabell med nya schemadefinitionen.

## <a name="next-steps"></a>Nästa steg

När du har Alter rad-transformeringen kan du [mottagare dina data till ett måldatalager](data-flow-sink.md).

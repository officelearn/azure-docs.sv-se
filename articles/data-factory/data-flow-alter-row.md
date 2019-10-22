---
title: Azure Data Factory mappa data flödets Alter Row-omvandling
description: Så här uppdaterar du databas målet med Azure Data Factory mappa data flöde Alter Row Transformation
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: fff08b3e046161fbedefdc55f4e6a39a7f965f80
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72387274"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Azure Data Factory Alter Row-transformering

Använd transformeringen Alter Row för att ange INSERT-, DELETE-, Update-och upsert-principer på rader. Du kan lägga till ett-till-många-villkor som uttryck. Dessa villkor måste anges i prioritetsordning, eftersom varje rad markeras med principen som motsvarar det första matchnings uttrycket. Vart och ett av dessa villkor kan resultera i att en rad (eller rader) infogas, uppdateras, tas bort eller upserted. Alter Row kan producera både DDL-& DML-åtgärder mot databasen.



![Ändra rad inställningar](media/data-flow/alter-row1.png "Ändra rad inställningar")

> [!NOTE]
> Alter Row-transformeringar fungerar bara på databas handfat i ditt data flöde. De åtgärder som du tilldelar till rader (Insert, Update, DELETE, upsert) sker inte under debug-sessioner. Du måste lägga till en åtgärd för att köra data flöde i en pipeline och använda fel sökning eller utlösare för pipeline för att införa principer för att ändra rad i dina databas tabeller.

## <a name="indicate-a-default-row-policy"></a>Ange en standard rads princip

Skapa en Alter Row-omvandling och ange en rad princip med villkoret `true()`. Varje rad som inte uppfyller något av de tidigare definierade uttrycken kommer att markeras för den angivna rad principen. Som standard markeras varje rad som inte uppfyller ett villkors uttryck för `Insert`.

![Ändra rad en princip](media/data-flow/alter-row4.png "Ändra rad en princip")

> [!NOTE]
> Om du vill markera alla rader med en princip kan du skapa ett villkor för principen och ange villkoret som `true()`.

## <a name="view-policies"></a>Visa principer

Aktivera fel söknings läge för data flöde om du vill visa resultatet av dina Alter Row-principer i data förhands gransknings fönstret. Att köra en Alter-rad i fel söknings läge för data flöde genererar inte DDL-eller DML-åtgärder mot målet. För att kunna utföra dessa åtgärder kan du köra data flödet inuti en aktivitet för att köra data flöde i en pipeline.

![Ändra rad principer](media/data-flow/alter-row3.png "Ändra rad principer")

På så sätt kan du verifiera och visa statusen för varje rad baserat på dina villkor. Det finns en ikon för varje INSERT-, Update-, Delete-och upsert-åtgärd som kommer att utföras i ditt data flöde och som anger vilken åtgärd som ska utföras när du kör data flödet inuti en pipeline.

## <a name="sink-settings"></a>Mottagar inställningar

Du måste ha en databas mottagar typ för att Alter Row ska fungera. I mottagar inställningarna bör du ange varje åtgärd som motsvarar dina villkor för Alter-rader som ska tillåtas.

![Ändra rad mottagare](media/data-flow/alter-row2.png "Ändra rad mottagare")

Standard beteendet i ADF-dataflödet med databas handfat är att infoga rader. Om du vill tillåta uppdateringar, upsertar och borttagningar måste du också markera dessa rutor i sinken för att tillåta åtgärderna.

> [!NOTE]
> Om dina infogningar, uppdateringar eller upsertar ändrar schemat för mål tabellen i sinken kommer ditt data flöde inte att fungera. Du måste välja alternativet "återskapa tabell" i mottagaren för att ändra mål schema i databasen. Detta tar bort och återskapar din tabell med den nya schema definitionen.

## <a name="next-steps"></a>Nästa steg

Efter omvandlingen av Alter Row kanske du vill lägga till [data i ett mål data lager](data-flow-sink.md).

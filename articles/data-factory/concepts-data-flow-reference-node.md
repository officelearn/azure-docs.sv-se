---
title: Azure Data Factory mappar referens nod för data flöde
description: Data Factory data flöde lägger till en reference-nod för kopplingar, uppslag, unioner
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 4ed17114cc0ce586c68c5b3e087acffdb82ea96c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030488"
---
# <a name="mapping-data-flow-reference-node"></a>Mappar referens nod för data flöde



Referens(media/data-flow/referencenode.png "nod") för ![referens nod]

En reference-nod läggs automatiskt till på arbets ytan för att tyda på att noden den är kopplad till refererar till en annan befintlig nod på arbets ytan. Tänk på en reference-nod som en pekare eller en referens till en annan data flödes omvandling.

Exempel: När du ansluter eller binder fler än en data ström kan data flödets arbets yta lägga till en referens nod som visar namn och inställningar för den icke-primära inkommande data strömmen.

Det går inte att flytta eller ta bort referens noden. Du kan dock Klicka på noden för att ändra den ursprungliga omvandlings inställningen.

De GRÄNSSNITTs regler som styr när data flödet lägger till referens-noden baseras på tillgängligt utrymme och lodrätt avstånd mellan rader.

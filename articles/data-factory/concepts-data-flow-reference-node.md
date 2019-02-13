---
title: Azure Data Factory mappning Datanoden Flow-referens
description: Dataflöde för data Factory läggs en referens-nod för kopplingar, sökningar, unioner
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 641375c2b848957ffc0f5ad092a28460d91b6690
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213567"
---
# <a name="azure-data-factory-mapping-data-flow-reference-node"></a>Azure Data Factory mappning Datanoden Flow-referens

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Referera till noden](media/data-flow/referencenode.png "referens nod")

En referens-nod läggs automatiskt till arbetsytan för en obestämd att noden som den är ansluten till refererar till en annan befintlig nod på arbetsytan. Tänk på en referens-nod som en pekare eller en referens till en annan transformering för flödet av data.

Exempel: När du ansluter eller Union mer än en dataström, dataflöde arbetsytan kan lägga till en referens-nod som visar namnet och inställningarna för icke-primär inkommande dataström.

Referensnoden kan inte flyttas eller tas bort. Du kan dock klicka i noden för att ändra inställningarna för ursprungliga omvandling.

UI-reglerna som styr när Referensnoden läggs dataflöde baseras på tillgängligt utrymme och lodrätt avstånd mellan rader.

---
title: Stöd för Render-hanterare – Azure Batch
description: Använda Integrering av Azure Batch render manager. Läs mer om inbyggt stöd eller tillägg för populära renderingshanterare.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 246907b16534d1a91833cab633a1973c97429f47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75449688"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Använda Azure Batch med renderingsbeslämda hanterare

Om du använder en befintlig lokal återgegrupp är det högst troligt att en renderingshanterare styr renderingsgruppskapaciteten och återge jobb.

Azure tillhandahåller antingen inbyggt stöd eller tillägg för populära renderingshanterare. Du kan sedan lägga till och ta bort virtuella Azure-datorer, inklusive virtuella datorer med användningsbaserad programlicensiering och virtuella datorer med låg prioritet.

Följande renderingshanterare stöds:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Kunglig återge](https://www.royalrender.de/)
* [Datum för Thinkbox](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Hubb för Azure Render

Azure Render Hub förenklar skapandet och hanteringen av Azure-renderingsgrupper.  Render Hub har inbyggt stöd för PipelineFx Qube och Deadline 10.  Mer information och detaljerade instruktioner finns [i GitHub-databasen](https://github.com/Azure/azure-render-hub).

## <a name="using-azure-with-pipelinefx-qube"></a>Använda Azure med PipelineFX Qube

Azure Render Hub stöder populära renderingshanterare, inklusive Deadline.  Instruktioner om hur du distribuerar och använder Render Hub finns [i GitHub-databasen](https://github.com/Azure/azure-render-hub).

Skript och instruktioner för att aktivera virtuella azure batch-pool-datorer som ska användas som Qube-arbetare är också tillgängliga i [GitHub-databasen](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Använda Azure med Royal Render

Royal Render har azure- och Azure Batch-integrering inbyggd, så att du kan utöka en renderingsgrupp med Azure-baserade virtuella datorer. En sammanfattning finns [i hjälpfilerna](https://www.royalrender.de/help8/index.html?Cloudrendering.html).

Ett exempel på en Royal Render-kund som använder Azure-integreringen finns i [kundberättelsen Jellyfish Pictures](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Använda Azure med deadline för Thinkbox

Azure Render Hub stöder populära renderingshanterare, inklusive Deadline.  Instruktioner om hur du distribuerar och använder Render Hub finns [i GitHub-databasen](https://github.com/Azure/azure-render-hub).

## <a name="next-steps"></a>Nästa steg

Prova Azure Batch-integreringen för renderingshanteraren med hjälp av rätt plugin-program och instruktioner på GitHub, där så är tillämpligt.

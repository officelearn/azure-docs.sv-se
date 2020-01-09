---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/17/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 74ef8270b5efcd3b7cdf756c103dcc2e1c935508
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460492"
---
Innan du tilldelar en RBAC-roll till ett säkerhets objekt bör du bestämma omfattningen av åtkomsten som säkerhets objekt ska ha. Bästa praxis är att bestämma att det alltid är bäst att bara bevilja det begränsande möjliga omfånget.

I följande lista beskrivs de nivåer där du kan begränsa åtkomsten till Azure blob-och Queue-resurser, från och med det snävaste omfånget:

- **En enskild behållare.** I det här omfånget gäller en roll tilldelning för alla blobar i behållaren, samt behållar egenskaper och metadata.
- **En enskild kö.** I det här omfånget gäller en roll tilldelning för meddelanden i kön, samt köegenskaper och metadata.
- **Lagrings kontot.** I det här omfånget gäller en roll tilldelning för alla behållare och deras blobbar, eller till alla köer och deras meddelanden.
- **Resurs gruppen.** I det här omfånget gäller en roll tilldelning för alla behållare eller köer i alla lagrings konton i resurs gruppen.
- **Prenumerationen.** I det här omfånget gäller en roll tilldelning för alla behållare eller köer i alla lagrings konton i alla resurs grupper i prenumerationen.

> [!IMPORTANT]
> Om din prenumeration innehåller ett namn område för ett Azure-DataBricks beviljas inte åtkomst till blob-och Queue-data för roller som är begränsade till prenumerationen. Omfattnings roller för resurs gruppen, lagrings kontot eller behållaren eller kön i stället.     

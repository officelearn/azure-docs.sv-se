---
title: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/16/2020
ms.author: tamram
ms.openlocfilehash: c222869df561a9a36ebd69eb9ae09fa688ba0086
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518696"
---
Innan du tilldelar en RBAC-roll till ett säkerhets objekt bör du bestämma omfattningen av åtkomsten som säkerhets objekt ska ha. Bästa praxis är att bestämma att det alltid är bäst att bara bevilja det begränsande möjliga omfånget.

I följande lista beskrivs de nivåer där du kan begränsa åtkomsten till Azure blob-och Queue-resurser, från och med det snävaste omfånget:

- **En enskild behållare.** I det här omfånget gäller en roll tilldelning för alla blobar i behållaren, samt behållar egenskaper och metadata.
- **En enskild kö.** I det här omfånget gäller en roll tilldelning för meddelanden i kön, samt köegenskaper och metadata.
- **Lagrings kontot.** I det här omfånget gäller en roll tilldelning för alla behållare och deras blobbar, eller till alla köer och deras meddelanden.
- **Resursgruppen.** I det här omfånget gäller en roll tilldelning för alla behållare eller köer i alla lagrings konton i resurs gruppen.
- **Prenumerationen.** I det här omfånget gäller en roll tilldelning för alla behållare eller köer i alla lagrings konton i alla resurs grupper i prenumerationen.
- **En hanterings grupp.** I det här omfånget gäller en roll tilldelning för alla behållare eller köer i alla lagrings konton i alla resurs grupper i alla prenumerationer i hanterings gruppen.

Mer information om RBAC-roll tilldelningar och omfång finns i [Vad är Azure-rollbaserad åtkomst kontroll (Azure RBAC)?](../articles/role-based-access-control/overview.md).

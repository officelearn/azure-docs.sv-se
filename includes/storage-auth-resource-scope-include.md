---
title: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/16/2020
ms.author: tamram
ms.openlocfilehash: 96d8100f2cffcfb001a693575128ce19e742225d
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534148"
---
Innan du tilldelar en Azure-roll till ett säkerhets objekt bör du bestämma omfattningen av åtkomsten som säkerhets objekt ska ha. Bästa praxis är att bestämma att det alltid är bäst att bara bevilja det begränsande möjliga omfånget.

I följande lista beskrivs de nivåer där du kan begränsa åtkomsten till Azure blob-och Queue-resurser, från och med det snävaste omfånget:

- **En enskild behållare.** I det här omfånget gäller en roll tilldelning för alla blobar i behållaren, samt behållar egenskaper och metadata.
- **En enskild kö.** I det här omfånget gäller en roll tilldelning för meddelanden i kön, samt köegenskaper och metadata.
- **Lagrings kontot.** I det här omfånget gäller en roll tilldelning för alla behållare och deras blobbar, eller till alla köer och deras meddelanden.
- **Resursgruppen.** I det här omfånget gäller en roll tilldelning för alla behållare eller köer i alla lagrings konton i resurs gruppen.
- **Prenumerationen.** I det här omfånget gäller en roll tilldelning för alla behållare eller köer i alla lagrings konton i alla resurs grupper i prenumerationen.
- **En hanterings grupp.** I det här omfånget gäller en roll tilldelning för alla behållare eller köer i alla lagrings konton i alla resurs grupper i alla prenumerationer i hanterings gruppen.

Mer information om roll tilldelningar och omfång för Azure finns i [Vad är Azures rollbaserad åtkomst kontroll (Azure RBAC)?](../articles/role-based-access-control/overview.md).

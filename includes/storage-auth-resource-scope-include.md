---
title: inkludera fil
description: inkludera fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8b97a62626666fa39a5b0622852d9eec47c2410a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024915"
---
Innan du tilldelar en RBAC-roll till ett säkerhets objekt bör du bestämma omfattningen av åtkomsten som säkerhets objekt ska ha. Bästa praxis är att bestämma att det alltid är bäst att bara bevilja det begränsande möjliga omfånget.

I följande lista beskrivs de nivåer där du kan begränsa åtkomsten till Azure blob-och Queue-resurser, från och med det snävaste omfånget:

- **En enskild behållare.** I det här omfånget gäller en roll tilldelning för alla blobar i behållaren, samt behållar egenskaper och metadata.
- **En enskild kö.** I det här omfånget gäller en roll tilldelning för meddelanden i kön, samt köegenskaper och metadata.
- **Lagrings kontot.** I det här omfånget gäller en roll tilldelning för alla behållare och deras blobbar, eller till alla köer och deras meddelanden.
- **Resursgruppen.** I det här omfånget gäller en roll tilldelning för alla behållare eller köer i alla lagrings konton i resurs gruppen.
- **Prenumerationen.** I det här omfånget gäller en roll tilldelning för alla behållare eller köer i alla lagrings konton i alla resurs grupper i prenumerationen.

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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460492"
---
Innan du tilldelar en RBAC-roll till ett säkerhetsobjekt bestämmer du omfattningen av åtkomsten som säkerhetsobjektet ska ha. Bästa praxis dikterar att det alltid är bäst att bara bevilja minsta möjliga omfattning.

I följande lista beskrivs de nivåer där du kan begränsa åtkomsten till Azure-blob och köresurser, med början i det smalaste omfånget:

- **En enskild behållare.** I det här omfånget gäller en rolltilldelning för alla blobbar i behållaren, samt behållaregenskaper och metadata.
- **En enskild kö.** I det här omfånget gäller en rolltilldelning för meddelanden i kön, samt köegenskaper och metadata.
- **Lagringskontot.** I det här scopet gäller en rolltilldelning för alla behållare och deras blobbar, eller för alla köer och deras meddelanden.
- **Resursgruppen.** I det här scopet gäller en rolltilldelning för alla behållare eller köer i alla lagringskonton i resursgruppen.
- **Prenumerationen.** I det här scopet gäller en rolltilldelning för alla behållare eller köer i alla lagringskonton i alla resursgrupper i prenumerationen.

> [!IMPORTANT]
> Om din prenumeration innehåller ett Namnområde för Azure DataBricks kommer roller som är begränsade till prenumerationen inte att ge åtkomst till blob- och ködata. Scoperoller till resursgruppen, lagringskontot eller behållaren eller kön i stället.     

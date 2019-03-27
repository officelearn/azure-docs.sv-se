---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ad4b244b58d741ad45463297df5bd358f3ae9918
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58450149"
---
Fastställa omfattningen av åtkomst som säkerhetsobjektet bör ha innan du tilldelar en RBAC-roll till ett säkerhetsobjekt. Bästa metoder kräver att det alltid är bäst att bevilja endast den minsta möjliga definitionsområdet.

I följande lista beskrivs de nivåer som du kan begränsa åtkomst till Azure blob och kö-resurser, från och med det minsta definitionsområdet:

- **En enskild behållare.** I den här omfattningen har åtkomst till alla blobar i behållaren, samt egenskaper för behållare och metadata i ett säkerhetsobjekt.
- **En enskild kö.** I den här omfattningen har åtkomst till meddelanden i kön, samt köegenskaper och metadata i ett säkerhetsobjekt.
- **Storage-konto.** I den här omfattningen har åtkomst till alla behållare och deras blobbar eller till alla köer och deras meddelanden i ett säkerhetsobjekt.
- **Resursgruppen.** I den här omfattningen har säkerhetsobjekt åtkomst till alla behållare eller köer i alla lagringskonton i resursgruppen.
- **Prenumerationen.** I den här omfattningen har säkerhetsobjekt åtkomst till alla behållare eller köer i alla lagringskonton i alla resursgrupper i prenumerationen.

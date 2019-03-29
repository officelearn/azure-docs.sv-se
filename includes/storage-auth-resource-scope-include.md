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
ms.openlocfilehash: a50eb45291ada23f55057f3c440c5b8b23cc4bce
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622645"
---
Fastställa omfattningen av åtkomst som säkerhetsobjektet bör ha innan du tilldelar en RBAC-roll till ett säkerhetsobjekt. Bästa metoder kräver att det alltid är bäst att bevilja endast den minsta möjliga definitionsområdet.

I följande lista beskrivs de nivåer som du kan begränsa åtkomst till Azure blob och kö-resurser, från och med det minsta definitionsområdet:

- **En enskild behållare.** I den här omfattningen har åtkomst till alla blobar i behållaren, samt egenskaper för behållare och metadata i ett säkerhetsobjekt.
- **En enskild kö.** I den här omfattningen har åtkomst till meddelanden i kön, samt köegenskaper och metadata i ett säkerhetsobjekt.
- **Storage-konto.** I den här omfattningen har åtkomst till alla behållare och deras blobbar eller till alla köer och deras meddelanden i ett säkerhetsobjekt.
- **Resursgruppen.** I den här omfattningen har säkerhetsobjekt åtkomst till alla behållare eller köer i alla lagringskonton i resursgruppen.
- **Prenumerationen.** I den här omfattningen har säkerhetsobjekt åtkomst till alla behållare eller köer i alla lagringskonton i alla resursgrupper i prenumerationen.

> [!IMPORTANT]
> Om din prenumeration omfattar ett namnområde för Azure DataBricks, kommer roller som är tilldelade prenumerationsområde att blockeras från att bevilja åtkomst till blob-och kö.
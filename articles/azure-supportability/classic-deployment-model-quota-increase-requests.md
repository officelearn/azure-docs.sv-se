---
title: Azures klassiska distributionsmodell | Microsoft Docs
description: Azures klassiska distributionsmodell
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/20/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5ae2a56c084116ae8d57a16696223e7990258558
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313253"
---
# <a name="classic-deployment-model"></a>Klassisk distributionsmodell

Klassisk distributionsmodell är den äldre generationens Azure-distribution läge mark tillämpar en global vCPU-kvot för virtuella datorer och VM-skalningsuppsättningar. Klassisk distributionsmodell rekommenderas inte längre och nu har ersatts av Resource Manager-modellen. Om du vill veta mer om dessa två distribution finns modeller och nytta av Resource Manager Distributionsmodellen Resource Manager-sidan. När en ny prenumeration skapas, tilldelas en standardkvot på virtuella processorer till den. Varje gång en ny virtuell dator är att distribueras med hjälp av klassiska distributionsmodellen, överstiga summan av nya och befintliga virtuella processorer användning i alla regioner inte vCPU-kvoten som godkänts för den klassiska distributionsmodellen. Läs mer om kvoter på Azure-prenumeration och tjänstens gränser.

Du kan begära en ökning av virtuella processorer gränsen för klassiska distributionsmodellen via Hjälp + Support-bladet och användningar + kvot-bladet i portalen.


---
title: Ändra vyn för virtuell dator-session till helskärm i Azure Skyddsmiljö | Microsoft Docs
description: I den här artikeln lär du dig hur du ändrar vyn till hela skärmen.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 6a063d6c8891133126924bfb934770f7818db71a
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191556"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion-preview"></a>Ändra till helskärmsläge för en vm-session: Azure Skyddsmiljö (förhandsversion)

Den här artikeln hjälper dig att ändra vyn virtuell dator till helskärm och tillbaka i webbläsaren. Innan du börjar arbeta med en virtuell dator, kontrollera att du har följt stegen för att [skapa en Skyddsmiljö-värd](bastion-create-host-portal.md). Sedan kan ansluta till den virtuella datorn som du vill arbeta med med hjälp av antingen [RDP](bastion-connect-vm-rdp.md) eller [SSH](bastion-connect-vm-ssh.md).

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="launch-the-clipboard-tool"></a>Starta verktyget Urklipp

Under fjärrsessionen, starta paletten Skyddsmiljö Urklipp åtkomst verktyget genom att välja de två pilarna som fanns i vänsterkant sessionen.

![verktyg](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>Välj helskärm

Välj den **helskärm** knappen för att växla sessionen till en upplevelse för hela skärmen. När du byter återinitierar sessionen att hela skärmen.

![helskärm](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>Nästa steg

Läs den [Skyddsmiljö vanliga frågor och svar](bastion-faq.md).
Lär dig hur du [kopiera och klistra in](bastion-vm-copy-paste.md) till och från en Azure-dator.
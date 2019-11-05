---
title: Ändra vyn för virtuell dator-session till full skärm i Azure skydds | Microsoft Docs
description: I den här artikeln lär du dig hur du ändrar vyn till hel skärms läge.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: a8c9cab825644fe797713c80d3a710c7a2d54850
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498055"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion"></a>Ändra till hel skärms läge för en VM-session: Azure skydds

Den här artikeln hjälper dig att ändra den virtuella datorns vy till full skärm och tillbaka i webbläsaren. Innan du arbetar med en virtuell dator bör du kontrol lera att du har följt stegen för att [skapa en skydds-värd](bastion-create-host-portal.md). Anslut sedan till den virtuella dator som du vill arbeta med via [RDP](bastion-connect-vm-rdp.md) eller [SSH](bastion-connect-vm-ssh.md).

## <a name="launch-the-clipboard-tool"></a>Starta urklipps verktyget

Under fjärrsessionen startar du skydds urklipps åtkomst verktyg genom att välja de två pilarna som finns längst till vänster i sessionen.

![verktyg](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>Välj hel skärms läge

Välj knappen **hel** skärm för att växla sessionen till en hel skärms upplevelse. När du har växlat ominitieras sessionen till full skärm.

![hel skärms läge](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor och svar om skydds](bastion-faq.md).
Lär dig hur du [kopierar och klistrar in](bastion-vm-copy-paste.md) till och från en virtuell Azure-dator.
---
title: 'Azure-skydds: Visa virtuell dator-session: hel skärms läge'
description: I den här artikeln lär du dig hur du ändrar vyn till hel skärms läge.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: f4aa2c5aa3f40802db7be64eeb778866819bfe67
ms.sourcegitcommit: 24f31287b6a526e23ff5b5469113522d1ccd4467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84744229"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion"></a>Ändra till hel skärms läge för en VM-session: Azure skydds

Den här artikeln hjälper dig att ändra den virtuella datorns vy till full skärm och tillbaka i webbläsaren. Innan du arbetar med en virtuell dator bör du kontrol lera att du har följt stegen för att [skapa en skydds-värd](bastion-create-host-portal.md). Anslut sedan till den virtuella dator som du vill arbeta med via [RDP](bastion-connect-vm-rdp.md) eller [SSH](bastion-connect-vm-ssh.md).

## <a name="launch-the-clipboard-tool"></a>Starta urklipps verktyget

Under fjärrsessionen startar du skydds urklipps åtkomst verktyg genom att välja de två pilarna som finns längst till vänster i sessionen.

![tools](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>Välj hel skärms läge

Välj knappen **hel** skärm för att växla sessionen till en hel skärms upplevelse. När du har växlat ominitieras sessionen till full skärm.

![helskärm](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor och svar om skydds](bastion-faq.md).
Lär dig hur du [kopierar och klistrar in](bastion-vm-copy-paste.md) till och från en virtuell Azure-dator.

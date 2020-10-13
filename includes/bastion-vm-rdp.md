---
title: inkludera fil
description: inkludera fil
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b079ede0845de3794507691bc3c252930e2a6604
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978150"
---
1. Öppna [Azure-portalen](https://portal.azure.com). Navigera till den virtuella dator som du vill ansluta till och välj sedan **Anslut**. Välj **skydds** i list rutan.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="Välj skydds" border="false":::

1. När du har valt skydds i list rutan visas ett sido fält med tre flikar: RDP, SSH och skydds. Eftersom skydds etablerades för det virtuella nätverket är fliken skydds aktiv som standard. Välj **Använd skydds**.

   :::image type="content" source="./media/bastion-vm-rdp/use-bastion.png" alt-text="Välj skydds" border="false":::

1. På sidan **Anslut med Azure-skydds** anger du användar namn och lösen ord för den virtuella datorn och väljer sedan **Anslut**.

   :::image type="content" source="./media/bastion-vm-rdp/host.png" alt-text="Välj skydds" border="false":::

1. RDP-anslutningen till den virtuella datorn via skydds öppnas direkt i Azure Portal (via HTML5) med port 443 och skydds-tjänsten.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Välj skydds" border="false":::

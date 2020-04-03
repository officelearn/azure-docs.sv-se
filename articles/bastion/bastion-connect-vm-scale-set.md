---
title: Ansluta till en windows-skalningsuppsättning för virtuella datorer med Azure Bastion | Microsoft-dokument
description: I den här artikeln får du lära dig hur du ansluter till en Azure-skalningsuppsättning för virtuella datorer med Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 290a20fcd827841c24983f3bdd54b6db8e154462
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619349"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Ansluta till en skalningsuppsättning för virtuella datorer med Azure Bastion

Den här artikeln visar hur du på ett säkert och smidigt sätt rdp till din Windows-uppsättning för virtuell datorskala i ett virtuellt Azure-nätverk med Azure Bastion. Du kan ansluta till en uppsättning instans för skalning av virtuella datorer direkt från Azure-portalen. När du använder Azure Bastion kräver virtuella datorer inte en klient, agent eller ytterligare programvara. Mer information om Azure Bastion finns i [översikten](bastion-overview.md).

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har konfigurerat en Azure Bastion-värd för det virtuella nätverket där skalningsuppsättningen för den virtuella datorn finns. Mer information finns i [Skapa en Azure Bastion-värd](bastion-create-host-portal.md). När Tjänsten Bastion har etablerats och distribuerats i det virtuella nätverket kan du använda den för att ansluta till en uppsättning instans för skalning av virtuella datorer i det här virtuella nätverket. Bastion förutsätter att du använder RDP för att ansluta till en Windows virtuell dator skalningsuppsättning, och SSH för att ansluta till din Linux virtuell dator skala uppsättning. Information om anslutning till en Virtuell Linux finns i [Ansluta till en virtuell dator - Linux](bastion-connect-vm-ssh.md).

## <a name="connect-using-rdp"></a><a name="rdp"></a>Ansluta med RDP

1. Öppna [Azure-portalen](https://portal.azure.com). Navigera till den skaluppsättning för virtuella datorer som du vill ansluta till.

   ![navigate](./media/bastion-connect-vm-scale-set/1.png)
2. Navigera till den uppsättning instans för virtuell dator som du vill ansluta till och välj sedan **Anslut**. När du använder en RDP-anslutning bör skaluppsättningen för den virtuella datorn vara en skalauppsättning för den virtuella datorn i Windows.

   ![skalningsuppsättning för virtuell dator](./media/bastion-connect-vm-scale-set/2.png)
3. När du har valt **Anslut**visas ett sidofält med tre flikar – RDP, SSH och Bastion. Välj fliken **Bastion** i sidofältet. Om du inte har etablerat Bastion för det virtuella nätverket kan du välja länken för att konfigurera Bastion. Konfigurationsinstruktioner finns i [Konfigurera Bastion](bastion-create-host-portal.md).

   ![Fliken Bastion](./media/bastion-connect-vm-scale-set/3.png)
4. På fliken Bastion anger du användarnamn och lösenord för din virtuella datorskalauppsättning och väljer sedan **Anslut**.

   ![connect](./media/bastion-connect-vm-scale-set/4.png)
5. RDP-anslutningen till den här virtuella datorn via Bastion öppnas direkt i Azure-portalen (över HTML5) med port 443 och Bastion-tjänsten.

## <a name="next-steps"></a>Nästa steg

Läs [Vanliga frågor om Bastion](bastion-faq.md).
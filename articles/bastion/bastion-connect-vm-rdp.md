---
title: Ansluta till en virtuell Windows-dator med Azure skydds | Microsoft Docs
description: I den här artikeln får du lära dig hur du ansluter till en virtuell Azure-dator som kör Windows med hjälp av Azure-skydds.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: dc741007c7de8d8e24f9c0f9e4e0c03306d036a4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498354"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Ansluta till en virtuell Windows-dator med Azure skydds

Den här artikeln visar hur du på ett säkert och smidigt sätt kan använda RDP för dina virtuella Windows-datorer i ett virtuellt Azure-nätverk med Azure skydds. Du kan ansluta till en virtuell dator direkt från Azure-portalen. När du använder Azure Bastion behövs ingen klient, agent eller ytterligare programvara för den virtuella datorn. Mer information om Azure-skydds finns i [översikten](bastion-overview.md).

## <a name="before-you-begin"></a>Innan du börjar

Kontrol lera att du har konfigurerat en Azure skydds-värd för det virtuella nätverk där den virtuella datorn finns. Mer information finns i [skapa en Azure skydds-värd](bastion-create-host-portal.md). När skydds-tjänsten har tillhandahållits och distribuerats i det virtuella nätverket kan du använda den för att ansluta till en virtuell dator i det här virtuella nätverket. Skydds förutsätter att du använder RDP för att ansluta till en virtuell Windows-dator och SSH för att ansluta till dina virtuella Linux-datorer. Information om hur du ansluter till en virtuell Linux-dator finns i [ansluta till en VM – Linux](bastion-connect-vm-ssh.md).

Följande roller krävs för att upprätta en anslutning:

* Rollen läsare på den virtuella datorn
* Rollen läsare på NÄTVERKSKORTet med den virtuella datorns privata IP-adress
* Läsar roll på Azure skydds-resursen

## <a name="rdp"></a>Anslut via RDP

1. Öppna [Azure-portalen](https://portal.azure.com). Navigera till den virtuella dator som du vill ansluta till och klicka sedan på **Anslut**. Den virtuella datorn ska vara en virtuell Windows-dator när en RDP-anslutning används.

   ![Anslut till virtuell dator](./media/bastion-connect-vm-rdp/connect.png)
1. När du klickar på Anslut visas ett sido fält med tre flikar – RDP, SSH och skydds. Om skydds etablerades för det virtuella nätverket är fliken skydds aktiv som standard. Om du inte etablerar skydds för det virtuella nätverket kan du klicka på länken för att konfigurera skydds. Konfigurations anvisningar finns i [Konfigurera skydds](bastion-create-host-portal.md).

   ![Anslut till virtuell dator](./media/bastion-connect-vm-rdp/bastion.png)
1. På fliken skydds, användar namn och lösen ord för den virtuella datorn klickar du på **Anslut**. RDP-anslutningen till den virtuella datorn via skydds öppnas direkt i Azure Portal (via HTML5) med port 443 och skydds-tjänsten.

   ![Anslut till virtuell dator](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor och svar om skydds](bastion-faq.md)
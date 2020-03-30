---
title: Ansluta till en Virtuell Windows-dator med Azure Bastion
description: I den här artikeln kan du läsa om hur du ansluter till en Virtuell Azure-dator som kör Windows med hjälp av Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 6ec60dc313c8a4374637adf38ea0e5a7d4ed964b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597360"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Ansluta till en virtuell Windows-dator med Azure Bastion

Med Azure Bastion kan du på ett säkert och smidigt sätt ansluta till dina virtuella datorer via SSL direkt i Azure-portalen. När du använder Azure Bastion kräver inte dina virtuella datorer en klient, agent eller ytterligare programvara. Den här artikeln visar hur du ansluter till dina virtuella windows-datorer. Information om hur du ansluter till en Virtuell Linux-dator finns i [Ansluta till en virtuell dator med Azure Bastion - Linux](bastion-connect-vm-ssh.md).

Azure Bastion ger säker anslutning till alla virtuella datorer i det virtuella nätverket där det etableras. Med Azure Bastion skyddas dina virtuella datorer från att exponera RDP/SSH-portar till omvärlden, samtidigt som du ger säker åtkomst med RDP/SSH. Mer information finns i [Översikt](bastion-overview.md).

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har konfigurerat en Azure Bastion-värd för det virtuella nätverket där den virtuella datorn finns. När Tjänsten Bastion har etablerats och distribuerats i det virtuella nätverket kan du använda den för att ansluta till valfri virtuell dator i det virtuella nätverket. Information om hur du konfigurerar en Azure Bastion-värd finns i [Skapa en Azure Bastion-värd](bastion-create-host-portal.md).

### <a name="required-roles"></a>Obligatoriska roller

För att upprätta en anslutning krävs följande roller:

* Läsarroll på den virtuella datorn
* Läsarroll på nätverkskortet med den virtuella datorns privata IP
* Läsarrollen på Azure Bastion-resursen

### <a name="ports"></a>Portar

Om du vill ansluta till den virtuella datorn i Windows måste du ha följande portar öppna på din virtuella Windows-dator:

* Inkommande portar: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Ansluta

1. Öppna [Azure-portalen](https://portal.azure.com). Navigera till den virtuella dator som du vill ansluta till och klicka sedan på **Anslut** och välj **Bastion** i listrutan.

   ![Vm-anslutning](./media/bastion-connect-vm-rdp/connect.png)
1. När du har klickat på Bastion visas ett sidofält med tre flikar – RDP, SSH och Bastion. Om Bastion har etablerats för det virtuella nätverket är fliken Bastion aktiv som standard. Om du inte har etablerat Bastion för det virtuella nätverket kan du klicka på länken för att konfigurera Bastion. Konfigurationsinstruktioner finns i [Konfigurera Bastion](bastion-create-host-portal.md).

   ![fliken bastion](./media/bastion-connect-vm-rdp/bastion.png)
1. På fliken Bastion anger du användarnamn och lösenord för din virtuella dator och klickar sedan på **Anslut**. RDP-anslutningen till den här virtuella datorn via Bastion öppnas direkt i Azure-portalen (över HTML5) med port 443 och Bastion-tjänsten.

   ![RDP-anslutning](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor](bastion-faq.md) om Bastion

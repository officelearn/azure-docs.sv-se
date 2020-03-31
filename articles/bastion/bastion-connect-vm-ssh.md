---
title: Ansluta till en virtuell Linux-dator med Azure Bastion
description: I den här artikeln kan du läsa om hur du ansluter till Linux Virtual Machine med hjälp av Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 381f45a51002188c72174cff83c26b829912a0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596835"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Ansluta med SSH till en virtuell Linux-dator med Azure Bastion

Den här artikeln visar hur du på ett säkert och smidigt sätt SSH till dina virtuella Linux-datorer i ett virtuellt Azure-nätverk. Du kan ansluta till en virtuell dator direkt från Azure-portalen. När du använder Azure Bastion kräver virtuella datorer inte en klient, agent eller ytterligare programvara. Mer information om Azure Bastion finns i [översikten](bastion-overview.md).

Du kan använda Azure Bastion för att ansluta till en virtuell Linux-dator med SSH. Du kan använda både användarnamn/lösenord och SSH-nycklar för autentisering. Du kan ansluta till din virtuella dator med SSH-nycklar med hjälp av antingen:

* En privat nyckel som du anger manuellt
* En fil som innehåller den privata nyckelinformationen

Den privata nyckeln SSH måste vara i `"-----BEGIN RSA PRIVATE KEY-----"` ett `"-----END RSA PRIVATE KEY-----"`format som börjar med och slutar med .

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har konfigurerat en Azure Bastion-värd för det virtuella nätverk där den virtuella datorn finns. Mer information finns i [Skapa en Azure Bastion-värd](bastion-create-host-portal.md). När Tjänsten Bastion har etablerats och distribuerats i det virtuella nätverket kan du använda den för att ansluta till valfri virtuell dator i det här virtuella nätverket. 

När du använder Bastion för att ansluta förutsätter det att du använder RDP för att ansluta till en Windows VM och SSH för att ansluta till dina virtuella Linux-datorer. Information om hur du ansluter till en Virtuell Windows finns i [Ansluta till en virtuell dator - Windows](bastion-connect-vm-rdp.md).

### <a name="required-roles"></a>Obligatoriska roller

För att kunna ansluta krävs följande roller:

* Läsarroll på den virtuella datorn
* Läsarroll på nätverkskortet med den virtuella datorns privata IP
* Läsarrollen på Azure Bastion-resursen

### <a name="ports"></a>Portar

För att kunna ansluta till Linux VM via SSH måste du ha följande portar öppna på den virtuella datorn:

* Inkommande port: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>Anslut: Använda användarnamn och lösenord

1. Öppna [Azure-portalen](https://portal.azure.com). Navigera till den virtuella dator som du vill ansluta till och klicka sedan på **Anslut** och välj **Bastion** i listrutan.

   ![Anslut](./media/bastion-connect-vm-ssh/connect.png)
1. När du har klickat på Bastion visas ett sidofält med tre flikar – RDP, SSH och Bastion. Om Bastion har etablerats för det virtuella nätverket är fliken Bastion aktiv som standard. Om du inte har etablerat Bastion för det virtuella nätverket läser du [Konfigurera Bastion](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Ange användarnamn och lösenord för SSH till din virtuella dator.
1. Klicka på **Knappen Anslut** när du har angett nyckeln.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Anslut: Ange manuellt en privat nyckel

1. Öppna [Azure-portalen](https://portal.azure.com). Navigera till den virtuella dator som du vill ansluta till och klicka sedan på **Anslut** och välj **Bastion** i listrutan.

   ![Anslut](./media/bastion-connect-vm-ssh/connect.png)
1. När du har klickat på Bastion visas ett sidofält med tre flikar – RDP, SSH och Bastion. Om Bastion har etablerats för det virtuella nätverket är fliken Bastion aktiv som standard. Om du inte har etablerat Bastion för det virtuella nätverket läser du [Konfigurera Bastion](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Ange användarnamnet och välj **SSH Private Key**.
1. Ange din privata nyckel i textområdet **SSH Private Key** (eller klistra in den direkt).
1. Klicka på **Knappen Anslut** när du har angett nyckeln.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Ansluta: Använda en privat nyckelfil

1. Öppna [Azure-portalen](https://portal.azure.com). Navigera till den virtuella dator som du vill ansluta till och klicka sedan på **Anslut** och välj **Bastion** i listrutan.

   ![Anslut](./media/bastion-connect-vm-ssh/connect.png)
1. När du har klickat på Bastion visas ett sidofält med tre flikar – RDP, SSH och Bastion. Om Bastion har etablerats för det virtuella nätverket är fliken Bastion aktiv som standard. Om du inte har etablerat Bastion för det virtuella nätverket läser du [Konfigurera Bastion](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Ange användarnamnet och välj **SSH Private Key från Local File**.
1. Klicka på knappen **Bläddra** (mappikonen i den lokala filen).
1. Bläddra efter filen och klicka sedan på **Öppna**.
1. Klicka på **Anslut** för att ansluta till den virtuella datorn. När du klickar på Anslut öppnas SSH till den här virtuella datorn direkt i Azure-portalen. Den här anslutningen är över HTML5 med port 443 på Bastion-tjänsten via den privata IP-adressen för din virtuella dator.

## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor](bastion-faq.md) om Bastion

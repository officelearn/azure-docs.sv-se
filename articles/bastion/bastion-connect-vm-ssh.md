---
title: Ansluta till en virtuell Linux-dator med Azure skydds
description: I den här artikeln får du lära dig hur du ansluter till en virtuell Linux-dator med hjälp av Azure-skydds.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 381f45a51002188c72174cff83c26b829912a0b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77596835"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Ansluta med SSH till en virtuell Linux-dator med Azure skydds

Den här artikeln visar hur du på ett säkert och smidigt sätt kan använda SSH för virtuella Linux-datorer i ett virtuellt Azure-nätverk. Du kan ansluta till en virtuell dator direkt från Azure Portal. När du använder Azure skydds kräver inte virtuella datorer en klient, agent eller ytterligare program vara. Mer information om Azure-skydds finns i [översikten](bastion-overview.md).

Du kan använda Azure-skydds för att ansluta till en virtuell Linux-dator med SSH. Du kan använda både användar namn/lösen ord och SSH-nycklar för autentisering. Du kan ansluta till din virtuella dator med SSH-nycklar genom att använda antingen:

* En privat nyckel som du anger manuellt
* En fil som innehåller information om privat nyckel

Den privata SSH-nyckeln måste vara i ett format som börjar `"-----BEGIN RSA PRIVATE KEY-----"` med och slutar `"-----END RSA PRIVATE KEY-----"`med.

## <a name="before-you-begin"></a>Innan du börjar

Kontrol lera att du har konfigurerat en Azure skydds-värd för det virtuella nätverk där den virtuella datorn finns. Mer information finns i [skapa en Azure skydds-värd](bastion-create-host-portal.md). När skydds-tjänsten har tillhandahållits och distribuerats i det virtuella nätverket kan du använda den för att ansluta till en virtuell dator i det här virtuella nätverket. 

När du använder skydds för att ansluta antar det att du använder RDP för att ansluta till en virtuell Windows-dator och SSH för att ansluta till dina virtuella Linux-datorer. Information om hur du ansluter till en virtuell Windows-dator finns i [ansluta till en VM-Windows](bastion-connect-vm-rdp.md).

### <a name="required-roles"></a>Nödvändiga roller

Följande roller krävs för att upprätta en anslutning:

* Rollen läsare på den virtuella datorn
* Rollen läsare på NÄTVERKSKORTet med den virtuella datorns privata IP-adress
* Läsar roll på Azure skydds-resursen

### <a name="ports"></a>Portar

För att kunna ansluta till den virtuella Linux-datorn via SSH måste du ha följande portar öppna på den virtuella datorn:

* Inkommande port: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>Anslut: Använd användar namn och lösen ord

1. Öppna [Azure Portal](https://portal.azure.com). Navigera till den virtuella dator som du vill ansluta till och klicka sedan på **Anslut** och välj **skydds** i list rutan.

   ![Anslut](./media/bastion-connect-vm-ssh/connect.png)
1. När du klickar på skydds visas ett sido fält med tre flikar – RDP, SSH och skydds. Om skydds etablerades för det virtuella nätverket är fliken skydds aktiv som standard. Om du inte etablerar skydds för det virtuella nätverket, se [Konfigurera skydds](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Ange användar namn och lösen ord för SSH till den virtuella datorn.
1. Klicka på knappen **Anslut** när du har angett nyckeln.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Anslut: Ange en privat nyckel manuellt

1. Öppna [Azure Portal](https://portal.azure.com). Navigera till den virtuella dator som du vill ansluta till och klicka sedan på **Anslut** och välj **skydds** i list rutan.

   ![Anslut](./media/bastion-connect-vm-ssh/connect.png)
1. När du klickar på skydds visas ett sido fält med tre flikar – RDP, SSH och skydds. Om skydds etablerades för det virtuella nätverket är fliken skydds aktiv som standard. Om du inte etablerar skydds för det virtuella nätverket, se [Konfigurera skydds](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Ange användar namnet och välj **privat SSH-nyckel**.
1. Ange din privata nyckel i text ytans **privata SSH-nyckel** (eller klistra in den direkt).
1. Klicka på knappen **Anslut** när du har angett nyckeln.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Anslut: använder en privat nyckel fil

1. Öppna [Azure Portal](https://portal.azure.com). Navigera till den virtuella dator som du vill ansluta till och klicka sedan på **Anslut** och välj **skydds** i list rutan.

   ![Anslut](./media/bastion-connect-vm-ssh/connect.png)
1. När du klickar på skydds visas ett sido fält med tre flikar – RDP, SSH och skydds. Om skydds etablerades för det virtuella nätverket är fliken skydds aktiv som standard. Om du inte etablerar skydds för det virtuella nätverket, se [Konfigurera skydds](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Ange användar namnet och välj **SSH privat nyckel från lokal fil**.
1. Klicka på knappen **Bläddra** (mappikonen i den lokala filen).
1. Bläddra efter filen och klicka sedan på **Öppna**.
1. Klicka på **Anslut** för att ansluta till den virtuella datorn. När du klickar på Anslut öppnas SSH till den virtuella datorn direkt i Azure Portal. Den här anslutningen är över HTML5 med port 443 på skydds-tjänsten via den privata IP-adressen för den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor och svar om skydds](bastion-faq.md)

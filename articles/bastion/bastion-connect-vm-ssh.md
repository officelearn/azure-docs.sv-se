---
title: Ansluta till en virtuell Linux-dator med Azure skydds | Microsoft Docs
description: I den här artikeln får du lära dig hur du ansluter till en virtuell Linux-dator med hjälp av Azure-skydds.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: b88327ea0b5d2958cc1c86fa317415f2441af894
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494477"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Ansluta med SSH till en virtuell Linux-dator med Azure skydds

Den här artikeln visar hur du på ett säkert och smidigt sätt kan använda SSH för virtuella Linux-datorer i ett virtuellt Azure-nätverk. Du kan ansluta till en virtuell dator direkt från Azure-portalen. När du använder Azure Bastion behövs ingen klient, agent eller ytterligare programvara för den virtuella datorn. Mer information om Azure-skydds finns i [översikten](bastion-overview.md).

Du kan använda Azure-skydds för att ansluta till en virtuell Linux-dator med SSH. Du kan använda både användar namn/lösen ord och SSH-nycklar för autentisering. Du kan ansluta till din virtuella dator med SSH-nycklar genom att använda antingen:

* En privat nyckel som du anger manuellt
* En fil som innehåller information om privat nyckel

Den privata SSH-nyckeln måste vara i ett format som börjar med `"-----BEGIN RSA PRIVATE KEY-----"` och slutar med `"-----END RSA PRIVATE KEY-----"`.

## <a name="before-you-begin"></a>Innan du börjar

Kontrol lera att du har konfigurerat en Azure skydds-värd för det virtuella nätverk där den virtuella datorn finns. Mer information finns i [skapa en Azure skydds-värd](bastion-create-host-portal.md). När skydds-tjänsten har tillhandahållits och distribuerats i det virtuella nätverket kan du använda den för att ansluta till en virtuell dator i det här virtuella nätverket. När du använder skydds för att ansluta antar det att du använder RDP för att ansluta till en virtuell Windows-dator och SSH för att ansluta till dina virtuella Linux-datorer.

Följande roller krävs för att upprätta en anslutning:

* Rollen läsare på den virtuella datorn
* Rollen läsare på NÄTVERKSKORTet med den virtuella datorns privata IP-adress
* Läsar roll på Azure skydds-resursen

## <a name="username"></a>Anslut: Använd användar namn och lösen ord

1.   Öppna [Azure-portalen](https://portal.azure.com). Navigera till den virtuella dator som du vill ansluta till och klicka sedan på **Anslut**. Den virtuella datorn bör vara en virtuell Linux-dator när en SSH-anslutning används.
1. När du klickar på Anslut visas ett sido fält med tre flikar – RDP, SSH och skydds. Om skydds etablerades för det virtuella nätverket är fliken skydds aktiv som standard. Om du inte etablerar skydds för det virtuella nätverket, se [Konfigurera skydds](bastion-create-host-portal.md).

   ![Anslut till virtuell dator](./media/bastion-connect-vm-ssh/bastion.png)
1. Ange användar namn och lösen ord för SSH till den virtuella datorn.
1. Klicka på knappen **Anslut** när du har angett nyckeln.

## <a name="privatekey"></a>Anslut: Ange en privat nyckel manuellt

1. Öppna [Azure-portalen](https://portal.azure.com). Navigera till den virtuella dator som du vill ansluta till och klicka sedan på **Anslut**. Den virtuella datorn bör vara en virtuell Linux-dator när en SSH-anslutning används.
1. När du klickar på Anslut visas ett sido fält med tre flikar – RDP, SSH och skydds. Om skydds etablerades för det virtuella nätverket är fliken skydds aktiv som standard. Om du inte etablerar skydds för det virtuella nätverket, se [Konfigurera skydds](bastion-create-host-portal.md).

   ![Anslut till virtuell dator](./media/bastion-connect-vm-ssh/bastion.png)
1. Ange användar namnet och välj **privat SSH-nyckel**.
1. Ange din privata nyckel i text ytans **privata SSH-nyckel** (eller klistra in den direkt).
1. Klicka på knappen **Anslut** när du har angett nyckeln.

## <a name="ssh"></a>Anslut: använder en privat nyckel fil

1. Öppna [Azure-portalen](https://portal.azure.com). Navigera till den virtuella dator som du vill ansluta till och klicka sedan på **Anslut**. Den virtuella datorn bör vara en virtuell Linux-dator när en SSH-anslutning används.

   ![Anslut till virtuell dator](./media/bastion-connect-vm-ssh/connect.png)
1. När du klickar på Anslut visas ett sido fält med tre flikar – RDP, SSH och skydds. Om skydds etablerades för det virtuella nätverket är fliken skydds aktiv som standard. Om du inte etablerar skydds för det virtuella nätverket, se [Konfigurera skydds](bastion-create-host-portal.md).

   ![Anslut till virtuell dator](./media/bastion-connect-vm-ssh/bastion.png)
1. Ange användar namnet och välj **SSH privat nyckel från lokal fil**.
1. Klicka på knappen **Bläddra** (mappikonen i den lokala filen).
1. Bläddra efter filen och klicka sedan på **Öppna**.
1. Klicka på **Anslut** för att ansluta till den virtuella datorn. När du klickar på Anslut öppnas SSH till den virtuella datorn direkt i Azure Portal. Den här anslutningen är över HTML5 med port 443 på skydds-tjänsten via den privata IP-adressen för den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor och svar om skydds](bastion-faq.md)
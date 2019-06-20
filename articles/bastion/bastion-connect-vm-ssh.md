---
title: Anslut till en Linux-VM med hjälp av Azure Skyddsmiljö | Microsoft Docs
description: I den här artikeln lär du dig hur du ansluter till Linux-dator med hjälp av Azure Skyddsmiljö.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 572043598c71a400e154c5c2e9e6c2f1e9b4ab49
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191803"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion-preview"></a>Ansluta med SSH till en Linux-dator med hjälp av Azure Skyddsmiljö (förhandsversion)

Den här artikeln visar hur du säkert och smidigt SSH till din virtuella Linux-datorer i Azure-nätverk. Du kan ansluta till en virtuell dator direkt från Azure-portalen. När du använder Azure Bastion behövs ingen klient, agent eller ytterligare programvara för den virtuella datorn. Läs mer om Azure Skyddsmiljö den [översikt](bastion-overview.md).

Du kan använda Azure Skyddsmiljö för att ansluta till en Linux-dator med SSH. Du kan använda både användarnamn/lösenord och SSH-nycklar för autentisering. Du kan ansluta till den virtuella datorn med SSH-nycklar med hjälp av antingen:

* En privat nyckel som du anger manuellt
* En fil som innehåller informationen om privata nyckeln

SSH-privata nyckeln måste vara i ett format som börjar med `"-----BEGIN RSA PRIVATE KEY-----"` och slutar med `"-----END RSA PRIVATE KEY-----"`.

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har konfigurerat en Azure-Skyddsmiljö-värd för det virtuella nätverket där den virtuella datorn finns. Mer information finns i [skapa en Azure-Skyddsmiljö-värd](bastion-create-host-portal.md). När tjänsten Skyddsmiljö etableras och distribueras i det virtuella nätverket, kan du använda den för att ansluta till virtuella datorer i det här virtuella nätverket. I den här förhandsversionen när du använder Skyddsmiljö för att ansluta, förutsätts att du använder RDP för att ansluta till en Windows-VM och SSH för att ansluta till din virtuella Linux-datorer.

För att upprätta en anslutning, krävs följande roller:

* Läsarroll för på den virtuella datorn
* Läsarroll på nätverkskortet med privata IP-Adressen för den virtuella datorn
* Läsarroll för Azure-skyddade resursen

## <a name="username"></a>Anslut: Med användarnamn och lösenord


1. I den [Azure-portalen](https://aka.ms/BastionHost) för förhandsversionen av Skyddsmiljö, navigera till den virtuella dator som du vill ansluta till och klicka sedan på **Connect**. Den virtuella datorn ska vara en Linux-dator när du använder en SSH-anslutning.
1. När du klickar på Anslut visas ett sida-fält som har tre flikar – RDP och SSH Skyddsmiljö. Om Skyddsmiljö etablerades för det virtuella nätverket, är fliken Skyddsmiljö aktiv som standard. Om du inte har etablerat Skyddsmiljö för det virtuella nätverket, se [konfigurera Skyddsmiljö](bastion-create-host-portal.md). Om du inte ser **Skyddsmiljö** visas, du har inte öppnat preview-portalen. Öppna portalens med [den här länken](https://aka.ms/BastionHost).

      ![VM-anslutning](./media/bastion-connect-vm-ssh/bastion.png)

1. Ange användarnamn och lösenord för SSH till den virtuella datorn.
1. Klicka på **Connect** knappen när du har angett nyckeln.

## <a name="privatekey"></a>Anslut: Ange manuellt en privat nyckel

1. I den [Azure-portalen](https://aka.ms/BastionHost) för förhandsversionen av Skyddsmiljö, navigera till den virtuella dator som du vill ansluta till och klicka sedan på **Connect**. Den virtuella datorn ska vara en Linux-dator när du använder en SSH-anslutning.
1. När du klickar på Anslut visas ett sida-fält som har tre flikar – RDP och SSH Skyddsmiljö. Om Skyddsmiljö etablerades för det virtuella nätverket, är fliken Skyddsmiljö aktiv som standard. Om du inte har etablerat Skyddsmiljö för det virtuella nätverket, se [konfigurera Skyddsmiljö](bastion-create-host-portal.md). Om du inte ser **Skyddsmiljö** visas, du har inte öppnat preview-portalen. Öppna portalens med [den här länken](https://aka.ms/BastionHost).

      ![VM-anslutning](./media/bastion-connect-vm-ssh/bastion.png)

1. Ange användarnamn och välj **SSH privat nyckel**.
1. Ange din privata nyckel i textområdet **SSH privat nyckel** (eller klistra in den direkt).
1. Klicka på **Connect** knappen när du har angett nyckeln.

## <a name="ssh"></a>Anslut: Med hjälp av en fil för privat nyckel

1. I den [Azure-portalen](https://aka.ms/BastionHost) för förhandsversionen av Skyddsmiljö, navigera till den virtuella dator som du vill ansluta till och klicka sedan på **Connect**. Den virtuella datorn ska vara en Linux-dator när du använder en SSH-anslutning.

    ![VM-anslutning](./media/bastion-connect-vm-ssh/connect.png)

1. När du klickar på Anslut visas ett sida-fält som har tre flikar – RDP och SSH Skyddsmiljö. Om Skyddsmiljö etablerades för det virtuella nätverket, är fliken Skyddsmiljö aktiv som standard. Om du inte har etablerat Skyddsmiljö för det virtuella nätverket, se [konfigurera Skyddsmiljö](bastion-create-host-portal.md). Om du inte ser **Skyddsmiljö** visas, du har inte öppnat preview-portalen. Öppna portalens med [den här länken](https://aka.ms/BastionHost).

    ![VM-anslutning](./media/bastion-connect-vm-ssh/bastion.png)

1. Ange användarnamn och välj **SSH privat nyckel från en lokal fil**.
1. Klicka på den **Bläddra** knappen (mappikonen i den lokala filen).
1. Bläddra till filen och klicka sedan på **öppna**.
1. Klicka på **Connect** att ansluta till den virtuella datorn. När du klickar på öppnas direkt Connect, SSH till den här virtuella datorn i Azure-portalen. Den här anslutningen är över HTML5 via port 443 på tjänsten Skyddsmiljö över den privata IP-Adressen för den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

Läs den [Skyddsmiljö vanliga frågor och svar](bastion-faq.md)
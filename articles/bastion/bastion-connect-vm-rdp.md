---
title: Anslut till en Windows-VM med hjälp av Azure Skyddsmiljö | Microsoft Docs
description: I den här artikeln lär du dig hur du ansluter till en Azure-dator som kör Windows med hjälp av Azure Skyddsmiljö.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 376b7042a513dd50647dc8f88bf1de70f65bb21c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478406"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion-preview"></a>Ansluta till en Windows-dator med hjälp av Azure Skyddsmiljö (förhandsversion)

Den här artikeln visar hur till säkert och smidigt RDP till dina virtuella Windows-datorer i ett virtuellt Azure nätverk med hjälp av Azure Skyddsmiljö. Du kan ansluta till en virtuell dator direkt från Azure-portalen. När du använder Azure Bastion behövs ingen klient, agent eller ytterligare programvara för den virtuella datorn. Läs mer om Azure Skyddsmiljö den [översikt](bastion-overview.md).

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har konfigurerat en Azure-Skyddsmiljö-värd för det virtuella nätverket där den virtuella datorn finns. Mer information finns i [skapa en Azure-Skyddsmiljö-värd](bastion-create-host-portal.md). När tjänsten Skyddsmiljö etableras och distribueras i det virtuella nätverket, kan du använda den för att ansluta till virtuella datorer i det här virtuella nätverket. I den här förhandsversionen förutsätter Skyddsmiljö att du använder RDP för att ansluta till en Windows-VM och SSH för att ansluta till din virtuella Linux-datorer. Information om anslutningen till en Linux-VM finns i [Anslut till en virtuell dator – Linux](bastion-connect-vm-ssh.md).

För att upprätta en anslutning, krävs följande roller:

* Läsarroll för på den virtuella datorn
* Läsarroll på nätverkskortet med privata IP-Adressen för den virtuella datorn
* Läsarroll för Azure-skyddade resursen

## <a name="rdp"></a>Ansluta med RDP

1. Använd [den här länken](https://aka.ms/BastionHost) att öppna sidan preview portal för Azure Skyddsmiljö. Navigera till den virtuella datorn som du vill ansluta till och klicka sedan på **Connect**. Den virtuella datorn ska vara en Windows-dator när du använder en RDP-anslutning.

    ![VM-anslutning](./media/bastion-connect-vm-rdp/connect.png)

1. När du klickar på Anslut visas ett sida-fält som har tre flikar – RDP och SSH Skyddsmiljö. Om Skyddsmiljö etablerades för det virtuella nätverket, är fliken Skyddsmiljö aktiv som standard. Om du inte har etablerat Skyddsmiljö för det virtuella nätverket, klickar du på länken om du vill konfigurera Skyddsmiljö. Instruktioner för konfiguration, se [konfigurera Skyddsmiljö](bastion-create-host-portal.md). Om du inte ser **Skyddsmiljö** visas, du har inte öppnat preview-portalen. Öppna portalen använder det här [förhandsversion länk](https://aka.ms/BastionHost).

    ![VM-anslutning](./media/bastion-connect-vm-rdp/bastion.png)

1. På fliken Skyddsmiljö, användarnamn och lösenord för den virtuella datorn, klicka sedan på **Connect**. RDP-anslutning till den här virtuella datorn via Skyddsmiljö öppnas direkt i Azure-portalen (via HTML5) med port 443 och Skyddsmiljö-tjänsten.

    ![VM-anslutning](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Nästa steg

Läs den [Skyddsmiljö vanliga frågor och svar](bastion-faq.md)

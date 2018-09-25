---
title: Felsök anslutningar med Azure Network Watcher - Azure-portalen | Microsoft Docs
description: Lär dig hur du använder anslutningen felsöka funktion i Azure Network Watcher med Azure portal.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: jdial
ms.openlocfilehash: 21e004e12a5111eb0e5fc7764c1e07fcb68c447d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986208"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Felsöka anslutningar med Azure Network Watcher med Azure portal

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [Azure REST-API](network-watcher-connectivity-rest.md)

Lär dig hur du använder anslutning felsöka för att kontrollera om en direkt TCP-anslutning från en virtuell dator till en viss slutpunkt kan upprättas.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har följande resurser:

* En instans av Network Watcher i regionen som du vill felsöka en anslutning.
* Virtuella datorer för att felsöka anslutningar med.

> [!IMPORTANT]
> Felsökning av anslutning kräver att den virtuella datorn som du felsöker från har den `AzureNetworkWatcherExtension` VM-tillägget installerat. Installera tillägget på en Windows-VM finns [tillägg för virtuell dator i Azure Network Watcher-Agent för Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och Linux VM finns [tillägg för virtuell dator i Azure Network Watcher-Agent för Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Tillägget krävs inte på slutpunkten för målet.

## <a name="check-connectivity-to-a-virtual-machine"></a>Kontrollera anslutningen till en virtuell dator

Det här exemplet kontrollerar anslutningen till en mål-dator via port 80.

Network Watcher och klickar på **anslutningsfelsökning**. Välj den virtuella datorn för att kontrollera anslutningen från. I den **mål** i avsnittet **väljer en virtuell dator** och välj rätt virtuell dator och port för att testa.

När du klickar på **Kontrollera**, anslutning mellan virtuella datorer på den port som angetts är markerad. I det här exemplet mål VM kan inte nås, visas en lista över hopp.

![Resultat av anslutning för en virtuell dator][1]

## <a name="check-remote-endpoint-connectivity"></a>Kontrollera fjärrslutpunkten anslutningen

Du kan kontrollera anslutningen och fördröjning till en fjärrslutpunkten, Välj den **ange manuellt** alternativknapp i den **mål** , ange URL-adressen och porten och klicka **Kontrollera**.  Det här används för fjärråtkomst slutpunkter som slutpunkter för webbplatser och lagring.

![Resultat av anslutning för en webbplats][2]

## <a name="next-steps"></a>Nästa steg

Lär dig hur du automatiserar infångade paket med virtuella datorer aviseringar genom att visa [skapar en avisering utlösta paketfångsten](network-watcher-alert-triggered-packet-capture.md)

Hitta om vissa trafik är tillåten i eller utanför din virtuella dator genom att besöka [Kontrollera Kontrollera IP-flöde](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png
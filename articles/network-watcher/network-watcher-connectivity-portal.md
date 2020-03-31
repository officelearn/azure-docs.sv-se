---
title: Felsöka anslutningar - Azure portal
titleSuffix: Azure Network Watcher
description: Lär dig hur du använder funktionen för anslutningsfelsökning i Azure Network Watcher med Azure-portalen.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: damendo
ms.openlocfilehash: e405a91b1ea541b4ed3328fdb3bf80ca82731c93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283243"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Felsöka anslutningar med Azure Network Watcher med Azure-portalen

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [Powershell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [REST-API för Azure](network-watcher-connectivity-rest.md)

Lär dig hur du använder felsöka anslutningar för att kontrollera om en direkt TCP-anslutning från en virtuell dator till en viss slutpunkt kan upprättas.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har följande resurser:

* En instans av Network Watcher i den region som du vill felsöka en anslutning.
* Virtuella datorer att felsöka anslutningar med.

> [!IMPORTANT]
> Felsöka anslutningar kräver att den virtuella `AzureNetworkWatcherExtension` datorn som du felsöker från har vm-tillägget installerat. För att installera tillägget på en Windows VM besök [Azure Network Watcher Agent virtuell dator tillägg för Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och för Linux VM besök Azure Network [Watcher Agent virtuell dator tillägg för Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Tillägget krävs inte på målslutpunkten.

## <a name="check-connectivity-to-a-virtual-machine"></a>Kontrollera anslutningen till en virtuell dator

I det här exemplet kontrolleras anslutningen till en virtuell måldator över port 80.

Navigera till nätverksbevakaren och klicka på **Felsöka anslutning**. Välj den virtuella datorn som du vill kontrollera anslutningen från. I avsnittet **Mål** väljer du **Välj en virtuell dator** och väljer rätt virtuell dator och port som ska testas.

När du klickar på **Kontrollera**kontrolleras anslutningen mellan de virtuella datorerna på den angivna porten. I exemplet kan inte nå måldatorn, en lista över hopp visas.

![Kontrollera anslutningsresultat för en virtuell dator][1]

## <a name="check-remote-endpoint-connectivity"></a>Kontrollera anslutning till fjärrslutpunkt

Om du vill kontrollera anslutningen och svarstiden till en fjärrslutpunkt väljer du knappen **Ange manuellt** i avsnittet **Mål,** matar in url:en och porten och klickar på **Kontrollera**.  Detta används för fjärrslutpunkter som webbplatser och lagringsslutpunkter.

![Kontrollera anslutningsresultat för en webbplats][2]

## <a name="next-steps"></a>Nästa steg

Lär dig hur du automatiserar paketinsamlingar med aviseringar för virtuella datorer genom att visa [Skapa en aviseringsutlöst paketfångst](network-watcher-alert-triggered-packet-capture.md)

Ta reda på om viss trafik tillåts i eller ut ur den virtuella datorn genom att besöka [Kontrollera IP-flödeskontroll](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png
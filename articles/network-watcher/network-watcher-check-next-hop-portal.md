---
title: Sök efter nästa hopp med Azure Network Watcher nexthop - Azure-portalen | Microsoft Docs
description: Den här artikeln beskriver hur du kan hitta nästa hopptyp är och ip-adressen med nästa hopp med Azure-portalen
services: network-watcher
documentationcenter: na
author: georgewallace
manager: jimdial
editor: ''
ms.assetid: 7b459dcf-4077-424e-a774-f7bfa34c5975
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 445ec8c7eeb8dd715d3778b44372d16666da7fb8
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2018
ms.locfileid: "31809975"
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-the-portal"></a>Ta reda på vilka nästa hopptyp är med nästa hopp-funktionen i Azure Nätverksbevakaren med hjälp av portalen

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Azure REST-API](network-watcher-check-next-hop-rest.md)

Nästa hopp är en funktion i Nätverksbevakaren som tillhandahåller möjligheten get nästa hopptyp och IP-adress baserat på en angiven virtuell dator. Den här funktionen är användbart för att fastställa om trafik som lämnar en virtuell dator som passerar en gateway, internet eller virtuella nätverk för att komma till sin destination.

## <a name="before-you-begin"></a>Innan du börjar

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Nätverksbevakaren](network-watcher-create.md) att skapa en Nätverksbevakaren. Det här scenariot förutsätter att det finns en resursgrupp med en giltig virtuell dator som ska användas.

## <a name="scenario"></a>Scenario

Det scenario som beskrivs i den här artikeln använder nästa hopp för att ta reda på nästa hopptyp och IP-adress för en resurs. Läs mer om nästa hopp [nästa hopp översikt](network-watcher-next-hop-overview.md).

I det här scenariot kommer du att:

* Hämta nästa hopp från en virtuell dator.

## <a name="get-next-hop"></a>Hämta nästa hopp

### <a name="step-1"></a>Steg 1

Gå till din Nätverksbevakaren resurs i Azure-portalen.

### <a name="step-2"></a>Steg 2

Klicka på **nästa hopp** i navigeringsfönstret, Välj den virtuella datorn och nätverksgränssnittet fylla källa och mål-IP, och klicka på den **nästa hopp** knappen.

> [!NOTE]
> Nästa hopp kräver att den Virtuella datorresursen har allokerats för att köras.

![Hämta nästa hopp-översikt][1]

### <a name="step-3"></a>Steg 3

När aktiviteten har slutförts tillhandahålls resultaten. IP-adressen och typ av enhet nästa hopp är visas. Följande tabell visar de tillgängliga returnerade värdena i portalen.

**Nästa Hopptyp**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* Ingen

Om en anpassad väg användes för att dirigera trafiken, visas användardefinierad väg (UDR) samt med resultat.

![Nästa hopp-resultat][2]

## <a name="next-steps"></a>Nästa steg

Lär dig hur du granskar din grupp för nätverkssäkerhet via programmering genom att besöka [NSG granskning med Nätverksbevakaren](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-check-next-hop-portal/figure1.png
[2]: ./media/network-watcher-check-next-hop-portal/figure2.png















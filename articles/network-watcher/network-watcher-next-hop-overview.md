---
title: "Introduktion till nästa hopp i Azure-Nätverksbevakaren | Microsoft Docs"
description: "Den här sidan innehåller en översikt över Nätverksbevakaren nästa hopp-funktion"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: bb2ca0486b3b3d27a77b70927cb3cbfbeac12c7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-next-hop-in-azure-network-watcher"></a>Introduktion till nästa hopp i Azure Nätverksbevakaren

Trafik från en virtuell dator som skickas till ett mål som är baserat på de effektiva vägar som är associerad med ett nätverkskort. Nästa hopp hämtar nästa hopptyp och IP-adressen för ett paket från en specifik virtuell dator och nätverkskort. Detta hjälper dig för att avgöra om paketet omdirigeras till målet eller är trafik som svart holed. En felaktig konfiguration av vägar av användaren, där en trafik dirigeras till en lokal plats eller en virtuell installation, kan leda till problem med nätverksanslutningen. Nästa hopp returnerar också routningstabellen som är associerad med nästa hopp. När du frågar ett nexthop om vägen definieras som en användardefinierad väg, returneras den rutten. Annars returnerar nästa hopp ”Systemväg”.

![Nästa hopp-översikt][1]

Följande är en lista över nästa hopptyper som kan returneras när du frågar nästa hopp.

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* Ingen

### <a name="next-steps"></a>Nästa steg

Lär dig mer om nästa hopp att söka efter problem med nätverksanslutningen genom att besöka [kontrollera nästa hopp på en virtuell dator](network-watcher-check-next-hop-portal.md)

<!--Image references-->
[1]: ./media/network-watcher-next-hop-overview/figure1.png














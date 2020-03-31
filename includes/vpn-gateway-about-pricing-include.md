---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/08/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8f8d366961049deb3eda193718ccb553aac930e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67666390"
---
Du betalar för två saker: beräkningskostnaderna per timme för den virtuella nätverksgatewayen och för utgående dataöverföring från den virtuella nätverksgatewayen. Information om priser finns på sidan [Priser](https://azure.microsoft.com/pricing/details/vpn-gateway). För äldre gateway SKU-priser finns på [prissidan för ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) och bläddrar till avsnittet **Virtuella nätverksgateways.**

**Beräkningskostnader för virtuell nätverksgateway**<br>Varje virtuell nätverksgateway har ett timpris för beräkningar. Priset grundas på den gateway-SKU du anger när du skapar en virtuell nätverksgateway. Kostnaden är för själva gatewayen och läggs till utöver den dataöverföring som går via gatewayen. Kostnaden för en aktiv-aktiv-konfiguration är densamma som en aktiv-passiv.

**Kostnader för överföring av data**<br>Kostnaderna för överföring av data beräknas baserat på utgående trafik från den virtuella nätverksgatewayen (källan).

* Om du skickar trafik till din lokala VPN-enhet debiteras du priset för utgående dataöverföring via Internet.
* Om du skickar trafik mellan virtuella nätverk i olika regioner baseras priset på regionen.
* Om du skickar trafik endast mellan virtuella nätverk i samma region tillkommer inga kostnader för data. Trafik mellan VNets i samma region är kostnadsfri.

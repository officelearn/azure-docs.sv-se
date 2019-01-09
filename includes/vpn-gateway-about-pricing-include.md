---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ae36adc78d8c87d85c64fd61cb3a50dfcae60b85
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "53995792"
---
Du betalar för två saker: beräkningskostnaderna per timme för den virtuella nätverksgatewayen och för utgående dataöverföring från den virtuella nätverksgatewayen. Information om priser finns på sidan [Priser](https://azure.microsoft.com/pricing/details/vpn-gateway).

**Beräkningskostnader för virtuell nätverksgateway**<br>Varje virtuell nätverksgateway har ett timpris för beräkningar. Priset grundas på den gateway-SKU du anger när du skapar en virtuell nätverksgateway. Kostnaden är för själva gatewayen och läggs till utöver den dataöverföring som går via gatewayen. Kostnaden för en aktiv-aktiv-konfiguration är densamma som en aktiv-passiv.

**Kostnader för överföring av data**<br>Kostnaderna för överföring av data beräknas baserat på utgående trafik från den virtuella nätverksgatewayen (källan).

* Om du skickar trafik till din lokala VPN-enhet debiteras du priset för utgående dataöverföring via Internet.
* Om du skickar trafik mellan virtuella nätverk i olika regioner baseras priset på regionen.
* Om du skickar trafik endast mellan virtuella nätverk i samma region tillkommer inga kostnader för data. Trafik mellan VNets i samma region är kostnadsfri.
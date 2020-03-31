---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2ed141847f923a847443d2293e850519357cdae2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187234"
---
Från och med 1 juli 2018 tas stödet för TLS 1.0 och 1.1 bort från Azure VPN Gateway. VPN Gateway kommer endast att stödja TLS 1.2. Endast point-to-site-anslutningar påverkas. anslutningar från plats till plats påverkas inte. Om du använder TLS för virtuella vpn på plats på Windows 10-klienter behöver du inte vidta några åtgärder. Om du använder TLS för point-to-site-anslutningar på Windows 7- och Windows 8-klienter läser du vanliga frågor och [svar om VPN Gateway](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S) för uppdateringsinstruktioner.
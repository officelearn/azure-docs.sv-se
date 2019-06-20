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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187234"
---
Från och med 1 juli 2018 tas stödet för TLS 1.0 och 1.1 bort från Azure VPN Gateway. VPN Gateway kommer endast att stödja TLS 1.2. Punkt-till-plats-anslutningar påverkas; plats-till-plats-anslutningar påverkas inte. Om du använder TLS för punkt-till-plats-VPN på Windows 10-klienter, behöver du inte vidta några åtgärder. Om du använder TLS för punkt-till-plats-anslutningar på Windows 7 och Windows 8-klienter, finns i den [VPN Gateway vanliga frågor och svar](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S) update-instruktioner.
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
ms.openlocfilehash: 0377548a3b026657ae3282801523b1c0c6731265
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236713"
---
Från 1 juli 2018, tas support bort för TLS 1.0- och 1.1 från Azure VPN-Gateway. VPN-Gateway stöder endast TLS 1.2. Punkt-till-plats-anslutningar påverkas; plats-till-platsanslutningar påverkas inte. Om du använder TLS för punkt-till-plats VPN-anslutningar på Windows 10-klienter, behöver du inte vidta några åtgärder. Om du använder TLS för punkt-till-plats-anslutningar på Windows 7 och Windows 8-klienter, finns det [VPN-Gateway och-svar](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S) update-instruktioner.
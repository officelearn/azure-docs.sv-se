---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0e12ad66c635632b29b70000b6e227ddcbb5357b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501925"
---
Användardefinierade vägar med ett 0.0.0.0/0-mål och NSG:er på GatewaySubnet **stöds inte**. Gateways som skapats med den här konfigurationen blockeras från att skapas. Gateways kräver åtkomst till hanteringskontrollanterna för att fungera korrekt. [BGP-dirigeradrutspridning](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol]) bör ställas in på "Aktiverad" på GatewaySubnet för att säkerställa tillgängligheten för gatewayen. Om detta är inställt på inaktiverat kommer gatewayen inte att fungera.

---
title: inkludera fil
description: inkludera fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "81274931"
---
Användardefinierade vägar med målet 0.0.0.0/0 och NSG: er på GatewaySubnet **stöds inte**. Gatewayer som har skapats med den här konfigurationen blockeras från att skapas. Gatewayer kräver åtkomst till hanterings styrenheterna för att fungera korrekt. [Instabilitetsdämpning av BGP-vägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) ska ställas in på "aktive rad" på GatewaySubnet för att säkerställa att gatewayen är tillgänglig. Om detta är inaktiverat kommer gatewayen inte att fungera.

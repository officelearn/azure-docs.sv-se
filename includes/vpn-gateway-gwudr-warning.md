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
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81274931"
---
Användardefinierade vägar med ett 0.0.0.0/0-mål och NSG:er på GatewaySubnet **stöds inte**. Gateways som skapats med den här konfigurationen blockeras från att skapas. Gateways kräver åtkomst till hanteringskontrollanterna för att fungera korrekt. [BGP-dirigeradrutspridning](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) bör ställas in på "Aktiverad" på GatewaySubnet för att säkerställa tillgängligheten för gatewayen. Om detta är inställt på inaktiverat kommer gatewayen inte att fungera.

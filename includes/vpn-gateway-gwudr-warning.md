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
ms.openlocfilehash: a852807ab685e85b76d26e5b39c99a32f645bbd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "71838170"
---
Användardefinierade vägar med ett 0.0.0.0/0-mål och NSG:er på GatewaySubnet **stöds inte**. Gateways som skapats med den här konfigurationen blockeras från att skapas. Gateways kräver åtkomst till hanteringskontrollanterna för att fungera korrekt.

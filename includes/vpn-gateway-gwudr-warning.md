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
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838170"
---
Användardefinierade vägar med målet 0.0.0.0/0 och NSG: er på GatewaySubnet **stöds inte**. Gatewayer som har skapats med den här konfigurationen blockeras från att skapas. Gatewayer kräver åtkomst till hanterings styrenheterna för att fungera korrekt.

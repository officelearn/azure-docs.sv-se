---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 43e381bd26dadbea5ef5bfb002e51465e180a097
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187229"
---
Koppla inte en routingtabell som innehåller en väg med 0.0.0.0/0 till gateway-undernätet som mål. Detta förhindrar att gatewayen från att fungera korrekt.
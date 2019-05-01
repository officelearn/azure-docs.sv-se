---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 06/04/2018
ms.date: 12/24/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 43e381bd26dadbea5ef5bfb002e51465e180a097
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64859124"
---
Koppla inte en routingtabell som innehåller en väg med 0.0.0.0/0 till gateway-undernätet som mål. Detta förhindrar att gatewayen från att fungera korrekt.

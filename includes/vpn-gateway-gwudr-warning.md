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
ms.openlocfilehash: 244d6be318662be794cac58aaa8350b433b6cb37
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825467"
---
Koppla inte en routingtabell som innehåller en väg med 0.0.0.0/0 till gateway-undernät som mål. Detta förhindrar att gatewayen från att fungera korrekt.
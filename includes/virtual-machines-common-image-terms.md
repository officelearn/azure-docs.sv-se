---
title: ta med fil
description: ta med fil
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 98d765e2f6909f00f8dfe76d06aef017aad67adf
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174997"
---
## <a name="terminology"></a>Terminologi

En Marketplace-avbildning i Azure har följande attribut:

* **Utgivare**: Organisationen som skapade avbildningen. Exempel: Canonical, MicrosoftWindowsServer
* **Erbjudande**: Namnet på en grupp relaterade bilder som skapats av en utgivare. Exempel: UbuntuServer, WindowsServer
* **SKU**: En instans av ett erbjudande, till exempel en större version av en distribution. Exempel: 18,04-LTS, 2019-datacenter
* **Version**: Versions numret för en avbildnings-SKU. 

För att identifiera en Marketplace-avbildning när du distribuerar en virtuell dator program mässigt, anger du dessa värden individuellt som parametrar. Vissa verktyg accepterar en bild- *urn*som kombinerar dessa värden, avgränsade med kolon (:) jokerteck *Utgivare*:*erbjudande*:*SKU*:*version*. I en URN kan du ersätta versions numret med "senaste", vilket väljer den senaste versionen av avbildningen. 

Om avbildnings utgivaren ger ytterligare licens-och inköps villkor måste du acceptera dessa villkor och aktivera programmerings distribution. Du måste också ange *inköps plan* parametrar när du distribuerar en virtuell dator program mässigt. Se [distribuera en avbildning med Marketplace-villkor](#deploy-an-image-with-marketplace-terms).

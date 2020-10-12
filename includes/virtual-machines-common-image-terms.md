---
title: inkludera fil
description: inkludera fil
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 98d765e2f6909f00f8dfe76d06aef017aad67adf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "71174997"
---
## <a name="terminology"></a>Terminologi

En Marketplace-avbildning i Azure har följande attribut:

* **Utgivare**: den organisation som skapade avbildningen. Exempel: Canonical, Microsoft Windows Server
* **Erbjudande**: namnet på en grupp relaterade bilder som skapats av en utgivare. Exempel: UbuntuServer, Windows Server
* **SKU**: en instans av ett erbjudande, till exempel en större version av en distribution. Exempel: 18,04-LTS, 2019-datacenter
* **Version**: versions numret för en avbildnings-SKU. 

För att identifiera en Marketplace-avbildning när du distribuerar en virtuell dator program mässigt, anger du dessa värden individuellt som parametrar. Vissa verktyg accepterar en bild- *urn*som kombinerar dessa värden, avgränsade med kolon (:) Character: *Publisher*:*erbjudande*:*SKU*:*version*. I en URN kan du ersätta versions numret med "senaste", vilket väljer den senaste versionen av avbildningen. 

Om avbildnings utgivaren ger ytterligare licens-och inköps villkor måste du acceptera dessa villkor och aktivera programmerings distribution. Du måste också ange *inköps plan* parametrar när du distribuerar en virtuell dator program mässigt. Se [distribuera en avbildning med Marketplace-villkor](#deploy-an-image-with-marketplace-terms).

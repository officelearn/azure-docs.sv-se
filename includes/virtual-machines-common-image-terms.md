---
title: ta med fil
description: ta med fil
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 50d71a3967e61e5d531f4bfeae3582b56230a0d2
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2019
ms.locfileid: "56825362"
---
## <a name="terminology"></a>Terminologi

En Marketplace-avbildning i Azure har följande attribut:

* **Publisher**: Organisationen som skapade avbildningen. Exempel: Canonical, MicrosoftWindowsServer
* **Erbjuder**: Namnet på en grupp av relaterade bilder som skapats av en utgivare. Exempel: UbuntuServer, WindowsServer
* **SKU**: En instans av ett erbjudande, till exempel en större version av en distribution. Exempel: 18.04-LTS, 2019 Datacenter
* **Version**: Det lägre versionsnumret för en bild-SKU. 

Du kan identifiera en Marketplace-avbildning när du distribuerar en virtuell dator via programmering genom att ange dessa värden individuellt som parametrar. Vissa verktyg accepterar en bild *URN*, som kombinerar dessa värden, avgränsade med kolon (:): *Publisher*:*Offer*:*Sku*:*Version*. I en URN, kan du ersätta det lägre versionsnumret med ”senaste”, vilket väljer den senaste versionen av avbildningen. 

Om avbildningens utgivare ger ytterligare en licens och köp villkor, måste du godkänna dessa villkor och aktivera programdistribution. Du måste också ange *köper plan* parametrar när du distribuerar en virtuell dator via programmering. Se [distribuera en avbildning med Marketplace-villkor](#deploy-an-image-with-marketplace-terms).

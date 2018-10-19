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
ms.openlocfilehash: 6c7a9857f6d6d57dc9e314bcb1ef848a326b7ed2
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49437104"
---
## <a name="terminology"></a>Terminologi

En Marketplace-avbildning i Azure har följande attribut:

* **Publisher**: den organisation som skapade avbildningen. Exempel: Canonical, Microsoft Windows Server
* **Erbjuder**: namnet på en grupp av relaterade bilder som skapats av en utgivare. Exempel: Ubuntu Server, WindowsServer
* **SKU**: en instans av ett erbjudande, till exempel en större version av en distributionsplats. Exempel: 16.04-LTS, 2016-Datacenter
* **Version**: det lägre versionsnumret för en bild-SKU. 

Du kan identifiera en Marketplace-avbildning när du distribuerar en virtuell dator via programmering genom att ange dessa värden individuellt som parametrar. Vissa verktyg accepterar en bild *URN*, som kombinerar dessa värden, avgränsade med kolon (:): *Publisher*:*erbjuder*:*Sku*: *Version*. I en URN, kan du ersätta det lägre versionsnumret med ”senaste”, vilket väljer den senaste versionen av avbildningen. 

Om avbildningens utgivare ger ytterligare en licens och köp villkor, måste du godkänna dessa villkor och aktivera programdistribution. Du måste också ange *köper plan* parametrar när du distribuerar en virtuell dator via programmering. Se [distribuera en avbildning med Marketplace-villkor](#deploy-an-image-with-marketplace-terms).
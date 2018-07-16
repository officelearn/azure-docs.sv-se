---
title: ta med fil
description: ta med fil
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 02/28/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 47ef014f31c628fed9d7b2b005e67e8138d3e7e9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38941826"
---
## <a name="terminology"></a>Terminologi

En Marketplace-avbildning i Azure har följande attribut:

* **Publisher** -den organisation som skapade avbildningen. Exempel: Canonical, Microsoft Windows Server
* **Erbjuder** -namnet på en grupp av relaterade bilder som skapats av en utgivare. Exempel: Ubuntu Server, Windows Server
* **SKU** – en instans av ett erbjudande, till exempel en större version av en distributionsplats. Exempel: 16.04-LTS, 2016-Datacenter
* **Version** -versionsnumret för en bild-SKU. 

Ange dessa värden individuellt som parametrar för att identifiera en Marketplace-avbildning när du distribuerar en virtuell dator via programmering, eller några verktyg accepterar avbildningen *URN*. URN kombinerar dessa värden, avgränsade med kolon (:): *Publisher*:*erbjuder*:*Sku*:*Version*. I en URN, kan du ersätta det lägre versionsnumret med ”senaste”, vilket väljer den senaste versionen av avbildningen. 

Om avbildningens utgivare ger ytterligare en licens och köp villkor, måste du godkänna dessa villkor och aktivera programdistribution. Du måste också ange *köper plan* parametrar när du distribuerar en virtuell dator via programmering. Se [distribuera en avbildning med Marketplace-villkor](#deploy-an-image-with-marketplace-terms).
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
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
ms.locfileid: "29743383"
---
## <a name="terminology"></a>Terminologi

Marketplace-avbildning i Azure har följande attribut:

* **Publisher** -organisationen som skapade avbildningen. Exempel: Kanoniska, Microsoft Windows Server
* **Erbjuder** -namnet på en grupp av relaterade bilder som har skapats av en utgivare. Exempel: Ubuntu Server, Windows Server
* **SKU** - en instans av ett erbjudande, till exempel en högre version av en distributionsplats. Exempel: 16.04-LTS, 2016 Datacenter
* **Version** -versionsnumret för en bild SKU. 

Du kan identifiera en Marketplace-avbildning när du distribuerar en virtuell dator via programmering genom ange dessa värden individuellt som parametrar eller vissa verktyg acceptera bilden *URN*. URN kombinerar dessa värden, avgränsade med kolon (:): *Publisher*:*erbjuder*:*Sku*:*Version*. I en URN kan du ersätta versionsnumret med ”senaste”, som väljs den senaste versionen av avbildningen. 

Om avbildningens utgivare ger ytterligare en licens och inköp villkor, måste du acceptera dessa villkor och aktiverar programdistribution. Du måste också ange *köper plan* parametrar när du distribuerar en virtuell dator via programmering. Se [distribuera en avbildning med Marketplace villkor](#deploy-an-image-with-marketplace-terms).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "71174997"
---
## <a name="terminology"></a>Terminologi

En Marketplace-avbildning i Azure har följande attribut:

* **Utgivare**: Organisationen som skapade avbildningen. Exempel: Canonical, Microsoft Windows Server
* **Erbjudande**: Namnet på en grupp relaterade bilder som skapats av en utgivare. Exempel: UbuntuServer, WindowsServer
* **SKU**: En instans av ett erbjudande, till exempel en större version av en distribution. Exempel: 18.04-LTS, 2019-Datacenter
* **Version**: Versionsnumret för en bild SKU. 

Om du vill identifiera en Marketplace-avbildning när du distribuerar en virtuell dator programmässigt anger du dessa värden individuellt som parametrar. Vissa verktyg accepterar en *bild-URN*, som kombinerar dessa värden, åtskilda av kolon (:) karaktär: *Utgivare:**Erbjudande:**Sku*:*Version*. I ett URN kan du ersätta versionsnumret med "senaste", som väljer den senaste versionen av bilden. 

Om avbildningsutgivaren tillhandahåller ytterligare licens- och inköpsvillkor måste du acceptera dessa villkor och aktivera programmatisk distribution. Du måste också ange parametrar för *inköpsplan* när du distribuerar en virtuell dator programmässigt. Se [Distribuera en avbildning med Marketplace-termer](#deploy-an-image-with-marketplace-terms).

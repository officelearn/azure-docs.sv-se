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
ms.openlocfilehash: a3c5290eb0179fe5842c495c2e08f22580d02bda
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38944168"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Distribuera en avbildning med Marketplace-villkor

Vissa VM-avbildningar på Azure Marketplace har ytterligare en licens och köp villkor att du måste acceptera innan du kan distribuera dem via programmering.  

Om du vill distribuera en virtuell dator från en sådan avbildning måste du acceptera villkoren för en bilds och aktivera programdistribution. Du behöver bara göra detta en gång i din prenumeration. Sedan varje gång som du distribuerar en virtuell dator via programmering från avbildningen, du måste också ange *köper plan* parametrar.

I de följande avsnitten visar hur du:

* Ta reda på om en Marketplace-avbildning har ytterligare licensvillkoren 
* Acceptera villkoren programmässigt
* Ange köp plan parametrar när du distribuerar en virtuell dator via programmering


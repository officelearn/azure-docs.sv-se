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
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
ms.locfileid: "29743386"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Distribuera en avbildning med Marketplace-villkor

Vissa VM-avbildningar i Azure Marketplace har ytterligare licens och inköp villkor måste du godkänna innan du distribuerar via programmering.  

Om du vill distribuera en virtuell dator från en sådan avbildning måste du acceptera det bild och aktivera programdistribution. Du behöver bara göra detta en gång i din prenumeration. Sedan varje gång du distribuerar en virtuell dator via programmering från avbildningen, du måste också ange *köper plan* parametrar.

I de följande avsnitten visar hur du:

* Ta reda på om en Marketplace-avbildning har ytterligare licensvillkoren 
* Acceptera villkoren programmässigt
* Ange inköp plan parametrar när du distribuerar en virtuell dator via programmering


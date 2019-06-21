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
ms.openlocfilehash: 4d2235eaea457c89d01a632afa5dd5a862bec344
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187335"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Distribuera en avbildning med Marketplace-villkor

Vissa VM-avbildningar på Azure Marketplace har ytterligare en licens och köp villkor att du måste acceptera innan du kan distribuera dem via programmering.  

Om du vill distribuera en virtuell dator från en sådan bild, måste både acceptera bildens allmänna och aktivera programdistribution. Du behöver bara göra detta en gång per prenumeration. Därefter varje gång du distribuerar en virtuell dator via programmering från den avbildning som du måste också ange *köper plan* parametrar.

I de följande avsnitten visar hur du:

* Ta reda på om en Marketplace-avbildning har ytterligare licensvillkoren 
* Acceptera villkoren programmässigt
* Ange köp plan parametrar när du distribuerar en virtuell dator via programmering


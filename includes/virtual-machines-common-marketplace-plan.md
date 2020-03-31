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
ms.openlocfilehash: 8e0e549f88caf4a541642bab77faf54b5e536b29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "71174996"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Distribuera en avbildning med Marketplace-termer

Vissa VM-avbildningar på Azure Marketplace har ytterligare licens- och inköpsvillkor som du måste acceptera innan du kan distribuera dem programmässigt.  

Om du vill distribuera en virtuell dator från en sådan avbildning måste du både acceptera avbildningens termer och aktivera programmatisk distribution. Du behöver bara göra detta en gång per prenumeration. Därefter måste du också ange parametrar för *inköpsplan* varje gång du distribuerar en virtuell dator programmässigt från avbildningen.

I följande avsnitt visas hur du:

* Ta reda på om en Marketplace-avbildning har ytterligare licensvillkor 
* Acceptera termerna programmässigt
* Ange parametrar för inköpsplan när du distribuerar en virtuell dator programmässigt


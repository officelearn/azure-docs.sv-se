---
title: Samordna virtuella Windows Azure-datorer | Microsoft Docs
description: Lär dig mer om hur samplaceringen av Azure VM-resurser kan förbättra svars tiden.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: cynthn
ms.openlocfilehash: ddffcd1230048a0b1e47076270ac24a607d53a7e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103242"
---
# <a name="co-locate-resource-for-improved-latency"></a>Samplacera resursen för förbättrad svars tid

När du distribuerar ditt program i Azure skapar spridnings instanser över regioner eller tillgänglighets zoner nätverks fördröjning, vilket kan påverka programmets övergripande prestanda. 


## <a name="preview-proximity-placement-groups"></a>Förhandsversion: Placerings grupper för närhet 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Nästa steg

Distribuera en virtuell dator till en [närhets placerings grupp](proximity-placement-groups.md) med hjälp av Azure PowerShell.


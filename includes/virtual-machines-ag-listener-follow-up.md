---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187290"
---
När du har skapat tillgänglighets gruppens lyssnare kan du behöva justera kluster parametrarna RegisterAllProvidersIP och HostRecordTTL för lyssnar resursen. Dessa parametrar kan minska åter anslutnings tiden efter en redundansväxling, vilket kan förhindra anslutningstimeout. Mer information om dessa parametrar, samt exempel kod, finns i [skapa eller konfigurera en lyssnare för tillgänglighets grupp](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).


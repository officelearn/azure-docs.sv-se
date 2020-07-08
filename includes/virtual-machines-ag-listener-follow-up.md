---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67187290"
---
När du har skapat tillgänglighets gruppens lyssnare kan du behöva justera kluster parametrarna RegisterAllProvidersIP och HostRecordTTL för lyssnar resursen. Dessa parametrar kan minska åter anslutnings tiden efter en redundansväxling, vilket kan förhindra anslutningstimeout. Mer information om dessa parametrar, samt exempel kod, finns i [skapa eller konfigurera en lyssnare för tillgänglighets grupp](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).


---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165448"
---
När du har skapat tillgänglighetsgruppslyssnaren kan det vara nödvändigt att justera parametrarna RegisterAllProvidersIP och HostRecordTTL kluster för lyssnare resursen. Dessa parametrar kan minska återanslutning tid efter en redundansväxling, vilket kan medföra att timeout. Mer information om dessa parametrar, samt exempelkod finns i [skapa eller konfigurera en tillgänglighetsgruppslyssnare](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).


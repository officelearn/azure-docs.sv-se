---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227201"
---
När du har skapat tillgänglighetsgruppslyssnaren kan det vara nödvändigt att justera parametrarna RegisterAllProvidersIP och HostRecordTTL kluster för lyssnare resursen. Dessa parametrar kan minska återanslutning tid efter en redundansväxling, vilket kan medföra att timeout. Mer information om dessa parametrar, samt exempelkod finns i [skapa eller konfigurera en tillgänglighetsgruppslyssnare](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).


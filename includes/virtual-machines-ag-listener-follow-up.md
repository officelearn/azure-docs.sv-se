---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097704"
---
När du har skapat tillgänglighetsgruppslyssnaren kan det vara nödvändigt att justera parametrarna RegisterAllProvidersIP och HostRecordTTL kluster för lyssnare resursen. Dessa parametrar kan minska återanslutning tid efter en redundansväxling, vilket kan medföra att timeout. Mer information om dessa parametrar, samt exempelkod finns i [skapa eller konfigurera en tillgänglighetsgruppslyssnare](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

<!-- Update_Description: update meta properties -->
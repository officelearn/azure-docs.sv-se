---
title: Introduktion till IP-flöde verifiera i Azure Network Watcher | Microsoft Docs
description: Den här sidan ger en översikt över funktionen för att verifiera Network Watcher IP-flöde
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: damendo
ms.openlocfilehash: 69aca5e0901a0da8aa98fe310ac220898bf650b2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76845014"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Introduktion till IP-flöde verifiera i Azure Network Watcher

Kontrol lera IP-flöde om ett paket tillåts eller nekas till eller från en virtuell dator. Informationen består av riktning, protokoll, lokal IP-adress, fjärr-IP, lokal port och Fjärrport. Om paketet nekas av en säkerhets grupp returneras namnet på den regel som nekade paketet. När alla käll-eller mål-IP-adresser kan väljas, verifierar IP-flödet att administratörer snabbt kan diagnostisera anslutnings problem från eller till Internet och från eller till den lokala miljön.

Kontrol lera IP-flöde kontrollerar reglerna för alla nätverks säkerhets grupper (NSG: er) som tillämpas på nätverks gränssnittet, till exempel ett undernät eller ett nätverkskort för virtuell dator. Trafikflödet verifieras sedan baserat på de konfigurerade inställningarna till eller från det nätverks gränssnittet. Verifiera IP-flöde är användbart för att bekräfta om en regel i en nätverks säkerhets grupp blockerar ingångs-eller utgående trafik till eller från en virtuell dator.

En instans av Network Watcher måste skapas i alla regioner som du planerar att köra kontrol lera IP-flödet. Network Watcher är en regional tjänst och kan bara köras mot resurser i samma region. Den instansen som används påverkar inte resultatet av IP-flödet, eftersom alla vägar som är kopplade till NÄTVERKSKORTet eller under nätet fortfarande returneras.

![1][1]

## <a name="next-steps"></a>Nästa steg

Besök följande artikel för att lära dig om ett paket tillåts eller nekas för en speciell virtuell dator via portalen. [Kontrol lera om trafik tillåts på en virtuell dator med IP-flöde verifiera med hjälp av portalen](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png


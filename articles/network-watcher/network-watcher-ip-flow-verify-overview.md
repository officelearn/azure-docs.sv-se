---
title: Introduktion till IP-flödeskontroll i Azure Network Watcher | Microsoft-dokument
description: Den här sidan ger en översikt över funktionen för att verifiera nätverksbevakningen av IP-flöde
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845014"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Introduktion till IP-flödesverifiering i Azure Network Watcher

IP-flöde verifierar om ett paket tillåts eller nekas eller från en virtuell dator. Informationen består av riktning, protokoll, lokal IP, fjärr-IP, lokal port och fjärrport. Om paketet nekas av en säkerhetsgrupp returneras namnet på regeln som nekade paketet. Även om alla käll- eller mål-IP-rättigheter kan väljas, hjälper IP-flödesverifiering administratörer att snabbt diagnostisera anslutningsproblem från eller till Internet och från eller till den lokala miljön.

IP-flöde verifiera tittar på reglerna för alla NSG-grupper (Network Security Groups) som tillämpas på nätverksgränssnittet, till exempel ett undernät eller ett nätverkskort för virtuella datorer. Trafikflödet verifieras sedan baserat på de konfigurerade inställningarna till eller från det nätverksgränssnittet. IP-flödesverifiering är användbart för att bekräfta om en regel i en nätverkssäkerhetsgrupp blockerar inkommande eller utgående trafik till eller från en virtuell dator.

En instans av Network Watcher måste skapas i alla regioner som du planerar att köra IP-flöde verifiera. Network Watcher är en regional tjänst och kan endast kördes mot resurser i samma region. Den instans som används påverkar inte resultatet av IP-flödeskontroll, eftersom alla vägar som är associerade med nätverkskortet eller undernätet fortfarande returneras.

![1][1]

## <a name="next-steps"></a>Nästa steg

Besök följande artikel om du vill veta om ett paket tillåts eller nekas för en viss virtuell dator via portalen. [Kontrollera om trafik tillåts på en virtuell dator med IP-flöde Verifiera med hjälp av portalen](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png


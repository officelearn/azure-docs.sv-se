---
title: Introduktion till IP-flöde verifiera i Azure Network Watcher | Microsoft Docs
description: Den här sidan innehåller en översikt av Network Watcher IP-flödesverifieringen i funktionen
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: kumud
ms.openlocfilehash: 5c34fd2b6d354f594ed153647c1bed700566fad6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64709588"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Introduktion till IP-flöde verifiera i Azure Network Watcher

IP-flödesverifieringen kontrollerar om ett paket tillåts eller nekas till eller från en virtuell dator. Informationen består av riktning, protokoll, lokal IP-, fjärr-IP, lokal port och portar. Om paketet nekas av en säkerhetsgrupp, returneras namnet på regeln som nekade paketet. Medan du kan välja alla käll- eller mål-IP, IP-flödesverifieringen hjälper administratörer snabbt diagnostisera anslutningsproblem från eller till internet och från eller till en lokal miljö.

Kontrollera IP-flöde för att söka efter reglerna för alla Nätverkssäkerhetsgrupper (NSG) som gäller för nätverksgränssnittet, till exempel ett undernät eller virtuella nätverkskort. Flödet i nätverkstrafiken verifieras sedan baserat på de konfigurerade inställningarna till eller från nätverksgränssnittet. Kontrollera IP-flöde är användbar för att bekräfta om en regel i en Nätverkssäkerhetsgrupp blockerar inkommande eller utgående trafik till eller från en virtuell dator.

En instans av Network Watcher måste skapas i alla regioner som du planerar att köra IP-flöde kontrollera. Network Watcher är en regional tjänst och kan bara kördes mot resurser i samma region. Den instans som används inte påverka resultatet för IP-Adressen Kontrollera flöde, så returneras fortfarande någon väg som är associerade med nätverkskort eller undernät.

![1][1]

## <a name="next-steps"></a>Nästa steg

Gå till följande artikel om du vill veta om ett paket tillåts eller nekas för en specifik virtuell dator via portalen. [Kontrollera om trafik tillåts på en virtuell dator med IP-Flow Kontrollera med hjälp av portalen](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png


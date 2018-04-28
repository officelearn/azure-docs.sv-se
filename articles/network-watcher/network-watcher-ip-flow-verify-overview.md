---
title: Introduktion till IP-flöde verifiera i Azure-Nätverksbevakaren | Microsoft Docs
description: Den här sidan innehåller en översikt av nätverket Watcher IP flödet verifieringsfunktioner
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 8a59047a586f3d7ad7c1f29b218396bd688caafd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Introduktion till IP-flöde verifiera i Azure Nätverksbevakaren

IP-flöde kontrollera kontrollerar om ett paket tillåts eller nekas till eller från en virtuell dator. Informationen består av riktning, protokoll, lokal IP-, fjärr-IP, lokal port och Fjärrport. Om paketet nekas av en säkerhetsgrupp, returneras namnet på regeln som nekats paketet. Medan du kan välja alla käll- eller IP-adresser, IP-flöde Kontrollera hjälper administratörer snabbt diagnostisera problem med nätverksanslutningen från eller till internet och från eller till den lokala miljön.

IP-flöde Kontrollera riktar sig till ett nätverksgränssnitt för en virtuell dator. Trafikflöde verifieras sedan baserat på de konfigurerade inställningarna till eller från nätverksgränssnittet. IP-flöde Kontrollera är användbar för att bekräfta om en regel i en Nätverkssäkerhetsgrupp blockerar inkommande trafik eller utgående trafik till eller från en virtuell dator.

Kontrollera en instans av Nätverksbevakaren måste skapas i alla regioner som du tänker köra IP-flödet. Nätverksbevakaren är en tjänst som regional och kan endast kördes mot resurser i samma region. Den instans som används inte påverka resultatet av IP-flöde kontrollera, som returneras fortfarande vägen som är kopplade till nätverkskortet.

![1][1]

## <a name="next-steps"></a>Nästa steg

Besök följande artikel om du vill veta om ett paket tillåts eller nekas för en specifik virtuell dator via portalen. [Kontrollera om trafik tillåts på en virtuell dator med IP-flöda Kontrollera med hjälp av portalen](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png













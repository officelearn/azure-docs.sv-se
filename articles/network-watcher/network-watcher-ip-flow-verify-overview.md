---
title: "Introduktion till IP-flöde verifiera i Azure-Nätverksbevakaren | Microsoft Docs"
description: "Den här sidan innehåller en översikt av nätverket Watcher IP flödet verifieringsfunktioner"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b2ad45e76320c59d18dce7b39166679801b4170a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Introduktion till IP-flöde verifiera i Azure Nätverksbevakaren

IP-flöde kontrollera kontrollerar om ett paket tillåts eller nekas till eller från en virtuell dator baserat på 5-tuppel information. Den här informationen består av riktning, protokoll, lokal IP-, fjärr-IP, lokal port och Fjärrport. Om paketet nekas av en säkerhetsgrupp, returneras namnet på regeln som nekats paketet. När alla käll- eller IP-adresser kan väljas hjälper administratörer att snabbt diagnostisera problem med nätverksanslutningen från eller till internet och från eller till den lokala miljön i den här funktionen.

IP-flöde Kontrollera riktar sig till ett nätverksgränssnitt för en virtuell dator. Trafikflöde verifieras sedan baserat på de konfigurerade inställningarna till eller från nätverksgränssnittet. Den här funktionen är användbar för att bekräfta om en regel i en Nätverkssäkerhetsgrupp blockerar inkommande trafik eller utgående trafik till eller från en virtuell dator.

Kontrollera en instans av Nätverksbevakaren måste skapas i alla regioner som du tänker köra IP-flödet. Nätverksbevakaren är en tjänst som regional och kan endast kördes mot resurser i samma region. Den här har inte påverka resultatet av IP-flöde Kontrollera som returneras fortfarande vägen som är kopplade till nätverkskortet.

![1][1]

## <a name="next-steps"></a>Nästa steg

Besök följande artikel om du vill veta om ett paket tillåts eller nekas för en specifik virtuell dator via portalen. [Kontrollera om trafik tillåts på en virtuell dator med IP-flöda Kontrollera med hjälp av portalen](network-watcher-check-ip-flow-verify-portal.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png













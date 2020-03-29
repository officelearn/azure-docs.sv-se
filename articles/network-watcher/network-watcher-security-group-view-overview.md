---
title: Introduktion till säkerhetsgruppsvyn i Azure Network Watcher | Microsoft-dokument
description: Den här sidan innehåller en översikt över säkerhetsvyn Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: damendo
ms.openlocfilehash: 18619cc0f7a21a39d0802575c484c68c5fa1a624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840765"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Introduktion till vyn Effektiva säkerhetsregler i Azure Network Watcher

Nätverkssäkerhetsgrupper associeras på undernätsnivå eller på nätverkskortsnivå. När den är associerad på undernätsnivå gäller den för alla VM-instanser i undernätet. Vyn Effektiva säkerhetsregler returnerar alla konfigurerade NSG:er och regler som är associerade på nätverks-och undernätsnivå för en virtuell dator som ger insikt i konfigurationen. Dessutom returneras de effektiva säkerhetsreglerna för var och en av nätverkskorten i en virtuell dator. Med hjälp av vyn Effektiva säkerhetsregler kan du bedöma en virtuell dator för nätverkssäkerhetsproblem som öppna portar. Du kan också validera om nätverkssäkerhetsgruppen fungerar som förväntat baserat på en [jämförelse mellan de konfigurerade och de godkända säkerhetsreglerna](network-watcher-nsg-auditing-powershell.md).

Ett mer utökat användningsfall är i säkerhetsefterlevnad och granskning. Du kan definiera en föreskrivande uppsättning säkerhetsregler som en modell för säkerhetsstyrning i organisationen. En periodisk efterlevnadsgranskning kan implementeras på ett programmatiskt sätt genom att jämföra de normativa reglerna med de gällande reglerna för var och en av de virtuella datorerna i nätverket.

I portalen visas regler för varje nätverksgränssnitt och grupperas efter inkommande kontra utgående. Detta ger en enkel vy över de regler som tillämpas på en virtuell dator. En nedladdningsknapp tillhandahålls för att enkelt ladda ner alla säkerhetsregler oavsett fliken till en CSV-fil.

![säkerhetsgruppsvyn][1]

Regler kan väljas och ett nytt blad öppnas för att visa nätverkssäkerhetsgruppen och käll- och målprefixen. Från det här bladet kan du navigera direkt till nätverkssäkerhetsgruppens resurs.

![Specificera][2]

### <a name="next-steps"></a>Nästa steg

Lär dig hur du granskar inställningarna för nätverkssäkerhetsgruppen genom att besöka [inställningarna för Audit Network Security Group med PowerShell](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png










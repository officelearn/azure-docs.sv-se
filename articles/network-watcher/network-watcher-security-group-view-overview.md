---
title: Introduktion till vyn gällande säkerhets regler i Azure Network Watcher | Microsoft Docs
description: Den här sidan ger en översikt över funktionen för visning av Network Watcher effektiva säkerhets regler
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
ms.openlocfilehash: f4c601184a060c3dfc4f033bcf983bf773f7167f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87022658"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Introduktion till vyn gällande säkerhets regler i Azure Network Watcher

Nätverks säkerhets grupper är kopplade till en under näts nivå eller på en NÄTVERKSKORTs nivå. När den är kopplad till en under näts nivå tillämpas den på alla virtuella dator instanser i under nätet. Vyn gällande säkerhets regler returnerar alla konfigurerade NSG: er och regler som är kopplade till ett nätverkskort och en under näts nivå för en virtuell dator som ger inblick i konfigurationen. Dessutom returneras de effektiva säkerhets reglerna för varje nätverkskort i en virtuell dator. Med vyn gällande säkerhets regler kan du utvärdera en virtuell dator för nätverks sårbarheter, till exempel öppna portar. Du kan också kontrol lera om nätverks säkerhets gruppen fungerar som förväntat baserat på en [jämförelse mellan de konfigurerade och godkända säkerhets reglerna](network-watcher-nsg-auditing-powershell.md).

Ett mer utökat användnings fall är i säkerhetsefterlevnad och granskning. Du kan definiera en preskriptad uppsättning säkerhets regler som en modell för säkerhets styrning i din organisation. En regelbunden granskning av efterlevnad kan implementeras på ett programmerings sätt genom att jämföra de förebyggande reglerna med de gällande reglerna för var och en av de virtuella datorerna i nätverket.

I Portal reglerna visas för varje nätverks gränssnitt och grupperas efter inkommande vs utgående. Detta ger en enkel vy över reglerna som tillämpas på en virtuell dator. En nedladdnings knapp tillhandahålls för att enkelt ladda ned alla säkerhets regler, oavsett fliken, i en CSV-fil.

![vyn säkerhets grupp][1]

Regler kan väljas och ett nytt blad öppnas upp för att Visa nätverks säkerhets gruppen och käll-och mål prefix. Från det här bladet kan du gå direkt till resursen nätverks säkerhets grupp.

![detaljgranska][2]

### <a name="next-steps"></a>Nästa steg

Du kan också använda funktionen *gällande säkerhets grupper* genom att använda andra metoder i listan nedan:
* [REST-API](https://docs.microsoft.com/rest/api/virtualnetwork/NetworkInterfaces/ListEffectiveNetworkSecurityGroups)
* [PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azeffectivenetworksecuritygroup?view=azps-4.4.0)
* [Azure CLI](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-list-effective-nsg)

Lär dig hur du granskar inställningarna för nätverks säkerhets gruppen genom att gå till [granskning nätverks säkerhets grupp inställningar med PowerShell](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png










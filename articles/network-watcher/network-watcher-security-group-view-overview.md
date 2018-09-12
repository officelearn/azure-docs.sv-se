---
title: Introduktion till säkerhetsgruppvy i Azure Network Watcher | Microsoft Docs
description: Den här sidan innehåller en översikt över Network Watcher visa säkerhetsfunktion
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: ad27ab85-9d84-4759-b2b9-e861ef8ea8d8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: jdial
ms.openlocfilehash: 15f6bd0d7da63924e52db8ec7e2cbb0ee7483f82
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391546"
---
# <a name="introduction-to-network-security-group-view-in-azure-network-watcher"></a>Introduktion till vy för nätverkssäkerhetsgrupp i Azure Network Watcher

Nätverkssäkerhetsgrupper är associerade på en undernätverksnivå eller på en NIC-nivå. När associerade på en undernätverksnivå måste tillämpas på alla VM-instanser i undernätet. Nätverkssäkerhetsgruppvy returnerar alla konfigurerade NSG: er och regler som är associerade på Nätverkskorts- och undernätsnivå nivå för en virtuell dator som ger inblick i konfigurationen. Dessutom returneras gällande säkerhetsregler för varje nätverkskort på en virtuell dator. Med hjälp av nätverkssäkerhetsgruppvy, kan du utvärdera en virtuell dator för nätverk, till exempel öppna portar. Du kan också bekräfta om Nätverkssäkerhetsgruppen fungerar som förväntat baserat på en [jämförelse mellan den konfigurerade och de godkända säkerhetsreglerna](network-watcher-nsg-auditing-powershell.md).

Det är ett mer utökade användningsfall i säkerhet och granskning. Du kan definiera en ingående säkerhetsregler som en modell för säkerhet, styrning i din organisation. En regelbunden efterlevnadsgranskning kan implementeras på ett programmässiga sätt genom att jämföra de ingående reglerna med reglerna för effektiva för var och en av de virtuella datorerna i nätverket.

I portalen regler är dividerat med effektiva, undernät, nätverksgränssnitt och standard. Detta ger en enkel överblick över de regler som tillämpas på en virtuell dator. En hämtningsknappen tillhandahålls för att enkelt hämta alla säkerhetsregler oavsett fliken till en CSV-fil.

![säkerhetsgruppvy][1]

Regler kan väljas och ett nytt blad som öppnas så att de Nätverkssäkerhetsgruppen och prefix som källa och mål. Du kan navigera direkt till Nätverkssäkerhetsgrupp kopplad till resursen från det här bladet.

![Detaljgranska][2]

### <a name="next-steps"></a>Nästa steg

Lär dig hur du granskningsinställningar säkerhetsgrupp i nätverket genom att besöka [granska Nätverkssäkerhetsgrupp inställningar med PowerShell](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png










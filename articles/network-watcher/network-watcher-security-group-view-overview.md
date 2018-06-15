---
title: Introduktion till säkerhetsvyn grupp i Azure-Nätverksbevakaren | Microsoft Docs
description: Den här sidan innehåller en översikt över Nätverksbevakaren säkerhet visa kapaciteten
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
ms.openlocfilehash: f4175875b68c52e68588b8d0debd003ab73427ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23864212"
---
# <a name="introduction-to-network-security-group-view-in-azure-network-watcher"></a>Introduktion till vyn av grupp för nätverk i Azure Nätverksbevakaren

Nätverkssäkerhetsgrupper associeras på en undernätverksnivå eller på en NIC-nivå. Som är associerad till en undernätverksnivå, gäller alla VM-instanser i undernätet. Nätverkssäkerhetsgruppen vyn returnerar alla konfigurerade NSG: er och regler som är associerade på ett nätverkskort och undernät nivå för en virtuell dator som ger inblick i konfigurationen. Dessutom returneras effektiva säkerhetsregler för varje nätverkskort i en virtuell dator. Med hjälp av Nätverkssäkerhetsgruppen vyn du utvärdera en virtuell dator för säkerhetsrisker i nätverket, till exempel öppna portar. Du kan också verifiera om säkerhetsgrupp för nätverk fungerar som förväntat baserat på en [jämförelse mellan den konfigurerade och effektiva säkerhetsregler](network-watcher-nsg-auditing-powershell.md).

Det är ett mer utökade användningsfall i säkerhetsefterlevnaden och granskning. Du kan definiera en normativ säkerhetsregler som en modell för styrning av säkerhet i din organisation. En periodisk efterlevnad audit kan implementeras på programmässiga sätt genom att jämföra normativ regler med de effektiva reglerna för var och en av de virtuella datorerna i nätverket.

I portalen regler är indelade efter gällande, undernät, nätverksgränssnittet och standard. Detta ger en enkel vy i reglerna som gäller för en virtuell dator. En knapp för hämtning har angetts för enkelt hämta säkerhetsregler oavsett fliken till en CSV-fil.

![gruppvy för säkerhet][1]

Regler kan väljas och ett nytt blad öppnas och visar Nätverkssäkerhetsgruppen och käll- och prefix. Du kan gå direkt till Nätverkssäkerhetsgruppen resursen från det här bladet.

![nedbrytning][2]

### <a name="next-steps"></a>Nästa steg

Lär dig hur du granskningsinställningar säkerhetsgrupp för nätverk genom att besöka [gransknings-och Nätverkssäkerhetsgruppen inställningar med PowerShell](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png










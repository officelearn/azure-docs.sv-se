---
title: Introduktion till diagnostik för nätverks konfiguration i Azure Network Watcher | Microsoft Docs
description: Den här sidan innehåller en översikt över diagnostik för Network Watcher-nätverks konfiguration
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2020
ms.author: damendo
ms.openlocfilehash: 4d308b8a1a589308e2481c8bb9e0dc9ec64be25b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90996694"
---
# <a name="introduction-to-network-configuration-diagnostics-in-azure-network-watcher"></a>Introduktion till diagnostik för nätverks konfiguration i Azure Network Watcher

Diagnostikverktyget för nätverks konfiguration hjälper kunderna att förstå vilka trafikflöden som ska tillåtas eller nekas i Azure-Virtual Network tillsammans med detaljerad information för fel sökning. Det kan hjälpa dig att förstå om dina NSG-regler har kon figurer ATS korrekt. 

## <a name="pre-requisites"></a>Förutsättningar
För att använda diagnostik för nätverks konfiguration måste Network Watcher aktive ras i din prenumeration. Se [skapa en Azure Network Watcher-instans](https://docs.microsoft.com/azure/network-watcher/network-watcher-create) som ska aktive ras.

## <a name="background"></a>Bakgrund

- Dina resurser i Azure är anslutna via virtuella nätverk (virtuella nätverk) och undernät. Säkerheten för dessa virtuella nätverk och undernät kan hanteras med en nätverks säkerhets grupp (NSG).
- En NSG innehåller en lista över säkerhets regler som tillåter eller nekar nätverks trafik till resurser som den är ansluten till. NSG: er kan kopplas till undernät, enskilda virtuella datorer eller enskilda nätverks gränssnitt (NIC) som är anslutna till virtuella datorer. 
- Alla trafikflöden i nätverket utvärderas med hjälp av reglerna i tillämpliga NSG.
- Reglerna utvärderas baserat på prioritets nummer från lägsta till högsta 

## <a name="how-does-network-configuration-diagnostic-work"></a>Hur fungerar diagnostik för nätverks konfiguration? 

För ett angivet flöde kör verktyget NCD en simulering av flödet och returnerar om flödet skulle tillåtas (eller nekas) och detaljerad information om regler som tillåter/nekar flödet.  Kunder måste ange information om ett flöde som källa, mål, protokoll osv. Verktyget returnerar om trafiken tillåts eller nekades, vilka NSG-regler som utvärderades för det angivna flödet och utvärderings resultatet för varje regel.

## <a name="next-steps"></a>Nästa steg

Använd diagnostik för nätverks konfiguration via andra gränssnitt
 - [REST-API](https://docs.microsoft.com/rest/api/network-watcher/networkwatchers/getnetworkconfigurationdiagnostic)
 - [PowerShell](https://docs.microsoft.com/powershell/module/az.network/invoke-aznetworkwatchernetworkconfigurationdiagnostic?view=azps-4.6.1)
 - [Azure CLI](https://docs.microsoft.com/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_run_configuration_diagnostic)


---
title: Översikt över Azure Security benchmark
description: Översikt över säkerhet-benchmark
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f2cdaafddb14d8b69fd708e4c89a578b60e53317
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82606882"
---
# <a name="overview-of-the-azure-security-benchmark"></a>Översikt över Azures säkerhets benchmark

Azure Security Benchmark innehåller rekommendationer som hjälper dig att förbättra säkerheten för dina program och data i Azure.

Detta riktmärke fokuserar på molnbaserade kontroll områden. Dessa kontroller är konsekventa med välkända säkerhets mått, till exempel de som beskrivs av CIS-kontroller (Center for Internet Security) version 7,1.

Följande kontroller används i Azures säkerhets benchmark: 

- [Nätverks säkerhet](security-control-network-security.md)
- [Loggning och övervakning](security-control-logging-monitoring.md)
- [Identitets- och åtkomstkontroll](security-control-identity-access-control.md)
- [Data skydd](security-control-data-protection.md)
- [Sårbarhetshantering](security-control-vulnerability-management.md)
- [Inventerings- och tillgångshantering](security-control-inventory-asset-management.md)
- [Säker konfiguration](security-control-secure-configuration.md)
- [Skydd mot skadlig kod](security-control-malware-defense.md)
- [Dataåterställning](security-control-data-recovery.md)
- [Incidenthantering](security-control-incident-response.md)
- [Penetrationstester och Red Team-tester](security-control-penetration-tests-red-team-exercises.md)

Du kan också hämta [Azure Security-prestandatest v1 Excel-kalkylbladet](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets).

## <a name="azure-security-benchmark-recommendations"></a>Rekommendationer för Azure Security benchmark 

Varje rekommendation innehåller följande information: 

- **Azure-ID**: det ID för Azure-säkerhetsbenchmark som motsvarar rekommendationen. 
- **CIS-ID**: de CIS-benchmark-rekommendationer som motsvarar den här rekommendationen.  
- **Ansvar**: om kunden eller tjänst leverantören (eller båda) är (är) ansvarig för att implementera den här rekommendationen. Säkerhets ansvars områden delas i det offentliga molnet. Vissa säkerhets kontroller är bara tillgängliga för moln tjänst leverantören och därför ansvarar leverantören för att adressera dem. Detta är allmänna observationer – för vissa enskilda tjänster skiljer sig ansvaret från det som anges i Azures säkerhets benchmark. Skillnaderna beskrivs i grundläggande rekommendationer för den enskilda tjänsten. 
- **Information**: motiveringen till rekommendationen och länkar till vägledning om hur du implementerar den. Om rekommendationen stöds av Azure Security Center, visas även den informationen.

Vi välkomnar din detaljerade feedback och aktivt deltagande i Azures säkerhets benchmark-ansträngning. Om du vill ge Azures prestandatest för Azure-säkerhet kan du fylla i formuläret på [https://aka.ms/AzSecBenchmark](https://aka.ms/AzSecBenchmark) .

## <a name="next-steps"></a>Nästa steg

- Se den första säkerhets kontrollen: [nätverks säkerhet](security-control-network-security.md)
- Läs [introduktionen till Azure Security benchmark](introduction.md)
- Hämta [Excel-kalkylbladet för Azure Security benchmark v1](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)

---
title: Översikt över Azure Security benchmark
description: Översikt över säkerhet-benchmark
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 2d12fa85fbc134ba2578795619db89f4a5058b26
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934750"
---
# <a name="overview-of-azure-security-controls"></a>Översikt över Azures säkerhets kontroller

Azures säkerhets benchmark innehåller rekommendationer som hjälper dig att förbättra säkerheten för dina program och data på Azure.

Detta riktmärke fokuserar på molnbaserade kontroll områden. Dessa kontroller är konsekventa med välkända säkerhets mått, till exempel de som beskrivs av CIS-kontroller (Center for Internet Security) version 7,1.

Följande kontroller används i Azures säkerhets benchmark: 

- [Nätverks säkerhet](security-control-network-security.md)
- [Loggning och övervakning](security-control-logging-monitoring.md)
- [Identitets-och Access Control](security-control-identity-access-control.md)
- [Dataskydd](security-control-data-protection.md)
- [Sårbarhets hantering](security-control-vulnerability-management.md)
- [Inventering och till gångs hantering](security-control-inventory-asset-management.md)
- [Säker konfiguration](security-control-secure-configuration.md)
- [Skydd mot skadlig kod](security-control-malware-defense.md)
- [Data återställning](security-control-data-recovery.md)
- [Incident svar](security-control-incident-response.md)
- [Inträngande tester och röda team övningar](security-control-penetration-tests-red-team-exercises.md)

Du kan också hämta [Azure Security-prestandatest v1 Excel-kalkylbladet](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets).

## <a name="azure-security-benchmark-recommendations"></a>Rekommendationer för Azure Security benchmark 

Varje rekommendation innehåller följande information: 

- **Azure-ID**: det ID för Azure-säkerhetsbenchmark som motsvarar rekommendationen. 
- **CIS-ID**: de CIS-benchmark-rekommendationer som motsvarar den här rekommendationen.  
- **Ansvar**: om kunden eller tjänst leverantören (eller båda) är (är) ansvarig för att implementera den här rekommendationen. Säkerhets ansvars områden delas i det offentliga molnet. Vissa säkerhets kontroller är bara tillgängliga för moln tjänst leverantören och därför ansvarar leverantören för att adressera dem. Detta är allmänna observationer – för vissa enskilda tjänster skiljer sig ansvaret från det som anges i Azures säkerhets benchmark. Skillnaderna beskrivs i grundläggande rekommendationer för den enskilda tjänsten. 
- **Information**: motiveringen till rekommendationen och länkar till vägledning om hur du implementerar den. Om rekommendationen stöds av Azure Security Center, visas även den informationen.

Vi välkomnar din detaljerade feedback och aktivt deltagande i Azures säkerhets benchmark-ansträngning. Om du vill ge benchmark-gruppens direkta indatatyper fyller du i formuläret på [https://aka.ms/AzSecBenchmark](https://aka.ms/AzSecBenchmark).

## <a name="next-steps"></a>Efterföljande moment

- Se den första säkerhets kontrollen: [nätverks säkerhet](security-control-network-security.md)
- Hämta [Excel-kalkylbladet för Azure Security benchmark v1](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)

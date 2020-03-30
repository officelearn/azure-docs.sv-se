---
title: Översikt över Azure Security Benchmark
description: Översikt över säkerhetsriktmärken
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 2f4b034948605b0a53a0320863608d284719a96d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587540"
---
# <a name="overview-of-the-azure-security-benchmark"></a>Översikt över Azure Security Benchmark

Azure Security Benchmark innehåller rekommendationer som hjälper dig att förbättra säkerheten för dina program och data på Azure.

Detta riktmärke fokuserar på molncentrerade kontrollområden. Dessa kontroller överensstämmer med välkända säkerhetsriktmärken, till exempel de som beskrivs av Cis-kontroller (Center for Internet Security) Version 7.1.

Följande kontroller används i Azure Security Benchmark: 

- [Nätverkssäkerhet](security-control-network-security.md)
- [Loggning och övervakning](security-control-logging-monitoring.md)
- [Identitets- och åtkomstkontroll](security-control-identity-access-control.md)
- [Dataskydd](security-control-data-protection.md)
- [Sårbarhetshantering](security-control-vulnerability-management.md)
- [Inventerings- och tillgångshantering](security-control-inventory-asset-management.md)
- [Säker konfiguration](security-control-secure-configuration.md)
- [Skydd mot skadlig kod](security-control-malware-defense.md)
- [Dataåterställning](security-control-data-recovery.md)
- [Incident respons](security-control-incident-response.md)
- [Intrångstester och Red Team-övningar (rött lag)](security-control-penetration-tests-red-team-exercises.md)

Du kan också hämta [Azure Security Benchmark v1 excel-kalkylbladet](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets).

## <a name="azure-security-benchmark-recommendations"></a>Azure Security Benchmark rekommendationer 

Varje rekommendation innehåller följande information: 

- **Azure ID**: Azure Security Benchmark ID som motsvarar rekommendationen. 
- **CIS ID:n:** CIS-referensrekommendationen/rekommendationerna som motsvarar denna rekommendation.  
- **Ansvar**: Huruvida kunden eller tjänsteleverantören (eller båda) är (är) ansvarig för att genomföra denna rekommendation. Säkerhetsansvar delas i det offentliga molnet. Vissa säkerhetskontroller är endast tillgängliga för molntjänstleverantören och därför är leverantören ansvarig för att hantera dessa. Dessa är allmänna observationer – för vissa enskilda tjänster kommer ansvaret att vara annorlunda än vad som anges i Azure Security Benchmark. Dessa skillnader beskrivs i baslinjerekommendationerna för den enskilda tjänsten. 
- **Detaljer**: Motiven för rekommendationen och länkar till vägledning om hur den ska genomföras. Om rekommendationen stöds av Azure Security Center visas även den informationen.

Vi välkomnar din detaljerade feedback och aktiva deltagande i Azure Security Benchmark-arbetet. Om du vill ge Benchmark-teamet direkt inmatning, [https://aka.ms/AzSecBenchmark](https://aka.ms/AzSecBenchmark)fyll i formuläret på .

## <a name="next-steps"></a>Efterföljande moment

- Se den första säkerhetskontrollen: [Nätverkssäkerhet](security-control-network-security.md)
- Läs [introduktionen](introduction.md) för Azure Security Benchmark
- Ladda ned [Azure Security Benchmark v1 excel-kalkylbladet](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)

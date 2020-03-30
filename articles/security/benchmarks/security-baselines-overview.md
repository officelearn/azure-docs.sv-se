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
ms.openlocfilehash: c16d7247b781fea04cfa2d53b8854cff14e039c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616452"
---
# <a name="overview-of-azure-security-baselines"></a>Översikt över Azure Security-originalplaner

Azure Security Baselines hjälper dig att stärka säkerheten för våra produkter genom förbättrade verktygs-, spårnings- och säkerhetsfunktioner och ger dig en konsekvent upplevelse när du skyddar din miljö.

Azure Service Security-baslinjer fokuserar på molncentrerade kontrollområden. Dessa kontroller överensstämmer med välkända säkerhetsriktmärken, till exempel de som beskrivs av Center for Internet Security (CIS). Våra baslinjer ger vägledning för de kontrollområden som anges i [Azure Security Benchmark](overview.md).

Varje rekommendation innehåller följande information:
- **Azure ID**: Azure Security Benchmark ID som motsvarar rekommendationen.
- **Rekommendation:** Efter direkt efter Azure-ID ger rekommendationen en beskrivning på hög nivå av kontrollen.
- **Vägledning**: Motiveringen till rekommendationen och länkar till vägledning om hur den ska genomföras. Om rekommendationen stöds av Azure Security Center visas även den informationen.
- **Ansvar**: Vem är ansvarig för att genomföra kontrollen. Möjliga scenarier är kundansvar, Microsofts ansvar eller delat ansvar.
- **Övervakning av Azure Security Center**: Om kontrollen övervakas av Azure Security Center, med länk till referens.

Alla rekommendationer, inklusive rekommendationer som inte är tillämpliga på den här specifika tjänsten, ingår i baslinjen för att ge dig en fullständig bild av hur Azure Security Benchmark relaterar till varje tjänst. Det kan ibland finnas kontroller som inte är tillämpliga av olika skäl, till exempel kan IaaS/compute-centrerade kontroller (till exempel kontroller som är specifika för os-konfigurationshantering) inte vara tillämpliga på PaaS-tjänster.

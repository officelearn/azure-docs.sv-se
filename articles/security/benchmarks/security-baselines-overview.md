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
ms.openlocfilehash: c16d7247b781fea04cfa2d53b8854cff14e039c7
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616452"
---
# <a name="overview-of-azure-security-baselines"></a>Översikt över Azures säkerhets bas linjer

Med Azures säkerhets bas linjer kan du förbättra säkerheten för våra produkter genom förbättrade verktyg, spårnings-och säkerhetsfunktioner och ger dig en enhetlig upplevelse när du skyddar din miljö.

Azure Service Security-bas linjer fokuserar på molnbaserade kontroll områden. Dessa kontroller är konsekventa med välkända säkerhets mått, till exempel de som beskrivs i Center for Internet Security (CIS). Våra bas linjer ger vägledning för de kontroll områden som anges i [Azures säkerhets benchmark](overview.md).

Varje rekommendation innehåller följande information:
- **Azure-ID**: det ID för Azure-säkerhetsbenchmark som motsvarar rekommendationen.
- **Rekommendation**: följer direkt efter Azure-ID: t ger rekommendationen en övergripande beskrivning av kontrollen.
- **Vägledning**: motiveringen till rekommendationen och länkar till vägledning om hur du implementerar den. Om rekommendationen stöds av Azure Security Center, visas även den informationen.
- **Ansvar**: vem som ansvarar för att implementera kontrollen. Möjliga scenarier är kund ansvar, Microsoft-ansvar eller delade ansvar.
- **Azure Security Center övervakning**: om kontrollen övervakas av Azure Security Center, med länk till referens.

Alla rekommendationer, inklusive rekommendationer som inte gäller för den här tjänsten, ingår i bas linjen för att ge dig en fullständig bild av hur Azures säkerhets benchmark relaterar till varje tjänst. Det kan ibland finnas kontroller som inte är tillämpliga av olika anledningar, t. ex. IaaS/Compute-inriktade kontroller (till exempel kontroller som är speciella för konfigurations hantering av operativ system) kanske inte kan tillämpas på PaaS-tjänster.

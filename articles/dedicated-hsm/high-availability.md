---
title: Hög tillgänglighet – Azure Dedikerad HSM | Microsoft-dokument
description: Azure Dedikerad HSM hög tillgänglighet exempel och grundläggande överväganden
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 536ef62acad900090924598edfa45450b2a8c951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70882249"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Azure Dedikerad HSM hög tillgänglighet

Azure Dedicated HSM stöds av Microsofts datacenter med högtillgänge. Alla datacenter som är tillgängliga och är sårbara för lokaliserade fel och under extrema omständigheter, regionala fel. Microsoft distribuerar HSM-enheter i olika datacenter inom en region för att säkerställa att etablering av flera enheter inte leder till att dessa enheter delar ett enda rack. En ytterligare nivå av hög tillgänglighet kan uppnås genom att para ihop dessa HSM-datorer över datacenter i en region med hjälp av Funktionen Gemalto HA-grupp. Det är också möjligt att para ihop enheter mellan regioner för att hantera regionala redundans i en katastrofåterställningssituation. Med den här konfigurationen med hög tillgänglighet i flera lager åtgärdas alla enhetsfel automatiskt för att programmen ska fungera. Alla datacenter har också extra enheter och komponenter på plats så att alla misslyckade enheter kan ersättas i tid.

## <a name="high-availability-example"></a>Exempel på hög tillgänglighet

Information om hur du konfigurerar HSM-enheter för hög tillgänglighet på programvarunivå finns i "Gemalto Luna Network HSM Administration Guide". Det här dokumentet finns på [Gemalto HSM-sidan](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).

Följande diagram visar en arkitektur med hög tillgänglig tillgång. Den använder flera enheter i regionen och flera enheter som är kopplade i en separat region. Den här arkitekturen använder minst fyra HSM-enheter och virtuella nätverkskomponenter.

![Diagram med hög tillgänglighet](media/high-availability/high-availability.png)

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att alla viktiga begrepp för tjänsten, till exempel hög tillgänglighet och säkerhet, är väl förstådda innan enhetsetablering och programdesign eller distribution.
Ytterligare begreppsnivå ämnen:

* [Distributionsarkitektur](deployment-architecture.md)
* [Fysisk säkerhet](physical-security.md)
* [Nätverk](networking.md)
* [Support](supportability.md)
* [Övervakning](monitoring.md)

Mer information om hur du konfigurerar HSM-enheter för hög tillgänglighet finns i Gemaltos kundsupportportal för administratörsguiderna och se avsnitt 6.

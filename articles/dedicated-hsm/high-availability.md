---
title: Hög tillgänglighet – Azure dedikerad HSM | Microsoft Docs
description: Exempel på Azure dedikerad HSM hög tillgänglighet och grundläggande överväganden
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
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70882249"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Azure dedikerad HSM hög tillgänglighet

Azure dedikerad HSM underbyggd av Microsofts med hög tillgänglighet datacenter. Men är alla datacenter med hög tillgänglighet sårbara för lokaliserat fel och i extrema fall regionala på fel. Microsoft distribuerar HSM-enheter i olika datacenter inom en region för att se till att etablera flera enheter inte leder till dessa enheter som delar ett enda rack. Ytterligare en hög tillgänglighets nivå kan uppnås genom att para ihop dessa HSM: er över data Center i en region med hjälp av grupp funktionen Gemalto HA. Det är också möjligt att par enheter över regioner för att åtgärda regional redundans i en disaster recovery-situation. Med den här konfigurationen med flera lager hög tillgänglighet åtgärdas eventuella fel automatiskt för att hålla program fungerar. Alla Datacenter har också ledig enheter och komponenter på plats så att alla misslyckade enheter kan ersättas i tid.

## <a name="high-availability-example"></a>Exempel på hög tillgänglighet

Information om hur du konfigurerar HSM-enheter för hög tillgänglighet på programnivå finns i ”Gemalto Luna nätverk HSM administrationsguiden'. Det här dokumentet är tillgängligt på [HSM-sidan för Gemalto](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).

Följande diagram visar en arkitektur med hög tillgänglighet. Den använder flera enheter i regionen och flera enheter tillsammans i en separat region. Den här arkitekturen använder minst fyra HSM-enheter och virtuella nätverkskomponenter.

![Diagram för hög tillgänglighet](media/high-availability/high-availability.png)

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att alla nyckelbegreppen för tjänst, till exempel hög tillgänglighet och säkerhet, är väl förstådda innan enhetsetablering och programmets design eller distribution.
Ytterligare konceptet på ämnen:

* [Distributionsarkitektur för](deployment-architecture.md)
* [Fysisk säkerhet](physical-security.md)
* [Nätverk](networking.md)
* [Support](supportability.md)
* [Övervakning](monitoring.md)

Mer specifik information om hur du konfigurerar HSM-enheter för hög tillgänglighet finns stöd för Gemalto kundportalen riktlinjerna för administratör och se avsnitt 6.

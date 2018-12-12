---
title: Hög tillgänglighet – Azure dedikerad HSM | Microsoft Docs
description: Exempel på Azure dedikerad HSM hög tillgänglighet och grundläggande överväganden
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 991482d3d6be4d09b37014e5bb03708987e0f74e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078182"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Azure dedikerad HSM hög tillgänglighet

Azure dedikerad HSM underbyggd av Microsofts med hög tillgänglighet datacenter. Men är alla datacenter med hög tillgänglighet sårbara för lokaliserat fel och i extrema fall regionala på fel. Microsoft distribuerar HSM-enheter i olika datacenter inom en region för att se till att etablera flera enheter inte leder till dessa enheter som delar ett enda rack. En ytterligare nivå av hög tillgänglighet uppnås genom att länka dessa HSM: er i datacenter i en region. Det är också möjligt att par enheter över regioner för att åtgärda regional redundans i en disaster recovery-situation. Med den här konfigurationen med flera lager hög tillgänglighet åtgärdas eventuella fel automatiskt för att hålla program fungerar. Alla Datacenter har också ledig enheter och komponenter på plats så att alla misslyckade enheter kan ersättas i tid.

## <a name="high-availability-example"></a>Exempel på hög tillgänglighet

Information om hur du konfigurerar HSM-enheter för hög tillgänglighet på programnivå finns i ”Gemalto Luna nätverk HSM administrationsguiden'. Det här dokumentet är tillgängligt på den [Gemalto Customer Support Portal](https://supportportal.gemalto.com/csm/).

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

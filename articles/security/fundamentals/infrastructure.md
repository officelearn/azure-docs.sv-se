---
title: Säkerhet för Azure-infrastruktur | Microsoft Docs
description: I artikeln beskrivs hur Microsoft arbetar för att skydda våra Azure-datacenter.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: a17d98d49d2c653e2498a663829d26e8a171fd74
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72433509"
---
# <a name="azure-infrastructure-security"></a>Säkerhet för Azure-infrastruktur
Microsoft Azure körs i Data Center som hanteras och drivs av Microsoft. Dessa geografiskt spridda Data Center följer viktiga bransch standarder, till exempel ISO/IEC 27001:2013 och NIST SP 800-53, för säkerhet och tillförlitlighet. Data centren hanteras, övervakas och administreras av Microsofts drift personal. Drifts personalen har års erfarenhet av att leverera världens största onlinetjänster med 24 x 7-kontinuitet.

Den här artikel serien innehåller information om vad Microsoft gör för att skydda Azure-infrastrukturen. Artikel adressen:

- [Fysisk säkerhet](physical-security.md)
- [Tillgänglighet](infrastructure-availability.md)
- [Komponenter och gränser](infrastructure-components.md)
- [Nätverksarkitektur](infrastructure-network.md)
- [Produktions nätverk](production-network.md)
- [SQL Database](infrastructure-sql.md)
- [Åtgärder](infrastructure-operations.md)
- [Övervakning](infrastructure-monitoring.md)
- [Äkthet](infrastructure-integrity.md)
- [Dataskydd](protection-customer-data.md)

## <a name="shared-responsibility-model"></a>Delad ansvars modell
Det är viktigt att förstå delningen av ansvaret mellan dig och Microsoft. Lokalt, du äger hela stacken, men när du går vidare till molnet överförs vissa ansvars områden till Microsoft. Följande bild illustrerar ansvars områdena, enligt distributions typen för stacken (program vara som en tjänst [SaaS], plattform som en tjänst [PaaS], infrastruktur som en tjänst [IaaS] och lokalt).

![Bild som visar ansvars områden](./media/infrastructure/responsibility-zones.png)

Du är alltid ansvarig för följande, oavsett typ av distribution:

- Data
- Slutpunkter
- Konto
- Åtkomsthantering

Se till att du förstår ansvars fördelningen mellan dig och Microsoft i en SaaS-, PaaS-och IaaS-distribution. Mer information finns i [delade ansvars områden för molnbaserad data behandling](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225237/1/Shared%20Responsibilities%20for%20Cloud%20Computing%20(2017-04-03).pdf).

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokal och fysisk säkerhet](physical-security.md)
- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Komponenter och gränser för Azure information system](infrastructure-components.md)
- [Azure nätverks arkitektur](infrastructure-network.md)
- [Azures produktions nätverk](production-network.md)
- [Azure SQL Database säkerhetsfunktioner](infrastructure-sql.md)
- [Åtgärder och hantering av Azure-produktion](infrastructure-operations.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Integritet för Azure-infrastruktur](infrastructure-integrity.md)
- [Data skydd för Azure-kunder](protection-customer-data.md)



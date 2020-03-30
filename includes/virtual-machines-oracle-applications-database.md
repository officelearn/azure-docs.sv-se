---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361536"
---
### <a name="database-tier"></a>Databasnivå

Databasnivån innehåller databasinstanserna för programmet. Databasen kan vara antingen ett Oracle DB, Oracle RAC eller Oracle Exadata Database system. 

Om valet är att använda Oracle DB kan databasinstansen distribueras på Azure via Oracle DB-avbildningar som är tillgängliga på Azure Marketplace. Alternativt kan du använda sammankopplingen mellan Azure och OCI för att distribuera Oracle DB i en PaaS-modell på OCI.

För Oracle RAC kan du distribuera Oracle RAC på Azure CloudSimple i IaaS-modell eller i OCI i PaaS-modellen. Vi rekommenderar att du använder ett RAC-system med två noder. 

Slutligen, för Exadata-system, använd OCI-sammankopplingen och distribuera Exadata-systemet i OCI. I det föregående arkitekturdiagrammet ovan visas ett Exadata-system som distribueras i OCI över två undernät.

För produktionsscenarier distribuerar du flera instanser av databasen över två tillgänglighetszoner (om de distribuerar i Azure) eller två tillgänglighetsdomäner (i OCI). Använd Oracle Active Data Guard för att synkronisera primär- och standby-databaser.

Databasnivån tar bara emot begäranden från mellannivån. Vi rekommenderar att du ställer in en nätverkssäkerhetsgrupp (säkerhetslista om du distribuerar databasen i OCI) så att begäranden på port 1521 endast tillåts från mellannivån och port 22 från skyddsservern av administrativa skäl.

För databaser som distribueras i OCI måste ett separat virtuellt molnnätverk konfigureras med en dynamisk routningsgateway (DRG) som är ansluten till FastConnect-kretsen.
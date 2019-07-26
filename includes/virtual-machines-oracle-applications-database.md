---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361536"
---
### <a name="database-tier"></a>Databasnivån

Databas nivån innehåller databas instanserna för programmet. Databasen kan vara antingen en Oracle DB, Oracle RAC eller Oracle Exadata Database system. 

Om du väljer att använda Oracle DB kan databas instansen distribueras på Azure via Oracle DB avbildningar som är tillgängliga på Azure Marketplace. Alternativt kan du använda kopplingen mellan Azure och OCI för att distribuera Oracle DB i en PaaS-modell på OCI.

För Oracle RAC kan du distribuera Oracle RAC på Azure-CloudSimple i IaaS-modellen eller i OCI i PaaS modell. Vi rekommenderar att du använder ett RAC-system med två noder. 

För Exadata-system använder du slutligen OCI-sammanlänkning och distribuerar Exadata-systemet i OCI. Föregående arkitektur diagram ovan visar ett Exadata-system som distribueras i OCI över två undernät.

För produktions scenarier distribuerar du flera instanser av databasen över två tillgänglighets zoner (om du distribuerar i Azure) eller två tillgänglighets domäner (i OCI). Använd Oracle Active data Guard för att synkronisera primära databaser och standby-databaser.

Databas nivån tar bara emot begär Anden från mellan nivån. Vi rekommenderar att du konfigurerar en nätverks säkerhets grupp (säkerhets lista om distributionen av databasen i OCI) till att endast tillåta begär Anden på port 1521 från mellan nivån och port 22 från skydds-servern av administrativa skäl.

För databaser som distribueras i OCI måste ett separat virtuellt moln nätverk konfigureras med en DRG (Dynamic routing Gateway) som är ansluten till din FastConnect-krets.
---
title: Migrera servrar och virtuella datorer till Azure med Azure Site Recovery
description: Beskriver hur du migrerar lokala och virtuella Azure IaaS-datorer till Azure med hjälp av tjänsten Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: raynew
ms.openlocfilehash: aaf01dcd63c21e4741456f4f7fccaf22b8fbfffc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281318"
---
# <a name="about-migration"></a>Om migrering

Använd Azure Migrate tjänsten för att migrera virtuella datorer och servrar till Azure, i stället för tjänsten Azure Site Recovery. [Läs mer](../migrate/migrate-services-overview.md) om Azure Migrate.


## <a name="why-use-azure-migrate"></a>Varför ska jag använda Azure Migrate?

Att använda Azure Migrate för migrering ger ett antal fördelar:
 
 
- Azure Migrate tillhandahåller en centraliserad hubb för identifiering, utvärdering och migrering till Azure.
- Användning av Azure Migrate ger möjlighet till samverkan och framtida utöknings barhet med Azure Migrate verktyg, andra Azure-tjänster och verktyg från tredje part.
- Verktyget Azure Migrate: Migreringsverktyg för Server är syftet-byggt för Server migrering till Azure. Den är optimerad för migrering. Du behöver inte lära dig mer om begrepp och scenarier som inte är direkt relevanta för migrering. 
- Det finns inga avgifter för verktygs användning för migrering i 180 dagar, från tidpunkten då replikeringen startades för en virtuell dator. Det ger dig tid att slutföra migreringen. Du betalar bara för de lagrings-och nätverks resurser som används i replikeringen och för de beräknings avgifter som förbrukas under testmigreringar.
- För virtuella VMware-datorer tillhandahåller Azure Migrate migrering utan agent, förutom den agentbaserade migreringen.
- Vi prioriterar nya funktioner för migrering för Azure Migrate: Migreringsverktyg för Server.

## <a name="when-to-use-site-recovery"></a>När ska jag använda Site Recovery?

Site Recovery ska användas:

- För haveri beredskap för lokala datorer till Azure.
- För haveri beredskap för virtuella Azure-datorer mellan Azure-regioner.

Även om vi rekommenderar att du använder Azure Migrate för att migrera lokala servrar till Azure, kan du fortsätta att använda den för att slutföra migreringen om du redan har startat migreringen med Site Recovery.  

## <a name="next-steps"></a>Nästa steg

> [Läs vanliga frågor](../migrate/resources-faq.md) om Azure Migrate.

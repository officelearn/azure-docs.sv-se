---
title: "Översikt över Azure-databas migrering Service Preview | Microsoft Docs"
description: "Översikt över Azure migrering databastjänsten, som ger sömlös migrering från flera databaskällor till Azure Data plattformar."
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.topic: article
ms.date: 12/13/2017
ms.openlocfilehash: 2aae105b7454209131db79c60d74740ce97c21ce
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="what-is-the-azure-database-migration-service-preview"></a>Vad är Azure databas migrering Service Preview?
Tjänsten Azure Database migrering är en helt hanterad tjänst som utformats för att aktivera sömlös migrering från flera databaskällor till Azure Data plattformar med minimal avbrottstid. Tjänsten är för närvarande i förhandsversion, med utvecklingsarbete fokuserar på:

- Tillförlitlighet och prestanda.
- Iterativ tillägg av käll-och mål-par.
- Fortsatt investeringar i friktion problemfri migrering.

## <a name="use-familiar-tools"></a>Använd välbekanta verktyg
Tjänsten Azure Database migrering integrerar några av funktionerna i vår befintliga verktyg och tjänster.  Det ger kunder med en omfattande lösning för hög tillgänglighet. Tjänsten använder den [Data Migration Assistant](http://aka.ms/dma) som genererar assessment rapporter som ger rekommendationer och vägleder dig genom de ändringar som krävs innan du utför en migrering. Det är att du kan utföra alla åtgärder som krävs. När du är redo att påbörja migreringsprocessen, utför tjänsten Azure Database migrering alla associerade steg. Du kan brand och glömmer migrering projekt med trygg i åtanke om du vet att processen drar nytta av bästa praxis enligt Microsofts.

## <a name="regional-availability-during-public-preview"></a>Regional tillgänglighet under Public Preview
Offentliga förhandsversionen av Azure-databastjänst för migrering är tillgängliga i följande områden:
- Östra USA
- Södra centrala USA
- Västra USA
- Södra Brasilien
- Västra Europa
- Norra Europa
- Sydostasien
- Indien, västra

## <a name="next-steps"></a>Nästa steg
- [Skapa en instans av tjänsten Azure Database migrering med hjälp av Azure portal](quickstart-create-data-migration-service-portal.md).
- [Migrera SQLServer till Azure SQL Database](tutorial-sql-server-to-azure-sql.md).
- [Översikt över krav för att använda tjänsten Azure Database migrering](pre-reqs.md).
- [Vanliga frågor och svar om hur du använder tjänsten Azure Database migrering](faq.md).

---
title: Viktiga ändringar som kommer till Azure Security Center
description: Kommande ändringar av Azure Security Center som du kan behöva känna till och för vilka du kan behöva planera
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2020
ms.author: memildin
ms.openlocfilehash: bf89135d3b5d33936d0b5d4142e966fa3f8f52b9
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92559048"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Viktiga kommande ändringar i Azure Security Center

> [!IMPORTANT]
> Informationen på den här sidan avser för hands versions produkter eller funktioner som kan ändras i stort sett innan de släpps kommersiellt. Microsoft lämnar inga åtaganden eller garantier, vare sig uttryckliga eller underförstådda, med avseende på den information som anges här.

På den här sidan får du lära dig om ändringar som planeras för Security Center. Den beskriver planerade ändringar av produkten som kan påverka saker som dina säkra poäng eller arbets flöden.

Om du letar efter den senaste versions informationen hittar du dem i [vad som är nytt i Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Planerade ändringar

### <a name="recommendations-related-to-azure-security-benchmark-to-be-added-preview"></a>Rekommendationer som rör ökad säkerhet för Azure-säkerhet (för hands version)

| Aspekt | Information |
|---------|---------|
|Meddelande datum | 26 oktober 2020  |
|Datum för den här ändringen  |  November 2020 |
|Påverkan     | Eventuellt mer rekommendationer att granska.<br>Ingen omedelbar påverkan på säker Poäng – för hands versions rekommendationer påverkar inte dina säkra poäng.<br>Ingen omedelbar inverkan på hälso status för dina resurser – för hands versions rekommendationer återger inte en resurs "ohälsosam".|
|  |  |

Azures säkerhets prestanda är Microsofts-skapade, Azure-/regionsspecifika uppsättning rikt linjer för säkerhets-och efterlevnads metod tips baserade på vanliga ramverk för efterlevnad. [Läs mer om Azure Security Benchmark](../security/benchmarks/introduction.md).

Följande 29 nya rekommendationer kommer att läggas till Security Center för att öka omfattningen av benchmark.

För hands versions rekommendationer återger inte en resurs som inte är felfri och ingår inte i beräkningarna av dina säkra poäng. Åtgärda dem när så är möjligt, så att när förhands gransknings perioden är slut bidrar de till dina poäng. Lär dig mer om hur du svarar på dessa rekommendationer i att [åtgärda rekommendationer i Azure Security Center](security-center-remediate-recommendations.md).

- Azure Backup ska vara aktiverat för virtuella datorer
- Gransknings kvarhållning för SQL-servrar ska vara minst 90 dagar
- Diagnostikloggar ska aktive ras i App Service 
- Tvinga SSL-anslutning ska vara aktive rad för MySQL-databas servrar
- Tvinga SSL-anslutning ska vara aktive rad för PostgreSQL-databas servrar
- FTPS måste anges i din API-app
- FTPS måste anges i din Function-app
- FTPS måste anges i din webbapp
- Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MariaDB
- Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MySQL
- Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for PostgreSQL
- Java bör uppdateras till den senaste versionen för din API-app
- Java bör uppdateras till den senaste versionen för din Function-app
- Java bör uppdateras till den senaste versionen för din webbapp
- Hanterad identitet ska användas i din API-app
- Hanterad identitet ska användas i din Function-app
- Hanterad identitet ska användas i din webbapp
- PHP bör uppdateras till den senaste versionen för din API-app
- PHP bör uppdateras till den senaste versionen för din webbapp
- Den privata slut punkten måste vara aktive rad för MariaDB-servrar
- Den privata slut punkten måste vara aktive rad för MySQL-servrar
- Den privata slut punkten måste vara aktive rad för PostgreSQL-servrar
- Python bör uppdateras till den senaste versionen för din API-app
- Python bör uppdateras till den senaste versionen för din Function-app
- Python bör uppdateras till den senaste versionen för din webbapp
- TLS bör uppdateras till den senaste versionen för din API-app
- TLS bör uppdateras till den senaste versionen för din Function-app
- TLS bör uppdateras till den senaste versionen för din webbapp
- Web Apps bör begära ett SSL-certifikat för alla inkommande begär Anden

Relaterade länkar:

- [Läs mer om Azure Security Benchmark](../security/benchmarks/introduction.md).
- [Läs mer om Azure API Apps](../app-service/app-service-web-tutorial-rest-api.md)
- [Läs mer om Azure Function-appar](../azure-functions/functions-overview.md)
- [Läs mer om Azure-Webbappar](../app-service/overview.md)
- [Läs mer om Azure Database for MariaDB](../mariadb/overview.md)
- [Läs mer om Azure Database for MySQL](../mysql/overview.md)
- [Läs mer om Azure Database for PostgreSQL](../postgresql/overview.md)

## <a name="next-steps"></a>Nästa steg

För alla nyligen gjorda ändringar av produkten, se [vad som är nytt i Azure Security Center](release-notes.md).
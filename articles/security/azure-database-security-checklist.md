---
title: Checklista för säkerhet i Azure-databas | Microsoft Docs
description: Den här artikeln innehåller en uppsättning checklista för Azure database-säkerhet.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 7712196caefeea8a62045d432d762fbd16f42011
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="azure-database-security-checklist"></a>Checklista för säkerhet i Azure-databas

För att förbättra säkerheten, Azure-databas innehåller ett antal inbyggda säkerhetsåtgärder som du kan använda för att begränsa och kontrollera åtkomsten.

Exempel på dessa är:

-   En brandvägg som låter dig skapa [regler i brandväggen](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) begränsa anslutning med IP-adress
-   Servernivå brandväggen tillgänglig från Azure-portalen
-   Databasnivå brandväggsregler tillgänglig från SSMS
-   Säker anslutning till databasen med hjälp av säkra anslutningssträngar
-   Använd åtkomsthantering
-   Datakryptering
-   Granskning av SQL Database
-   Hotidentifiering för SQL-databas

## <a name="introduction"></a>Introduktion
Molntjänster kräver nya säkerhet paradigm som är okända för många användare, databasadministratörer och programmerare. Vissa organisationer kan därför tveka att implementera en molninfrastruktur för datahantering på grund av upplevd säkerhetsrisker. Mycket av det här problem kan vara undvikas genom en bättre förståelse för säkerhetsfunktioner som är inbyggda i Microsoft Azure och Microsoft Azure SQL Database.

## <a name="checklist"></a>Checklista
Vi rekommenderar att du läser den [säkerhetsmetoder för Azure-databas](https://docs.microsoft.com/azure/security/azure-database-security-best-practices) artikel innan du granska den här checklistan. Du kommer att kunna få ut mesta möjliga av den här checklistan när du är medveten om bästa praxis. Du kan sedan använda den här checklistan för att se till att du har adresserat viktiga problem i Azure database-säkerhet.


|Checklista för kategori| Beskrivning|
| ------------ | -------- |
|**Skydda Data**||
| <br> Kryptering i rörelse/överföring| <ul><li>[Transport Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol), för kryptering av data när data flyttas till nätverk.</li><li>Databasen kräver säker kommunikation från klienter baserat på de [TDS (Tabular Data Stream)](https://msdn.microsoft.com/en-in/library/dd357628.aspx) protocol över TLS (Transport Layer Security).</li></ul> |
|<br>Vilande kryptering| <ul><li>[Transparent datakryptering](http://go.microsoft.com/fwlink/?LinkId=526242)när inaktiva data lagras fysiskt i någon digital form.</li></ul>|
|**Kontrollera åtkomst**||  
|<br> Åtkomst till databasen | <ul><li>[Autentisering](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) (Azure Active Directory-autentisering) AD för autentisering använder identiteter som hanteras av Azure Active Directory.</li><li>[Auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) ge användare de minsta behörigheter som krävs.</li></ul> |
|<br>Programåtkomst| <ul><li>[Rad level Security](https://msdn.microsoft.com/library/dn765131) (med hjälp av säkerhetsprinciper, samtidigt som begränsar radnivå åtkomst baserat på en användares identitet, roll eller körningen kontext).</li><li>[Dynamisk Datamaskering](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) (med hjälp av behörighet & principen begränsar exponering av känsliga data genom att maskera till icke-privilegierade användare)</li></ul>|
|**Proaktiv övervakning**||  
| <br>Spåra & identifiering| <ul><li>[Granskning](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) spårar databashändelser och skriver dem till en granskningslogg / aktivitet log din [Azure Storage-konto](https://docs.microsoft.com/azure/storage/storage-create-storage-account).</li><li>Spåra Azure Database hälsotillstånd med [aktivitetsloggar för Azure-Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).</li><li>[Hotidentifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen. </li></ul> |
|<br>Azure Security Center| <ul><li>[Data övervakning](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-databases) använda Azure Security Center som en centraliserad säkerhet övervakningslösning för SQL och andra Azure-tjänster.</li></ul>|       

## <a name="conclusion"></a>Sammanfattning
Azure-databas är en robust databasplattform, med en fullständig uppsättning funktioner som uppfyller kraven för många organisationens och regelmässig efterlevnad. Du kan enkelt skydda data genom att kontrollera fysisk tillgång till dina data och använda en mängd olika alternativ för datasäkerhet på fil-, kolumn- eller radnivå med Transparent datakryptering, cellnivå kryptering eller säkerhet på radnivå. Alltid gör krypterat också att åtgärder mot krypterade data, förenkla processen för programuppdateringar. I sin tur ger åtkomst till granskningsloggar för SQL-databas aktivitet dig den information du behöver, så att du vet hur och när data används.

## <a name="next-steps"></a>Nästa steg
Med några få enkla steg kan du förbättra databasens skydd mot obehöriga användare och obehörig åtkomst. I den här självstudien får du lära du dig att:

- Ställ in [regler i brandväggen](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) för servern och eller-databasen.
- Skydda dina data med [kryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- Aktivera [SQL Database auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing).


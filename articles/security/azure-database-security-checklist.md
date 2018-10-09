---
title: Checklista för Azure database-säkerhet | Microsoft Docs
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
ms.openlocfilehash: 7682a733c5630c16d9684ec3cb9e7757b03a8b1b
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855943"
---
# <a name="azure-database-security-checklist"></a>Checklista för Azure database-säkerhet

För att förbättra säkerheten, Azure-databasen innehåller ett antal inbyggda säkerhetskontroller som du kan använda för att begränsa och kontrollera åtkomsten.

Exempel på dessa är:

-   En brandvägg som låter dig skapa [brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) begränsar anslutning efter IP-adress
-   Brandväggen på servernivå kan nås från Azure-portalen
-   Regler för brandväggen på databasnivå kan nås från SSMS
-   Säker anslutning till databasen med säker anslutningssträngar
-   Använda access management
-   Datakryptering
-   Granskning av SQL Database
-   Hotidentifieringen i SQL Database

## <a name="introduction"></a>Introduktion
Molnbaserad databehandling kräver ny security paradigm som är okända för många programanvändare, databasadministratörer och programmerare. Vissa organisationer är därför tveksamma att implementera en molninfrastruktur för hantering av på grund av upplevd säkerhetsrisker. Mycket av det här problemet kan dock lindras genom en bättre förståelse för säkerhetsfunktioner som är inbyggda i Microsoft Azure och Microsoft Azure SQL Database.

## <a name="checklist"></a>Checklista
Vi rekommenderar att du läser den [säkerhetsmetoder för Azure Database](https://docs.microsoft.com/azure/security/azure-database-security-best-practices) artikeln innan du granska den här checklistan. Du kommer att kunna få ut det mesta av den här checklistan när du förstår de bästa metoderna. Du kan sedan använda den här checklistan för att se till att du har adresserat viktiga problem i Azure database-säkerhet.


|Checklista för kategori| Beskrivning|
| ------------ | -------- |
|**Skydda Data**||
| <br> Kryptering under överföring/överföring| <ul><li>[Transport Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol), för kryptering av data när data flyttas till nätverk.</li><li>Databasen kräver säker kommunikation från klienter baserat på den [TDS (Tabular Data Stream)](https://msdn.microsoft.com/en-in/library/dd357628.aspx) protokollet via TLS (Transport Layer Security).</li></ul> |
|<br>Vilande kryptering| <ul><li>[Transparent datakryptering](http://go.microsoft.com/fwlink/?LinkId=526242)när inaktiva data lagras fysiskt i någon digital form.</li></ul>|
|**Kontrollera åtkomst**||  
|<br> Åtkomst till databasen | <ul><li>[Autentisering](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) (Azure Active Directory-autentisering) AD-autentisering använder identiteter som hanteras av Azure Active Directory.</li><li>[Auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) och ge användare så få behörigheter som krävs.</li></ul> |
|<br>Programåtkomst| <ul><li>[Rad säkerhet på](https://msdn.microsoft.com/library/dn765131) (med hjälp av säkerhetsprinciper, samtidigt som begränsar på radnivå åtkomst baserat på en användares identitet, roll eller körning kontext).</li><li>[Dynamisk Datamaskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) (med hjälp av behörighet och principen begränsar exponering av känsliga data genom att maskera den för icke-privilegierade användare)</li></ul>|
|**Proaktiv övervakning**||  
| <br>Spåra och identifiera| <ul><li>[Granskning](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) spårar databashändelser och skriver dem till en granskningslogg / aktivitetslogg din [Azure Storage-konto](https://docs.microsoft.com/azure/storage/storage-create-storage-account).</li><li>Spåra Azure-databas hälsa med [Azure Monitor-aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).</li><li>[Hotidentifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen. </li></ul> |
|<br>Azure Security Center| <ul><li>[Övervakning av data](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-databases) via Azure Security Center som en centraliserad säkerhet övervakningslösning för SQL och andra Azure-tjänster.</li></ul>|       

## <a name="conclusion"></a>Sammanfattning
Azure Database är en robust plattform, med en hel rad säkerhetsfunktioner som uppfyller kraven för många organisationens och regelmässig efterlevnad. Du kan enkelt skydda data genom att kontrollera fysisk åtkomst till dina data och använda en mängd olika alternativ för datasäkerhet på fil-, kolumn- eller radnivå med Transparent datakryptering, kryptering på cellnivå eller säkerhet på radnivå. Alltid kan krypterad också åtgärder mot krypterade data, förenkla processen för programuppdateringar. I sin tur ger åtkomst till granskningsloggar för SQL-Databasaktivitet dig den information du behöver, så att du vet hur och när data behöver nås.

## <a name="next-steps"></a>Nästa steg
Med några få enkla steg kan du förbättra databasens skydd mot obehöriga användare och obehörig åtkomst. I den här självstudien får du lära du dig att:

- Konfigurera [brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) för din server och eller en databas.
- Skydda dina data med [kryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- Aktivera [SQL Database-granskning](https://docs.microsoft.com/azure/sql-database/sql-database-auditing).


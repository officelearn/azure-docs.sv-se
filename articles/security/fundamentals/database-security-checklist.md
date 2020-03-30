---
title: Checklista för azure-databassäkerhet| Microsoft-dokument
description: Den här artikeln innehåller en uppsättning checklistor för Azure-databassäkerhet.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: d9283a36d5f7ccb82b2cc211485487d5a3dcce7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79201033"
---
# <a name="azure-database-security-checklist"></a>Checklista för azure-databassäkerhet

För att förbättra säkerheten innehåller Azure Database ett antal inbyggda säkerhetskontroller som du kan använda för att begränsa och kontrollera åtkomst.

Exempel på dessa är:

-    En brandvägg som gör att du kan skapa [brandväggsregler](../../sql-database/sql-database-firewall-configure.md) som begränsar anslutningen via IP-adress,
-    Brandvägg på servernivå som är tillgänglig från Azure-portalen
-    Brandväggsregler på databasnivå som är tillgängliga från SSMS
-    Säker anslutning till databasen med hjälp av säkra anslutningssträngar
-    Använda åtkomsthantering
-    Datakryptering
-    Granskning av SQL Database
-    Identifiering av HOT I SQL Database

## <a name="introduction"></a>Introduktion
Cloud computing kräver nya säkerhetsparadigmer som är okända för många programanvändare, databasadministratörer och programmerare. Som ett resultat är vissa organisationer tveksamma till att implementera en molninfrastruktur för datahantering på grund av upplevda säkerhetsrisker. Mycket av det här problemet kan dock lindras genom en bättre förståelse av de säkerhetsfunktioner som är inbyggda i Microsoft Azure och Microsoft Azure SQL Database.

## <a name="checklist"></a>Checklista
Vi rekommenderar att du läser artikeln [om bästa praxis för Azure Database Security Innan](database-best-practices.md) du granskar den här checklistan. Du kommer att kunna få ut det mesta av denna checklista när du förstår de bästa metoderna. Du kan sedan använda den här checklistan för att se till att du har åtgärdat de viktiga problemen i Azure-databasens säkerhet.


|Kategori för checklista| Beskrivning|
| ------------ | -------- |
|**Skydda data**||
| <br> Kryptering i rörelse/kollektivtrafik| <ul><li>[Transport Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol), för datakryptering när data flyttar till nätverken.</li><li>Databasen kräver säker kommunikation från klienter baserat på [TDS-protokollet (Tabelldataström)](https://msdn.microsoft.com/library/dd357628.aspx) via TLS (Transport Layer Security).</li></ul> |
|<br>Vilande kryptering| <ul><li>[Transparent datakryptering](https://go.microsoft.com/fwlink/?LinkId=526242), när inaktiva data lagras fysiskt i någon digital form.</li></ul>|
|**Styr åtkomsten**||  
|<br> Åtkomst till databasen | <ul><li>[Ad-autentisering](../../sql-database/sql-database-manage-logins.md) (Azure Active Directory Authentication) ad-autentisering använder identiteter som hanteras av Azure Active Directory.</li><li>[Auktorisering](../../sql-database/sql-database-manage-logins.md) ger användarna de lägsta behörigheter som krävs.</li></ul> |
|<br>Tillgång till program| <ul><li>[Säkerhet på radnivå](https://msdn.microsoft.com/library/dn765131) (Med hjälp av säkerhetsprinciper, samtidigt som du begränsar åtkomst på radnivå baserat på en användares identitet, roll eller körningskontext).</li><li>[Dynamisk datamaskering](../../sql-database/sql-database-dynamic-data-masking-get-started.md) (Med hjälp av behörighet & princip, begränsar känslig dataexponering genom att maskera den till icke-privilegierade användare)</li></ul>|
|**Proaktiv övervakning**||  
| <br>Spårning & upptäcka| <ul><li>[Granskning](../../sql-database/sql-database-auditing.md) spårar databashändelser och skriver dem till en granskningslogg/aktivitetslogg i ditt [Azure Storage-konto](../../storage/common/storage-create-storage-account.md).</li><li>Spåra Azure Database-hälsotillståndet med hjälp av [Azure Monitor-aktivitetsloggar](../../azure-monitor/platform/platform-logs-overview.md).</li><li>[Hotidentifiering](../../sql-database/sql-database-threat-detection.md) identifierar avvikande databasaktiviteter som anger potentiella säkerhetshot mot databasen. </li></ul> |
|<br>Azure Security Center| <ul><li>[Övervakning av data](../../security-center/security-center-enable-auditing-on-sql-databases.md) Använd Azure Security Center som en centraliserad säkerhetsövervakningslösning för SQL och andra Azure-tjänster.</li></ul>|        

## <a name="conclusion"></a>Slutsats
Azure Database är en robust databasplattform med ett komplett utbud av säkerhetsfunktioner som uppfyller många organisatoriska och regulatoriska efterlevnadskrav. Du kan enkelt skydda data genom att styra den fysiska åtkomsten till dina data och använda en mängd olika alternativ för datasäkerhet på fil-, kolumn- eller radnivå med transparent datakryptering, kryptering på cellnivå eller säkerhet på radnivå. Alltid krypterad möjliggör också åtgärder mot krypterade data, vilket förenklar processen för programuppdateringar. I sin tur ger åtkomst till granskningsloggar av SQL Database-aktivitet dig den information du behöver, så att du kan veta hur och när data används.

## <a name="next-steps"></a>Nästa steg
Med några få enkla steg kan du förbättra databasens skydd mot obehöriga användare och obehörig åtkomst. I den här självstudien får du lära du dig att:

- Konfigurera [brandväggsregler](../../sql-database/sql-database-firewall-configure.md) för servern och eller databasen.
- Skydda dina data med [kryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- Aktivera [GRANSKNING AV SQL Database](../../sql-database/sql-database-auditing.md).


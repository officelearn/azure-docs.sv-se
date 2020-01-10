---
title: Check lista för Azure Database Security | Microsoft Docs
description: Den här artikeln innehåller en uppsättning check lista för Azure Database Security.
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
ms.openlocfilehash: 9298b802417f5a7162ef74bb0f7906144aae4e35
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75745705"
---
# <a name="azure-database-security-checklist"></a>Check lista för Azure Database Security

För att förbättra säkerheten innehåller Azure Database ett antal inbyggda säkerhets kontroller som du kan använda för att begränsa och kontrol lera åtkomst.

Dessa är:

-   En brand vägg som gör att du kan skapa [brand Väggs regler](../../sql-database/sql-database-firewall-configure.md) som begränsar anslutningarna med IP-adress,
-   Brand vägg för server nivå som är tillgänglig från Azure Portal
-   Brand Väggs regler på databas nivå som är tillgängliga från SSMS
-   Säker anslutning till databasen med hjälp av säkra anslutnings strängar
-   Använd åtkomst hantering
-   Datakryptering
-   Granskning av SQL Database
-   SQL Database hot identifiering

## <a name="introduction"></a>Introduktion
Molnbaserad data behandling kräver nya säkerhets paradigm som är okända för många program användare, databas administratörer och programmerare. Därför är vissa organisationer tveka för att implementera en moln infrastruktur för data hantering på grund av uppfattade säkerhets risker. Mycket av detta kan dock minskas genom en bättre förståelse av säkerhetsfunktionerna som är inbyggda i Microsoft Azure och Microsoft Azure SQL Database.

## <a name="checklist"></a>Checklista
Vi rekommenderar att du läser artikeln [metod tips för Azure Database Security](database-best-practices.md) innan du läser igenom check listan. Du kommer att kunna få ut det mesta av check listan när du förstår de bästa metoderna. Du kan sedan använda den här check listan för att kontrol lera att du har åtgärdat de viktiga problemen i Azure Database Security.


|Check lista kategori| Beskrivning|
| ------------ | -------- |
|**Skydda data**||
| <br> Kryptering i rörelse/överföring| <ul><li>[Transport Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol)för data kryptering när data flyttas till nätverken.</li><li>Databasen kräver säker kommunikation från klienter som baseras på [TDS-protokollet (tabell data ström)](https://msdn.microsoft.com/library/dd357628.aspx) över TLS (Transport Layer Security).</li></ul> |
|<br>Vilande kryptering| <ul><li>[Transparent datakryptering](https://go.microsoft.com/fwlink/?LinkId=526242)när inaktiva data lagras fysiskt i ett digitalt formulär.</li></ul>|
|**Kontrol lera åtkomst**||  
|<br> Åtkomst till databasen | <ul><li>[Autentisering](../../sql-database/sql-database-control-access.md) (Azure Active Directory autentisering) AD-autentisering använder identiteter som hanteras av Azure Active Directory.</li><li>[Auktorisera](../../sql-database/sql-database-control-access.md) användare de lägsta privilegier som krävs.</li></ul> |
|<br>Program åtkomst| <ul><li>[Säkerhet på radnivå](https://msdn.microsoft.com/library/dn765131) (med hjälp av säkerhets principer, samtidigt som den begränsar åtkomsten på radnivå baserat på en användares identitet, roll eller körnings kontext).</li><li>[Dynamisk data maskning](../../sql-database/sql-database-dynamic-data-masking-get-started.md) (med behörighet & principen begränsar känslig data exponering genom att maskera den till icke-privilegierade användare)</li></ul>|
|**Proaktiv övervakning**||  
| <br>Spåra & att identifiera| <ul><li>[Granskning](../../sql-database/sql-database-auditing.md) spårar databas händelser och skriver dem till en Gransknings logg/aktivitets logg i ditt [Azure Storage konto](../../storage/common/storage-create-storage-account.md).</li><li>Spåra Azures databas hälsa med [Azure Monitor aktivitets loggar](../../azure-monitor/platform/platform-logs-overview.md).</li><li>[Hot identifiering](../../sql-database/sql-database-threat-detection.md) identifierar avvikande databas aktiviteter som indikerar potentiella säkerhetshot mot databasen. </li></ul> |
|<br>Azure Security Center| <ul><li>[Data övervakning](../../security-center/security-center-enable-auditing-on-sql-databases.md) Använd Azure Security Center som en centraliserad lösning för säkerhets övervakning för SQL och andra Azure-tjänster.</li></ul>|       

## <a name="conclusion"></a>Slutsats
Azure Database är en robust databas plattform med en fullständig uppsättning säkerhetsfunktioner som uppfyller många krav på organisation och regelefterlevnad. Du kan enkelt skydda data genom att kontrol lera den fysiska åtkomsten till dina data och använda en mängd olika alternativ för data säkerhet på fil-, kolumn-eller rad nivå med transparent datakryptering, kryptering på cell nivå eller säkerhet på radnivå. Always Encrypted aktiverar också åtgärder mot krypterade data, vilket fören klar processen med program uppdateringar. I sin tur ger åtkomst till gransknings loggar för SQL Database-aktivitet dig den information du behöver, så att du kan veta hur och när data nås.

## <a name="next-steps"></a>Nästa steg
Med några få enkla steg kan du förbättra databasens skydd mot obehöriga användare och obehörig åtkomst. I den här självstudien får du lära du dig att:

- Konfigurera [brand Väggs regler](../../sql-database/sql-database-firewall-configure.md) för servern och eller databasen.
- Skydda dina data med [kryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- Aktivera [SQL Database granskning](../../sql-database/sql-database-auditing.md).


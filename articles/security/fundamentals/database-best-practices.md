---
title: Metodtips för databassäkerhet – Microsoft Azure
description: Den här artikeln innehåller en uppsättning metodtips för Azure-databassäkerhet.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 0f2e0257c5bf855b0d9be61c43b68b4e30b3d80d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125114"
---
# <a name="azure-database-security-best-practices"></a>Metodtips för databassäkerhet
I den här artikeln beskrivs metodtips för databassäkerhet.

De bästa metoderna baseras på konsensus och de fungerar med aktuella Azure-plattformsfunktioner och funktionsuppsättningar. Åsikter och teknik förändras med tiden och denna artikel uppdateras regelbundet för att återspegla dessa förändringar.

## <a name="secure-databases"></a>Säkra databaser
Säkerhet är ett stort problem för att hantera databaser och det har alltid varit en prioritet för [Azure SQL Database](../../sql-database/index.yml). Dina databaser kan vara tätt säkrade för att uppfylla de flesta lagstadgade krav eller säkerhetskrav, inklusive HIPAA, ISO 27001/27002 och PCI DSS-nivå 1. En aktuell lista över certifieringar för säkerhetsefterlevnad finns på [Webbplatsen Microsoft Trust Center](https://azure.microsoft.com/support/trust-center/services/). Du kan också välja att placera dina databaser i specifika Azure-datacenter baserat på lagstadgade krav.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Använda brandväggsregler för att begränsa databasåtkomst
Microsoft Azure SQL Database tillhandahåller en relationsdatabastjänst för Azure och andra internetbaserade program. För att ge åtkomstsäkerhet styr SQL Database åtkomst med:

- Brandväggsregler som begränsar anslutningen via IP-adress.
- Autentiseringsmekanismer som kräver att användare bevisar sin identitet.
- Auktoriseringsmekanismer som begränsar användare till specifika åtgärder och data.

Brandväggar förhindrar all åtkomst till databasservern tills du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.

Följande bild visar var du anger en serverbrandvägg i SQL Database:

![Brandväggsregler](./media/database-best-practices/azure-database-security-best-practices-Fig1.png)

Azure SQL Database-tjänsten är endast tillgänglig via TCP-port 1433. Om du vill komma åt en SQL-databas från datorn kontrollerar du att klientdatorbrandväggen tillåter utgående TCP-kommunikation på TCP-port 1433. Blockera inkommande anslutningar på TCP-port 1433 med hjälp av brandväggsregler, om du inte behöver dessa anslutningar för andra program.

Som en del av anslutningsprocessen omdirigeras anslutningar från virtuella Azure-datorer till en IP-adress och port som är unika för varje arbetarroll. Portnumret är i intervallet 11000 till 11999. Mer information om TCP-portar finns i [Portar över 1433 för ADO.NET 4.5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

Mer information om brandväggsregler i SQL Database finns [SQL Database-brandväggsregler](../../sql-database/sql-database-firewall-configure.md).

> [!Note]
> Förutom IP-regler hanterar brandväggen regler för virtuella nätverk. Slutpunkter för virtuella nätverk baseras på slutpunkter för virtuella nätverkstjänster. Virtuella nätverksregler kan vara att föredra framför IP-regler i vissa fall. Mer information finns i [Slutpunkter och regler för virtuella nätverkstjänster för Azure SQL Database](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="enable-database-authentication"></a>Aktivera databasautentisering
SQL Database stöder två typer av autentisering, SQL Server-autentisering och Azure AD-autentisering.

### <a name="sql-server-authentication"></a>*SQL Server-autentisering*

Här är några av fördelarna:

- Det gör att SQL Database kan stödja miljöer med blandade operativsystem, där alla användare inte autentiseras av en Windows-domän.
- Gör att SQL Database stöder äldre program och partnerprogram som kräver SQL Server-autentisering.
- Tillåter användare att ansluta från okända eller ej betrodda domäner. Ett exempel är ett program där etablerade kunder ansluter med tilldelade SQL Server-inloggningar för att få status för sina order.
- Tillåter att SQL Database stöder webbaserade program där användare skapar sina egna identiteter.
- Tillåter programutvecklare att distribuera sina program med hjälp av en komplex behörighetshierarki baserad på kända, förinställda SQL Server-inloggningar.

> [!NOTE]
> SQL Server-autentisering kan inte använda Kerberos säkerhetsprotokoll.

Om du använder SQL Server-autentisering måste du:

- Hantera de starka autentiseringsuppgifterna själv.
- Skydda autentiseringsuppgifterna i anslutningssträngen.
- (Potentiellt) skydda autentiseringsuppgifterna som skickas över nätverket från webbservern till databasen. Mer information finns [i Så här ansluter du till SQL Server med SQL-autentisering i ASP.NET 2.0](/previous-versions/msp-n-p/ff648340(v=pandp.10)).

### <a name="azure-active-directory-ad-authentication"></a>*Ad-autentisering (Azure Active Directory)*
Azure AD-autentisering är en mekanism för anslutning till Azure SQL Database och [SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) med hjälp av identiteter i Azure AD. Med Azure AD-autentisering kan du hantera identiteterna för databasanvändare och andra Microsoft-tjänster på en central plats. Central ID-hantering ger en enda plats för hantering av databasanvändare och förenklar behörighetshanteringen.

> [!NOTE]
> Vi rekommenderar användning av Azure AD-autentisering över användningen av SQL Server-autentisering.

Här är några av fördelarna:

- Det ger ett alternativ till SQL Server-autentisering.
- Det hjälper till att stoppa spridningen av användaridentiteter över databasservrar.
- Det tillåter lösenordsrotation på ett enda ställe.
- Kunder kan hantera databasbehörigheter med hjälp av externa (Azure AD) grupper.
- Det kan eliminera lagring av lösenord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure Active Directory.
- Den använder inneslutna databasanvändare för att autentisera identiteter på databasnivå.
- Den stöder tokenbaserad autentisering för program som ansluter till SQL Database.
- Den stöder AD FS (domänfederation) eller inbyggd användar-/lösenordsautentisering för en lokal Azure Active Directory-instans utan domänsynkronisering.
- Azure AD stöder anslutningar från SQL Server Management Studio som använder Universell Active Directory-autentisering, vilket inkluderar multifaktorautentisering. Multifaktorautentisering ger stark autentisering med en rad verifieringsalternativ – telefonsamtal, sms, smartkort med PIN-kod eller avisering av mobilappar. Mer information finns i [SSMS-stöd för Azure AD MultiFaktor-autentisering med SQL Database och SQL Data Warehouse](../../sql-database/sql-database-ssms-mfa-authentication.md).

Konfigurationsstegen innehåller följande procedurer för att konfigurera och använda Azure AD-autentisering:

- Skapa och fyll i Azure AD.
- Valfritt: Associera eller ändra Active Directory-instansen som för närvarande är associerad med din Azure-prenumeration.
- Skapa en Azure Active Directory-administratör för Azure SQL Database eller [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Konfigurera klientdatorerna.
- Skapa inneslutna databasanvändare i databasen mappade till Azure AD-identiteter.
- Anslut till databasen med hjälp av Azure AD-identiteter.

Du hittar detaljerad information i [Använd Azure Active Directory-autentisering för autentisering med SQL Database, Hanterad instans eller SQL Data Warehouse](../../sql-database/sql-database-aad-authentication.md).

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Skydda dina data med hjälp av kryptering och säkerhet på radnivå
[Azure SQL Database transparent datakryptering](../../sql-database/transparent-data-encryption-azure-sql.md) hjälper till att skydda data på disk och skyddar mot obehörig åtkomst till maskinvara. TDE utför realtidskryptering och realtidsdekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler i vila, utan att några ändringar krävs i programmet. Transparent datakryptering krypterar lagringen av en hel databas med hjälp av en symmetrisk nyckel som kallas databaskrypteringsnyckeln.

Även när hela lagringen är krypterad är det viktigt att även kryptera själva databasen. Detta är ett genomförande av den djupgående försvarsstrategin för dataskydd. Om du använder Azure SQL Database och vill skydda känsliga data (till exempel kreditkorts- eller personnummer) kan du kryptera databaser med FIPS 140-2 validerad 256-bitars AES-kryptering. Denna kryptering uppfyller kraven i många branschstandarder (till exempel HIPAA och PCI).

Filer relaterade till [BPE (Buffertpool Extension)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) krypteras inte när du krypterar en databas med hjälp av transparent datakryptering. Du måste använda krypteringsverktyg på filsystemnivå som [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) eller [EFS (Encrypting File System)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10)) för BPE-relaterade filer.

Eftersom en behörig användare som en säkerhetsadministratör eller en databasadministratör kan komma åt data även om databasen är krypterad med transparent datakryptering, bör du också följa dessa rekommendationer:

- Aktivera SQL Server-autentisering på databasnivå.
- Använd Azure AD-autentisering med hjälp av [RBAC-roller](/azure/role-based-access-control/overview).
- Se till att användare och program använder separata konton för att autentisera. På så sätt kan du begränsa de behörigheter som beviljas användare och program och minska risken för skadlig aktivitet.
- Implementera säkerhet på databasnivå med hjälp av fasta databasroller (till exempel db_datareader eller db_datawriter). Du kan också skapa anpassade roller för ditt program för att bevilja explicita behörigheter till markerade databasobjekt.

Om du vill ha andra sätt att skydda dina data bör du tänka på följande:

- [Kryptering på cellnivå](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) för att kryptera vissa kolumner eller även celler av data med olika krypteringsnycklar.
- [Alltid krypterad](/sql/relational-databases/security/encryption/always-encrypted-database-engine), vilket gör det möjligt för klienter att kryptera känsliga data i klientprogram och aldrig avslöja krypteringsnycklarna till Database Engine (SQL Database eller SQL Server). Därför ger Alltid krypterad en separation mellan dem som äger data (och kan visa dem) och de som hanterar data (men bör inte ha någon åtkomst).
- [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security), som gör det möjligt för kunder att styra åtkomsten till rader i en databastabell baserat på egenskaperna hos den användare som kör en fråga. (Exempelkännetecken är gruppmedlemskap och utförandekontext.)

Organisationer som inte använder kryptering på databasnivå kan vara mer mottagliga för attacker som komprometterar data som finns i SQL-databaser.

Du kan läsa mer om SQL Database transparent datakryptering genom att läsa artikeln [Transparent datakryptering med Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="enable-database-auditing"></a>Aktivera databasgranskning
Granska en instans av SQL Server Database Engine eller en enskild databas innebär spårning och loggning händelser. För SQL Server kan du skapa granskningar som innehåller specifikationer för händelser och specifikationer på servernivå för händelser på databasnivå. Granskade händelser kan skrivas till händelseloggarna eller till granskningsfiler.

Det finns flera granskningsnivåer för SQL Server, beroende på myndighets- eller standardkrav för din installation. SQL Server-granskning innehåller verktyg och processer för att aktivera, lagra och visa granskningar på olika server- och databasobjekt.

[Granskning av Azure SQL Database](/azure/sql-database/sql-database-auditing) spårar databashändelser och skriver dem till en granskningslogg i ditt Azure-lagringskonto.

Granskning kan hjälpa dig att upprätthålla regelefterlevnad, förstå databasaktivitet och hitta avvikelser och avvikelser som kan peka på affärsproblem eller säkerhetsöverträdelser. Granskning underlättar efterlevnaden av efterlevnadsstandarder men garanterar inte efterlevnad.

Mer information om databasgranskning och hur du aktiverar den finns i [Komma igång med granskning av SQL-databas](/azure/sql-database/sql-database-auditing).

## <a name="enable-database-threat-detection"></a>Aktivera identifiering av databashot
Hotskyddet går utöver detektering. Skydd mot databashot omfattar:

- Upptäcka och klassificera dina känsligaste data så att du kan skydda dina data.
- Implementera säkra konfigurationer i databasen så att du kan skydda databasen.
- Identifiera och svara på potentiella hot när de inträffar så att du snabbt kan svara och åtgärda.

**Bästa praxis**: Identifiera, klassificera och märk känsliga data i dina databaser.   
**Detalj:** Klassificera data i DIN SQL-databas genom att aktivera [dataidentifiering och klassificering](/azure/sql-database/sql-database-data-discovery-and-classification) i Azure SQL Database. Du kan övervaka åtkomsten till känsliga data i Azure-instrumentpanelen eller hämtningsrapporterna.

**Bästa praxis**: Spåra databassårbarheter så att du proaktivt kan förbättra databassäkerheten.   
**Detalj**: Använd azure SQL Database [Vulnerability](/azure/sql-database/sql-vulnerability-assessment) Assessment-tjänsten, som söker efter potentiella databassäkerhetsproblem. Tjänsten använder en kunskapsbas med regler som flaggar säkerhetsproblem och visar avvikelser från bästa praxis, till exempel felkonfigurationer, överdrivna behörigheter och oskyddade känsliga data.

Reglerna baseras på Microsofts bästa praxis och fokuserar på de säkerhetsproblem som utgör de största riskerna för databasen och dess värdefulla data. De täcker både problem på databasnivå och säkerhetsproblem på servernivå, till exempel inställningar för serverbrandvägg och behörigheter på servernivå. Dessa regler representerar också många av de krav som regleringsorganen har för att uppfylla sina efterlevnadsstandarder.

**Bästa praxis**: Aktivera identifiering av hot.  
**Detalj:** Aktivera Azure SQL Database [Threat Detection](/azure/sql-database/sql-database-threat-detection) för att få säkerhetsaviseringar och rekommendationer om hur du undersöker och minskar hot. Du får aviseringar om misstänkta databasaktiviteter, potentiella sårbarheter och SQL-injektionsattacker samt avvikande databasåtkomst och frågemönster.

[Avancerat skydd mot hot](/azure/sql-database/sql-advanced-threat-protection) är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Den innehåller de tjänster som nämndes tidigare: Data Discovery and Classification, Vulnerability Assessment och Threat Detection. Det ger en enda plats för att aktivera och hantera dessa funktioner.

Genom att aktivera dessa funktioner kan du:

- Uppfylla standarder för datasekretess och efterlevnadskrav.
- Kontrollera åtkomsten till dina databaser och skärpa deras säkerhet.
- Övervaka en dynamisk databasmiljö där ändringar är svåra att spåra.
- Identifiera och reagera på potentiella hot.

Dessutom integrerar hotidentifiering aviseringar med Azure Security Center för en central vy av säkerhetstillståndet för alla dina Azure-resurser.

## <a name="next-steps"></a>Nästa steg
Se metodtips och mönster för [Azure-säkerhet](best-practices-and-patterns.md) för fler metodtips för säkerhet som ska användas när du utformar, distribuerar och hanterar dina molnlösningar med hjälp av Azure.

Följande resurser är tillgängliga för att ge mer allmän information om Azure-säkerhet och relaterade Microsoft-tjänster:
* [Blogg för Azure Security Team](https://blogs.msdn.microsoft.com/azuresecurity/) – för aktuell information om det senaste inom Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – där Microsofts säkerhetsproblem, inklusive problem med Azure, kan rapporteras eller via e-post tillsecure@microsoft.com

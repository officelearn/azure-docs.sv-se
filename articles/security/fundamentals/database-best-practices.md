---
title: Metod tips för databas säkerhet – Microsoft Azure
description: Den här artikeln innehåller en uppsättning metod tips för Azure Database Security.
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
ms.openlocfilehash: 72b15d77baedae318d4503f2d481b08202730459
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927988"
---
# <a name="azure-database-security-best-practices"></a>Metodtips för databassäkerhet
Den här artikeln beskriver metod tips för databas säkerhet.

Bästa praxis bygger på en uppfattning om yttrandet och de fungerar med aktuella funktioner i Azure-plattformen och-funktions uppsättningar. Åsikter och tekniker förändras med tiden och den här artikeln uppdateras regelbundet för att avspegla ändringarna.

## <a name="secure-databases"></a>Säkra databaser
Säkerhet är viktigt för att hantera databaser och har alltid varit en prioritet för [Azure SQL Database](../../sql-database/index.yml). Dina databaser kan vara tätt skyddade för att tillgodose de flesta regler eller säkerhets krav, inklusive HIPAA, ISO 27001/27002 och PCI DSS nivå 1. En aktuell lista över certifieringar för säkerhetskompatibilitet finns på [webbplatsen för Microsoft säkerhets Center](https://azure.microsoft.com/support/trust-center/services/). Du kan också välja att placera dina databaser i särskilda Azure-datacenter baserat på myndighets krav.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Använd brand Väggs regler för att begränsa databas åtkomst
Microsoft Azure SQL Database tillhandahåller en Relations databas tjänst för Azure och andra Internetbaserade program. För att ge åtkomst säkerhet, SQL Database kontrol lera åtkomst med:

- Brand Väggs regler som begränsar anslutningarna med IP-adress.
- Autentiseringsmekanismer som kräver att användare bekräftar sin identitet.
- Auktoriserings metoder som begränsar användare till vissa åtgärder och data.

Brand väggar förhindrar all åtkomst till din databas server tills du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.

Följande bild visar var du ställer in en server brand vägg i SQL Database:

![Brandväggsregler](./media/database-best-practices/azure-database-security-best-practices-Fig1.png)

Azure SQL Database-tjänsten är endast tillgänglig via TCP-port 1433. För att få åtkomst till en SQL-databas från datorn måste du kontrol lera att klient datorns brand vägg tillåter utgående TCP-kommunikation på TCP-port 1433. Blockera inkommande anslutningar på TCP-port 1433 med hjälp av brand Väggs regler, om du inte behöver dessa anslutningar för andra program.

Som en del av anslutnings processen omdirigeras anslutningar från virtuella Azure-datorer till en IP-adress och port som är unika för varje arbets roll. Portnumret är i intervallet 11000 till 11999. Mer information om TCP-portar finns i [portar utöver 1433 för ADO.NET 4,5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

Mer information om brandväggsregler i SQL Database finns [SQL Database-brandväggsregler](../../sql-database/sql-database-firewall-configure.md).

> [!Note]
> Förutom IP-regler hanterar brand väggen virtuella nätverks regler. Virtuella nätverks regler baseras på tjänst slut punkter för virtuella nätverk. Regler för virtuella nätverk kan vara bättre för IP-regler i vissa fall. Mer information finns i [tjänst slut punkter och regler för virtuella nätverk för Azure SQL Database](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="enable-database-authentication"></a>Aktivera databas-autentisering
SQL Database stöder två typer av autentisering, SQL Server autentisering och Azure AD-autentisering.

### <a name="sql-server-authentication"></a>*SQL Server autentisering*

Här är några av fördelarna:

- Den tillåter SQL Database att stödja miljöer med blandade operativ system, där alla användare inte autentiseras av en Windows-domän.
- Tillåter SQL Database att stödja äldre program och program som tillhandahålls av partner och som kräver SQL Server autentisering.
- Tillåter användare att ansluta från okända eller ej betrodda domäner. Ett exempel är ett program där etablerade kunder ansluter med tilldelade SQL Server inloggningar för att ta emot statusen för deras beställningar.
- Tillåter SQL Database att stödja webbaserade program där användare skapar egna identiteter.
- Gör det möjligt för program varu utvecklare att distribuera sina program med hjälp av en komplex behörighets hierarki baserat på kända, förinställda SQL Server inloggningar.

> [!NOTE]
> SQL Server autentisering kan inte använda Kerberos säkerhets protokollet.

Om du använder SQL Server autentisering måste du:

- Hantera de starka autentiseringsuppgifterna själv.
- Skydda autentiseringsuppgifterna i anslutnings strängen.
- (Möjligen) skydda de autentiseringsuppgifter som skickas via nätverket från webb servern till-databasen. Mer information finns i [så här: Anslut till SQL Server med SQL-autentisering i ASP.NET](/previous-versions/msp-n-p/ff648340(v=pandp.10))2,0.

### <a name="azure-active-directory-ad-authentication"></a>*Azure Active Directory (AD)-autentisering*
Azure AD-autentisering är en mekanism för att ansluta till Azure SQL Database och [SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) med hjälp av identiteter i Azure AD. Med Azure AD-autentisering kan du hantera identiteter för databas användare och andra Microsoft-tjänster på en central plats. Hantering av central-ID är en enda plats för att hantera databas användare och för att förenkla behörighets hanteringen.

> [!NOTE]
> Vi rekommenderar användningen av Azure AD-autentisering vid användning av SQL Server autentisering.

Här är några av fördelarna:

- Det är ett alternativ till att SQL Server autentisering.
- Det hjälper till att stoppa spridningen av användar identiteter på databas servrar.
- Det tillåter lösen ords rotation på en enda plats.
- Kunder kan hantera databas behörigheter med hjälp av externa (Azure AD) grupper.
- Det kan eliminera lagring av lösen ord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure Active Directory.
- Den använder inneslutna databas användare för att autentisera identiteter på databas nivå.
- Den stöder tokenbaserad autentisering för program som ansluter till SQL Database.
- Det stöder AD FS (domän Federation) eller autentisering med intern användare/lösenordsautentisering för en lokal Azure Active Directory instans utan domän-synkronisering.
- Azure AD stöder anslutningar från SQL Server Management Studio som använder Active Directory Universal Authentication, som innehåller Multi-Factor Authentication. Multi-Factor Authentication ger stark autentisering med ett antal verifierings alternativ – telefonsamtal, textmeddelande, smartkort med PIN-kod eller meddelande om mobilapp. Mer information finns i [SSMS-stöd för Azure AD Multi-Factor Authentication med SQL Database och SQL Data Warehouse](../../sql-database/sql-database-ssms-mfa-authentication.md).

Konfigurations stegen innehåller följande procedurer för att konfigurera och använda Azure AD-autentisering:

- Skapa och fyll i Azure AD.
- Valfritt: Associera eller ändra Active Directory-instansen som är associerad med din Azure-prenumeration.
- Skapa en Azure Active Directory administratör för Azure SQL Database eller [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Konfigurera klient datorerna.
- Skapa inneslutna databas användare i databasen som har mappats till Azure AD-identiteter.
- Anslut till databasen med hjälp av Azure AD-identiteter.

Du hittar detaljerad information i [använda Azure Active Directory autentisering för autentisering med SQL Database, hanterad instans eller SQL Data Warehouse](../../sql-database/sql-database-aad-authentication.md).

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Skydda dina data med hjälp av kryptering och säkerhet på radnivå
[Azure SQL Database transparent data kryptering](../../sql-database/transparent-data-encryption-azure-sql.md) skyddar data på disk och skyddar mot obehörig åtkomst till maskin vara. Den utför kryptering och dekryptering i real tid av databasen, tillhör ande säkerhets kopior och transaktionsloggfiler i vila utan att det krävs några ändringar i programmet. Transparent data kryptering krypterar lagringen av en hel databas med hjälp av en symmetrisk nyckel som kallas databas krypterings nyckel.

Även om hela lagringen är krypterad är det viktigt att även kryptera själva databasen. Det här är en implementering av den djupgående metoden för data skydd. Om du använder Azure SQL Database och vill skydda känsliga data (till exempel kreditkorts nummer eller person nummer) kan du kryptera databaser med FIPS 140-2-verifierad 256-bitars AES-kryptering. Den här krypteringen uppfyller kraven i många bransch standarder (till exempel HIPAA och PCI).

Filer som rör [buffertpooltillägget (funktions växlaren BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) krypteras inte när du krypterar en databas med hjälp av transparent data kryptering. Du måste använda krypterings verktyg på fil system nivå som [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) eller [Krypterande filsystem (EFS)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10)) för funktions växlaren BPE-relaterade filer.

Eftersom en behörig användare som en säkerhets administratör eller databas administratör kan komma åt data även om databasen är krypterad med transparent data kryptering bör du även följa dessa rekommendationer:

- Aktivera SQL Server autentisering på databas nivå.
- Använd Azure AD-autentisering med [RBAC-roller](/azure/role-based-access-control/overview).
- Kontrol lera att användare och program använder separata konton för att autentisera. På så sätt kan du begränsa de behörigheter som beviljas användare och program och minska risken för skadlig aktivitet.
- Implementera säkerhet på databas nivå med hjälp av fasta databas roller (till exempel db_datareader eller db_datawriter). Du kan också skapa anpassade roller för ditt program för att bevilja explicita behörigheter till valda databas objekt.

För andra sätt att skydda dina data kan du tänka på följande:

- [Kryptering på cellnivå](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) för att kryptera vissa kolumner eller även celler av data med olika krypteringsnycklar.
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine), vilket gör att klienter kan kryptera känsliga data i klient program och aldrig avslöja krypterings nycklarna till databas motorn (SQL Database eller SQL Server). Det innebär att Always Encrypted ger en separation mellan de som äger data (och kan visa den) och de som hanterar data (men inte har någon åtkomst).
- [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security), som gör det möjligt för kunder att styra åtkomsten till rader i en databas tabell baserat på egenskaperna för den användare som kör en fråga. (Exempel på egenskaper är grupp medlemskap och körnings kontext.)

Organisationer som inte använder kryptering på databas nivå kan vara mer känsliga för attacker som komprometterar data som finns i SQL-databaser.

Du kan lära dig mer om SQL Database transparent data kryptering genom att läsa artikeln [Transparent datakryptering med Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="enable-database-auditing"></a>Aktivera databas granskning
Granskning av en instans av SQL Server databas motorn eller en enskild databas omfattar spårnings-och loggnings händelser. För SQL Server kan du skapa granskningar som innehåller specifikationer för händelser och specifikationer på server nivå för händelser på databas nivå. Granskade händelser kan skrivas till händelse loggarna eller för att granska filer.

Det finns flera nivåer av granskning för SQL Server, beroende på myndighets krav eller standard krav för din installation. SQL Server granskning innehåller verktyg och processer för att aktivera, lagra och Visa granskningar på olika Server-och databas objekt.

[Azure SQL Database granskning](/azure/sql-database/sql-database-auditing) spårar databas händelser och skriver dem till en Gransknings logg i ditt Azure Storage-konto.

Granskning kan hjälpa dig att upprätthålla regelefterlevnad, förstå databas aktivitet och hitta avvikelser och avvikelser som kan leda till affärs problem eller säkerhets överträdelser. Granskning underlättar efterlevnaden av normerna men garanterar inte efterlevnad.

Mer information om databas granskning och hur du aktiverar det finns i [Kom igång med SQL Database-granskning](/azure/sql-database/sql-database-auditing).

## <a name="enable-database-threat-detection"></a>Aktivera identifiering av databas hot
Hot skyddet går förbi identifieringen. Skydd mot databas hot innehåller:

- Identifiera och klassificera dina mest känsliga data så att du kan skydda dina data.
- Implementera säkra konfigurationer i databasen så att du kan skydda databasen.
- Identifiera och svara på potentiella hot när de inträffar så att du snabbt kan reagera och åtgärda dem.

**Bästa praxis**: Identifiera, klassificera och märk känsliga data i dina databaser.   
**Information**: Klassificera data i din SQL-databas genom att aktivera [identifiering och klassificering av data](/azure/sql-database/sql-database-data-discovery-and-classification) i Azure SQL Database. Du kan övervaka åtkomsten till känsliga data på Azure-instrumentpanelen eller hämta rapporter.

**Bästa praxis**: Spåra sårbarheter i databasen så att du proaktivt kan förbättra databas säkerheten.   
**Information**: Använd tjänsten Azure SQL Database [sårbarhets bedömning](/azure/sql-database/sql-vulnerability-assessment) som söker efter potentiella databas sårbarheter. Tjänsten använder en kunskaps bas för regler som flaggar säkerhets sårbarheter och visar avvikelser från bästa praxis, till exempel felkonfigurationer, för hög behörighet och oskyddade känsliga data.

Reglerna baseras på Microsofts bästa praxis och fokuserar på de säkerhets problem som ger största möjliga risker för din databas och dess värdefulla data. De behandlar både problem på databas nivå och säkerhets problem på server nivå, t. ex. inställningar för Server brand vägg och behörigheter på server nivå. Dessa regler motsvarar också många av de krav som ställs av myndighets myndigheter för att uppfylla sina krav på efterlevnad.

**Bästa praxis**: Aktivera hot identifiering.  
**Information**:  Aktivera Azure SQL Database [hot identifiering](/azure/sql-database/sql-database-threat-detection) för att få säkerhets aviseringar och rekommendationer om hur du undersöker och minimerar hot. Du får aviseringar om misstänkta databas aktiviteter, potentiella sårbarheter och SQL-injektering-attacker, samt avvikande databas åtkomst och fråge mönster.

[Avancerat skydd](/azure/sql-database/sql-advanced-threat-protection) är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Den innehåller de tjänster som nämns ovan: Identifiering och klassificering av data, sårbarhets bedömning och hot identifiering. Det ger en enda plats för att aktivera och hantera dessa funktioner.

Genom att aktivera dessa funktioner kan du:

- Uppfylla data sekretess standarder och regler för efterlevnad av efterlevnad.
- Kontrol lera åtkomsten till dina databaser och skydda säkerheten.
- Övervaka en dynamisk databas miljö där ändringar är svåra att spåra.
- Identifiera och svara på potentiella hot.

Hot identifieringen integrerar dessutom aviseringar med Azure Security Center för en central vy över säkerhets läget för alla dina Azure-resurser.

## <a name="enable-feature-restrictions"></a>Aktivera funktions begränsningar

De data som finns i dina databaser kan exponeras för angripare med hjälp av angrepps vektorer som utnyttjar databas fel och fråge körnings tider. Azure SQL Database innehåller ett antal funktioner för funktions begränsning för att skydda din databas. Läs mer i [SQL Database funktions begränsningar](/azure/sql-database/sql-database-feature-restrictions).

## <a name="next-steps"></a>Nästa steg
Se [metod tips och mönster för Azure-säkerhet](best-practices-and-patterns.md) för att få bättre säkerhets metoder att använda när du utformar, distribuerar och hanterar dina moln lösningar med hjälp av Azure.

Följande resurser är tillgängliga för att ge mer allmän information om Azure-säkerhet och relaterade Microsoft-tjänster:
* [Azure Security Team-bloggen](https://blogs.msdn.microsoft.com/azuresecurity/) – för uppdaterad information om det senaste i Azure-säkerhet
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – där säkerhets problem i Microsoft, inklusive problem med Azure, kan rapporteras eller via e-post tillsecure@microsoft.com

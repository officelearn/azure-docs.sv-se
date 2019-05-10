---
title: Rekommenderade säkerhetsmetoder – Microsoft Azure-databas
description: Den här artikeln innehåller en uppsättning Metodtips för Azure database-säkerhet.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: b4744201f506730303e190783acc60bfaa383720
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409813"
---
# <a name="azure-database-security-best-practices"></a>Metodtips för Azure database-säkerhet
Den här artikeln beskriver Metodtips för databassäkerhet.

De bästa metoderna är baserade på en enhälligt av åsikter och de fungerar med den aktuella Azure-plattformsfunktioner och egenskapsuppsättningar. Andras åsikter och tekniker som ändras med tiden och den här artikeln uppdateras regelbundet att återspegla dessa ändringar.

## <a name="secure-databases"></a>Skydda databaser
Säkerhet är ett viktigt mål för hantering av databaser och den har alltid varit en prioritet för [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/). Dina databaser kan skyddas nära för att hjälpa uppfyller de flesta juridiska eller säkerhetskrav, inklusive HIPAA, ISO 27001/27002 och PCI DSS Level 1. En aktuell lista över security efterlevnadscertifieringar är tillgänglig på den [Microsoft Trust Center plats](https://azure.microsoft.com/support/trust-center/services/). Du kan också välja att placera dina databaser i specifika Azure-datacenter som baseras på myndighetskrav.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Använd brandväggsregler för att begränsa databasåtkomst
Microsoft Azure SQL Database tillhandahåller en relationsdatabastjänst för Azure och andra Internetbaserade program. För att ge åtkomstsäkerhet, kontrollerar SQL Database åtkomsten med:

- Brandväggsregler som begränsar anslutning efter IP-adress.
- Autentiseringsmekanismer som kräver att användare bevisar sin identitet.
- Auktoriseringsmekanismer som begränsar användare till specifika åtgärder och data.

Förhindrar brandväggar all åtkomst till din databasserver tills du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.

I följande bild visas där du anger en serverbrandvägg i SQL-databas:

![Brandväggsregler](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

Azure SQL Database-tjänsten är bara tillgänglig via TCP-port 1433. Se till att klientdatorns brandvägg tillåter utgående TCP-kommunikation på TCP-port 1433 för att komma åt en SQL-databas från datorn. Blockera inkommande anslutningar på TCP-port 1433 med hjälp av brandväggsregler, om du inte behöver de här anslutningarna för andra program.

Som del av anslutningen omdirigeras anslutningar från Azure-datorer till en IP-adress och port som är unika för varje arbetsroll. Portnumret är i intervallet 11000 till 11999. Mer information om TCP-portar finns i [portar utöver 1433 för ADO.NET 4.5](../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

Mer information om brandväggsregler i SQL Database finns [SQL Database-brandväggsregler](../sql-database/sql-database-firewall-configure.md).

> [!Note]
> Förutom IP-regler hanterar virtual network-regler i brandväggen. Virtuella Nätverksregler baseras på vnet-tjänstslutpunkter. Regler för virtuellt nätverk kan vara bättre att IP-regler i vissa fall. Mer information finns i [tjänstslutpunkter i virtuella nätverk och regler för Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="enable-database-authentication"></a>Aktivera Databasautentisering
SQL Database stöder två typer av autentisering, SQL Server-autentisering och Azure AD-autentisering.

### <a name="sql-server-authentication"></a>*SQL Server-autentisering*

Följande: fördelar

- Det gör att SQL-databas till stöd för miljöer med blandad operativsystem, där alla användare inte autentiseras med en Windows-domän.
- Kan SQL-databas till stöd för äldre program och tillhandahålls av partner program som kräver SQL Server-autentisering.
- Tillåter användare att ansluta från okänd eller icke betrodda domäner. Ett exempel är ett program där etablerade kunder ansluta med tilldelade SQL Server-inloggningar för att ta emot status för sina order.
- Kan SQL-databas för webbaserade program där användare kan skapa sina egna identiteter.
- Kan utvecklare distribuera sina program med hjälp av en hierarki för komplexa behörighet baserat på kända, förinställda SQL Server-inloggningar.

> [!NOTE]
> SQL Server-autentisering kan inte använda Kerberos-säkerhetsprotokoll.

Om du använder SQL Server-autentisering, måste du:

- Hantera starka autentiseringsuppgifter själv.
- Skydda autentiseringsuppgifter i anslutningssträngen.
- (Eventuellt) skydda autentiseringsuppgifterna som skickas över nätverket från webbservern till databasen. Mer information finns i [Gör så här: Anslut till SQLServer med SQL-autentisering i ASP.NET 2.0](/previous-versions/msp-n-p/ff648340(v=pandp.10)).

### <a name="azure-active-directory-ad-authentication"></a>*Azure Active Directory (AD)-autentisering*
Azure AD-autentisering är en mekanism för att ansluta till Azure SQL Database och [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) med hjälp av identiteter i Azure AD. Med Azure AD-autentisering kan du Hantera identiteter för databasanvändare och andra Microsoft-tjänster på en central plats. Central hantering av ID innehåller en enda plats för att hantera databasanvändare och förenklar hanteringen av behörighet.

> [!NOTE]
> Vi rekommenderar användning av Azure AD-autentisering över användningen av SQL Server-autentisering.

Följande: fördelar

- Det är ett alternativ till SQL Server-autentisering.
- Det hjälper dig att stoppa spridning av användaridentiteter över databasservrar.
- Det gör att lösenordsrotation i en och samma plats.
- Kunder kan hantera databasbehörigheter med hjälp av externa (Azure AD)-grupper.
- Men kan inte lagra lösenord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure Active Directory.
- Den använder oberoende databasanvändare för att autentisera identiteter på databasnivå.
- Det har stöd för tokenbaserad autentisering för program som ansluter till SQL-databas.
- Det stöder AD FS (domän federation) eller interna användare/lösenord autentisering för en lokal Azure Active Directory-instans utan domänsynkronisering.
- Azure AD stöder anslutningar från SQL Server Management Studio som använder Active Directory Universal-autentisering, inklusive Multi-Factor Authentication. Multi-Factor Authentication ger stark autentisering med en rad verifieringsalternativ – telefonsamtal, textmeddelande, smartkort och PIN-kod eller mobilapp. Mer information finns i [SSMS-stöd för Azure AD Multi-Factor Authentication med SQL Database och SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Konfigurationen omfattar följande procedurer för att konfigurera och använda Azure AD-autentisering:

- Skapa och fylla i Azure AD.
- Valfritt: Koppla eller ändra Active Directory-instans som för närvarande är associerat med din Azure-prenumeration.
- Skapa en Azure Active Directory-administratör för Azure SQL Database eller [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Konfigurera klientdatorer.
- Skapa oberoende databasanvändare i databasen som mappats till Azure AD-identiteter.
- Ansluta till databasen med hjälp av Azure AD-identiteter.

Du hittar detaljerad information i [Använd Azure Active Directory-autentisering för autentisering med SQL Database Managed Instance eller SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md).

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Skydda dina data med hjälp av kryptering och säkerhet på radnivå
[Azure SQL Database transparent datakryptering](../sql-database/transparent-data-encryption-azure-sql.md) hjälper dig att skydda data på disken och skyddar mot obehörig åtkomst till maskinvara. Den utför i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler vilande utan ändringar i programmet. Transparent datakryptering krypteras lagring av en hel databas med hjälp av en symmetrisk nyckel som heter databaskrypteringsnyckeln.

Även om hela lagringen är krypterad, är det viktigt att även kryptera själva databasen. Det här är en implementering av metoden skydd på djupet för dataskydd. Om du använder Azure SQL Database och vill skydda känsliga data (till exempel kreditkort eller personnummer), kan du kryptera databaser med FIPS 140-2-verifierade 256-bitars AES-kryptering. Den här krypteringen uppfyller kraven för många branschstandarder (till exempel HIPAA och PCI).

Filer som är relaterade till [buffra pool-tillägget (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) krypteras inte när du krypterar en databas med hjälp av transparent datakryptering. Du måste använda filsystemnivå-kryptering verktyg som [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) eller [Krypterande filsystem (EFS)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10)) för BPE-relaterade filer.

Eftersom en behörig användare som en administratör eller en databasadministratör kan komma åt data även om databasen är krypterad med transparent datakryptering, bör du även följa dessa rekommendationer:

- Aktivera SQL Server-autentisering på databasnivå.
- Använd Azure AD-autentisering med hjälp av [RBAC-roller](../role-based-access-control/overview.md).
- Se till att användare och program använder separata konton för autentisering. På så sätt kan du begränsa behörigheterna för användare och program och minska risken för skadlig aktivitet.
- Implementera säkerhet på databasnivå med hjälp av fasta databasroller (till exempel db_datareader och db_datawriter). Eller så kan du skapa anpassade roller för programmet att bevilja behörighet till valda databasobjekt.

Överväg att andra sätt att skydda dina data:

- [Kryptering på cellnivå](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) för att kryptera vissa kolumner eller även celler av data med olika krypteringsnycklar.
- [Alltid krypterad](/sql/relational-databases/security/encryption/always-encrypted-database-engine), vilket gör att klienter att kryptera känsliga data i klientprogram och aldrig avslöja krypteringsnycklarna till databasmotorn (SQL Database eller SQL Server). Därför Always Encrypted ger dig en åtskillnad mellan den som äger data (och kan visa den) och de som hanterar data (men inte har åtkomsträttigheter).
- [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security), som ger kunder möjlighet att styra åtkomsten till rader i en databastabell baserat på egenskaperna för den användare som kör en fråga. (Exempel egenskaper är gruppen medlemskap och körning kontexten.)

Organisationer som inte använder kryptering på databasnivå kan vara mer sårbar för attacker som kan äventyra data som finns i SQL-databaser.

Du kan lära dig mer om SQL Database transparent datakryptering genom att läsa artikeln [Transparent datakryptering med Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="enable-database-auditing"></a>Aktivera database-granskning
Granskning av en instans av SQL Server Database Engine eller en individuell databas innebär att spåra och loggning av händelser. Du kan skapa granskningar som innehåller specifikationer för servernivå händelser och specifikationer för händelser på databasnivå för SQL Server. Granskade händelser kan skrivas till händelseloggarna eller granska filer.

Det finns flera nivåer för granskning för SQL Server, beroende på myndigheter eller standarder krav för installationen. Granskning av SQL Server innehåller verktyg och processer för att aktivera, lagra och visa granskningar på olika server- och databasobjekt.

[Azure SQL Database-granskning](../sql-database/sql-database-auditing.md) spårar databasen händelser och skriver dem till en granskningslogg i ditt Azure storage-konto.

Granskning kan hjälpa dig att upprätthålla regelefterlevnad, Förstå Databasaktivitet och hitta avvikelser och fel som kan peka på affärsproblem eller säkerhetsöverträdelser. Granskning underlättar infört efterlevnadsstandarder men garanterar inte efterlevnad.

Läs mer om database-granskning och hur du aktiverar det i [Kom igång med SQL-databasgranskning](../sql-database/sql-database-auditing.md).

## <a name="enable-database-threat-detection"></a>Aktivera hotidentifiering för databasen
Skydd mot hot är mer omfattande än identifiering. Skydd mot hot på databasen innehåller:

- Identifiera och klassificera dina mest känsliga data så att du kan skydda dina data.
- Implementera säkra konfigurationer på din databas så att du kan skydda databasen.
- Identifiera och svara på potentiella hot allteftersom de sker så att du kan snabbt svara och reparera.

**Bästa praxis**: Identifiera, klassificera och märka känslig data i dina databaser.   
**Information om**: Klassificera data i din SQL-databas genom att aktivera [Dataidentifiering och klassificering](../sql-database/sql-database-data-discovery-and-classification.md) i Azure SQL Database. Du kan övervaka åtkomsten till känsliga data i Azure-instrumentpanelen eller ladda ned rapporter.

**Bästa praxis**: Spåra säkerhetsrisker i databasen så att du proaktivt kan förbättra din databassäkerhet.   
**Information om**: Använd Azure SQL Database [Sårbarhetsbedömning](../sql-database/sql-vulnerability-assessment.md) -tjänsten, som söker efter säkerhetsrisker i databasen. Tjänsten används en kunskapsbas med regler som flagga säkerhetsrisker och visa avvikelser från regelverk som felkonfigurationer, onödigt generösa behörigheter och oskyddade känsliga data.

Reglerna är baserade på Microsofts bästa praxis och fokusera på säkerhetsproblem som presenterar de största riskerna för din databas och dess värdefulla data. De täcker både på databasnivå problem och servernivå säkerhetsproblem som serverbrandväggsinställningarna och behörigheter på servernivå. De här reglerna representerar många av kraven från reglerande organ för att uppfylla sina efterlevnadsstandarder.

**Bästa praxis**: Aktivera hotidentifiering.  
**Information om**:  Aktivera Azure SQL Database [Hotidentifiering](../sql-database/sql-database-threat-detection.md) få säkerhetsaviseringar och rekommendationer om hur du undersöka och åtgärda hot. Du får aviseringar om misstänkta databasaktiviteter, potentiella svagheter, SQL-inmatningsattacker och avvikande mönster för dataåtkomst och frågeprestanda.

[Avancerat skydd](../sql-database/sql-advanced-threat-protection.md) en enhetlig paket för avancerade funktioner för SQL-säkerhet. Det innehåller de tjänster som vi nämnde tidigare: Dataidentifiering och klassificering, Sårbarhetsbedömning och Hotidentifiering. Det ger en enda plats för att aktivera och hantera dessa funktioner.

Att dessa funktioner som hjälper dig att:

- Uppfyll data sekretesstandarder och efterlevnadskrav.
- Kontrollera åtkomsten till dina databaser och förstärka säkerhet.
- Övervaka en dynamisk databasmiljö där ändringar är svåra att spåra.
- Identifiera och svara på potentiella hot.

Dessutom integreras Hotidentifiering aviseringar med Azure Security Center för en central vy över säkerhetsläget hos alla dina Azure-resurser.

## <a name="next-steps"></a>Nästa steg
Se [säkerhet i Azure-metodtips och mönster](security-best-practices-and-patterns.md) för flera beprövade metoder för att använda när du utforma, distribuera och hantera dina molnlösningar med hjälp av Azure.

Följande resurser är tillgängliga för att tillhandahålla mer allmän information om Azure-säkerhet och relaterade Microsoft-tjänster:
* [Azure-Säkerhetsteamets blogg](https://blogs.msdn.microsoft.com/azuresecurity/) – uppdaterad information på senast inom Azure-säkerhet
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – där kan du rapportera säkerhetsproblem i Microsoft, inklusive problem med Azure, eller mejla till secure@microsoft.com

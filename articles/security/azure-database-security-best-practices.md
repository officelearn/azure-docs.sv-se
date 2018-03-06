---
title: "Säkerhetsmetoder för Azure-databas | Microsoft Docs"
description: "Den här artikeln innehåller en uppsättning Metodtips för säkerhet i Azure-databas."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: c8f49a778ab5e32108531cab05cab7f7eb092221
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="azure-database-security-best-practices"></a>Säkerhetsmetoder för Azure-databas

Säkerhet är ett viktigt mål vid hantering av databaser och det har alltid en prioritet för Azure SQL Database. Databaserna kan skyddas nära för att uppfylla de flesta reglerande eller säkerhetskrav, bland annat HIPAA, ISO 27001/27002 och PCI DSS nivå 1. En lista över efterlevnad Säkerhetscertifieringar är tillgänglig på den [Microsoft Trust Center plats](http://azure.microsoft.com/support/trust-center/services/). Du kan också välja att placera dina databaser i specifika Azure-Datacenter baserat på föreskrifter.

I den här artikeln diskuteras en samling Azure-databas säkerhetsmetoder. Följande rekommendationer härleds från våra upplevelse med säkerhet på Azure-databas och erfarenheter från kunder som dig själv.

För varje bästa praxis förklarar vi:

-   Vad som är bästa praxis
-   Varför du vill aktivera den bästa praxis
-   Vad kan vara resultatet om du inte aktivera bästa praxis
-   Hur du kan lära dig att aktivera bästa praxis

Den här artikeln säkerhetsmetoder för Azure-databas är baserad på en konsensus åsikt och Azure plattformsfunktioner och funktionerna som de finns på den tid som den här artikeln skrevs. Åsikter och tekniker ändras med tiden och den här artikeln kommer att uppdateras regelbundet så att dessa ändringar.

Azure-databas Metodtips om säkerhet i den här artikeln omfattar:

-   Använd brandväggsreglerna för att begränsa åtkomst till databasen
-   Aktivera Databasautentisering
-   Skydda dina data med hjälp av kryptering
-   Skydda data under överföring
-   Aktivera granskning för databasen
-   Aktivera hotidentifiering för databasen


## <a name="use-firewall-rules-to-restrict-database-access"></a>Använd brandväggsreglerna för att begränsa åtkomst till databasen

Microsoft Azure SQL Database tillhandahåller en relationsdatabastjänst för Azure och andra Internetbaserade program. För att ge åtkomstsäkerhet, kontrollerar SQL-databas åtkomsten med brandväggsregler begränsa anslutningsbarhet med IP-adress, autentiseringsmekanismer som kräver att användare att bevisa sin identitet och auktorisering mekanismer för att begränsa användare till specifika åtgärder och data. Brandväggar förhindrar all åtkomst till databasservern förrän du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.

![Brandväggsregler](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

Azure SQL Database-tjänsten är endast tillgänglig via TCP-port 1433. Se till att klientdatorns brandvägg tillåter utgående TCP-kommunikation på TCP-port 1433 för att komma åt en SQL Database från datorn. Om det behövs inte för andra program, blockera inkommande anslutningar på TCP-port 1433 med hjälp av brandväggsregler.

Som en del av anslutningen omdirigeras anslutningar från virtuella Azure-datorer till en annan IP-adress och port, som är unik för varje arbetsroll. Portnumret är i intervallet 11000 till 11999. Mer information om TCP-portar finns [portar utöver 1433 för ADO.NET 4.5 och SQL Database2](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12).

> [!Note]
> Mer information om brandväggsregler i SQL Database finns [SQL Database-brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="enable-database-authentication"></a>Aktivera Databasautentisering
SQL Database stöder två typer av autentisering, SQL-autentisering och Azure Active Directory-autentisering (Azure AD-autentisering).

**SQL-autentisering** rekommenderas i följande fall:

-   Det gör SQL Azure som stöd för miljöer med blandad operativsystem där inte alla användare autentiseras med en Windows-domän.
-   Tillåter SQL Azure som stöd för äldre program och program från tredje part som kräver SQL Server-autentisering.
-   Tillåter användare att ansluta från okänd eller ej betrodda domäner. Ett program där etablerade kunder ansluter med tilldelade till exempel SQL Server-inloggningar för att få status för sina order.
-   Tillåter SQL Azure som stöd för webbaserade program där användare kan skapa sina egna identiteter.
-   Gör det möjligt för programutvecklare att distribuera sina program med hjälp av en komplex behörighet hierarki baserat på kända, förinställda SQL Server-inloggningar.

> [!Note]
> SQL Server-autentisering kan dock använda Kerberos-säkerhetsprotokoll.

Om du använder **SQL-autentisering** måste du:

-   Hantera starka autentiseringsuppgifter själv.
-   Skydda autentiseringsuppgifter i anslutningssträngen.
-   (Möjligen) skydda autentiseringsuppgifterna som skickas över nätverket från webbservern till databasen. Mer information finns i [så här: ansluta till SQL Server med hjälp av SQL-autentisering i ASP.NET 2.0](https://msdn.microsoft.com/library/ms998300.aspx).

**Azure Active Directory-autentisering** är en mekanism för anslutning till Microsoft Azure SQL Database och [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) med hjälp av identiteter i Azure Active Directory (AD Azure). Med Azure AD-autentisering kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster på en central plats. Central hantering av ID innehåller en enda plats för att hantera användare och förenklar hantering av behörighet. Följande: fördelar

-   Det ger ett alternativ till SQL Server-autentisering.
-   Hjälper till att stoppa spridning av användaridentiteter över databasservrar.
-   Tillåter lösenord rotation i en enda plats.
-   Kunder kan hantera databasen med hjälp av externa (AAD)-grupper.
-   Men du kan inte lagra lösenord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure Active Directory.
-   Azure AD-autentisering använder oberoende databasanvändare för att autentisera identiteter på databasnivå.
-   Azure AD stöder tokenbaserad autentisering för program som ansluter till SQL-databas.
-   Azure AD authentication stöder AD FS (domänfederation) eller intern användarlösenord autentisering för en lokal Active Directory med Azure utan domänsynkronisering.
-   Azure AD stöder anslutningar från SQL Server Management Studio som använder Active Directory Universal-autentisering, vilket innefattar Multi-Factor Authentication (MFA). MFA ingår stark autentisering med ett antal alternativ för enkel verifiering – telefonsamtal, textmeddelande, smartkort och PIN-kod eller meddelande i mobilappen. Mer information finns i [SSMS stöd för Azure AD MFA med SQL Database och SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication).

Konfigurationsstegen innehåller följande procedurer för att konfigurera och använda Azure Active Directory-autentisering.

-   Skapa och fylla i Azure AD.
-   Valfritt: Koppla eller ändra active directory som associeras med din Azure-prenumeration.
-   Skapa en Azure Active Directory-administratör för Azure SQL server eller [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Konfigurera klientdatorer.
-   Skapa oberoende databasanvändare i din databas som mappats till Azure AD identiteter.
-   Ansluta till databasen med hjälp av Azure AD identiteter.

Du hittar information information [här](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).

## <a name="protect-your-data-using-encryption"></a>Skydda dina data med hjälp av kryptering

[Azure SQL Database transparent datakryptering (TDE)](https://msdn.microsoft.com/library/dn948096.aspx) skyddar mot hot från skadlig aktivitet genom att utföra realtid kryptering och dekryptering av databas, tillhörande säkerhetskopior och transaktionsloggfiler vilande utan ändringar i programmet. TDE krypterar lagring av en hel databas med hjälp av en symmetrisk nyckel som heter databaskrypteringsnyckeln.

Även om hela lagring krypteras är det mycket viktigt att kryptera även själva databasen. Det här är en implementering av skydd på djupet metod för att skydda data. Om du använder Azure SQL Database och vill skydda känsliga data, till exempel kreditkort eller personnummer, kan du kryptera databaser med FIPS 140-2-validerade 256-bitars AES-kryptering som uppfyller kraven för många branschstandarder (till exempel HIPAA, PCI).

Det är viktigt att förstå att filer relaterade till [bufferten pool-tillägg (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) krypteras inte när en databas är krypterat med TDE. Du måste använda filen kryptering på Systemverktyg som [BitLocker](https://technet.microsoft.com/library/cc732774) eller [EFS (ENCRYPTING File System)](https://technet.microsoft.com/library/cc700811.aspx) för BPE relaterade filer.

Eftersom en behörig användare som en administratör eller en databasadministratör kan komma åt data även om databasen är krypterad med TDE, bör du också följa rekommendationerna nedan:

-   Aktivera SQL-autentisering på databasnivå.
-   Använda Azure AD för autentisering med [RBAC-roller](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is).
-   Användare och program bör använda separata konton för att autentisera. Det här sättet kan du begränsa behörigheterna för användare och program och minska riskerna med skadlig aktivitet.
-   Implementera databasen säkerhetsnivå via fasta databasroller (till exempel db_datareader eller db_datawriter) eller du kan skapa anpassade roller för programmet att bevilja explicit behörighet till valda databasobjekt.

För andra sätt att kryptera dina data, kan du överväga:

-   [Kryptering på cellnivå](https://msdn.microsoft.com/library/ms179331.aspx) för att kryptera vissa kolumner eller även celler av data med olika krypteringsnycklar.
-   Kryptering används med hjälp av Always Encrypted: [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) tillåter klienter att kryptera känsliga data i klientprogram och aldrig avslöja krypteringsnycklarna till databasmotorn (SQL Database eller SQL Server). Därför Always Encrypted ger dig en åtskillnad mellan dem som äger data (och visa det) och de som hanterar data (men bör har ingen åtkomst).
-   Använder säkerhet på radnivå: säkerhet på radnivå ger kunder möjlighet att styra åtkomsten till rader i en databastabell på grund av egenskaper för den användare som kör en fråga (t.ex. gruppen medlemskap eller köra sammanhang). Mer information finns i [Säkerhet på radnivå](https://msdn.microsoft.com/library/dn765131).

## <a name="protect-data-in-transit"></a>Skydda data under överföring
Skydda data under överföringen ska väsentlig del av strategin för skydd av data. Eftersom data flyttar fram och tillbaka från många platser, vi Allmänt rekommenderar att du alltid använder SSL/TLS-protokoll för att utbyta data mellan olika platser. I vissa fall kanske du vill isolera hela kommunikationskanalen mellan din lokala och moln infrastruktur med hjälp av ett virtuellt privat nätverk (VPN).

För data som flyttas mellan din lokala infrastruktur och Azure, bör du lämpliga skyddsåtgärder, till exempel HTTPS eller VPN.

För organisationer som behöver för att skydda åtkomst från flera arbetsstationer som finns lokalt till Azure kan använda [Azure plats-till-plats-VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

För organisationer som behöver för att skydda åtkomst från enskilda arbetsstationer som finns lokalt eller utanför företagets lokaler till Azure, Överväg att använda [punkt-till-plats VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Större datauppsättningar kan flyttas dedikerade snabba WAN-länk som [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Om du väljer att använda ExpressRoute kan du också kryptera data på programnivå med hjälp av [SSL/TLS](https://support.microsoft.com/kb/257591) eller andra protokoll för extra skydd.

Om du interagerar med Azure Storage via Azure Portal, alla transaktioner ska ske via HTTPS. [Storage REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) över HTTPS kan också användas för att interagera med [Azure Storage](https://azure.microsoft.com/services/storage/) och [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Organisationer som inte kan skydda data under överföringen är mer känslig för [man-in-the-middle-attacker](https://technet.microsoft.com/library/gg195821.aspx), [avlyssning](https://technet.microsoft.com/library/gg195641.aspx) och sessionskapning. Dessa attacker kan vara det första steget i att komma åt känsliga data.

Läs mer om Azure VPN-alternativet genom att läsa artikeln [planering och design för VPN-Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

## <a name="enable-database-auditing"></a>Aktivera granskning för databasen
Granskning av en instans av SQL Server Database Engine eller en individuell databas innebär att spåra och loggning av händelser som inträffar i databasmotorn. SQL Server audit kan du skapa server granskningar som kan innehålla server audit specifikationer för serverhändelser och databasen audit specifikationer för databashändelser. Granskade händelser kan skrivas till händelseloggarna eller granska filer.

Det finns flera nivåer av granskning för SQL Server, beroende på myndigheter eller standarder dina krav. SQL Server Audit innehåller de verktyg och processer som du ska kunna aktivera, lagra och visa granskningar på olika objekt för server och databas.

[Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) spårar databashändelser och skriver dem till en granskningslogg logga in i ditt Azure Storage-konto-databas.

Granskning kan hjälpa dig att upprätthålla regelefterlevnad, förstå databasaktiviteter och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.

Granskning kan och underlättar anslutning till efterlevnadsstandarder men garantera inte efterlevnad.

Mer information om granskning för databasen och hur du aktiverar det finns i artikel [aktivera granskning och hotidentifiering identifiering på SQL-servrar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers).

## <a name="enable-database-threat-detection"></a>Aktivera hotidentifiering för databasen
SQL-Hotidentifiering kan du identifiera och svara på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar på avvikande aktiviteter. Du får en avisering vid misstänkt databasaktiviteter, potentiella säkerhetsproblem och SQL injection attacker samt avvikande databasen åtkomstmönster. SQL-Hotidentifiering aviseringar ger detaljerad information om misstänkt aktivitet och rekommenderar åtgärd att undersöka och minska risken.

Till exempel är SQL injection en av säkerhetsproblem för vanliga Web program på Internet, används till att attackera datadrivna program. Angripare dra nytta av programmet säkerhetsproblem att mata in skadlig SQL-instruktioner i programmet fält, brott mot eller ändra data i databasen.

Mer information om hur du ställer in hotidentifiering för din databas i Azure portal finns [Hotidentifiering för SQL-databasen](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

Dessutom kan SQL Hotidentifiering integreras med [Azure Security Center](https://azure.microsoft.com/services/security-center/). Vi erbjuder dig att testa kostnadsfritt i 60 dagar.

Mer information om databasen Hotidentifiering och hur du aktiverar det finns i artikel [aktivera granskning och hotidentifiering identifiering på SQL-servrar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers).

## <a name="conclusion"></a>Slutsats
Azure-databas är en robust databasplattform, med en fullständig uppsättning funktioner som uppfyller kraven för många organisationens och regelmässig efterlevnad. Du kan skydda data genom att kontrollera fysisk tillgång till dina data och använda en mängd olika alternativ för datasäkerhet på fil-, kolumn- eller radnivå med Transparent datakryptering, cellnivå kryptering eller säkerhet på radnivå. Alltid gör krypterat också att åtgärder mot krypterade data, förenkla processen för programuppdateringar. I sin tur ger åtkomst till granskningsloggar för SQL-databas aktivitet dig den information du behöver, så att du vet hur och när data används.

## <a name="next-steps"></a>Nästa steg
- Mer information om brandväggsregler finns [regler i brandväggen](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).
- Mer information om användare och inloggningar finns i [Hantera inloggningar](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).
- En självstudiekurs finns [skydda din Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial).

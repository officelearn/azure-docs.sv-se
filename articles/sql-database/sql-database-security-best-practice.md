---
title: Spelbok för att ta itu med gemensamma säkerhetskrav | Microsoft-dokument
titleSuffix: Azure SQL Database
description: Den här artikeln innehåller vanliga säkerhetskrav och metodtips i Azure SQL Database.
ms.service: sql-database
ms.subservice: security
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 02/20/2020
ms.reviewer: ''
ms.openlocfilehash: c18e1b1a1feba5c528a692b7d63287b3751b62cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77506226"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database"></a>Playbook för att åtgärda gemensamma säkerhetskrav med Azure SQL Database

> [!NOTE]
> Det här dokumentet innehåller metodtips för hur du löser gemensamma säkerhetskrav. Alla krav gäller inte för alla miljöer och du bör konsultera databasen och säkerhetsteamet som funktioner ska implementeras på.

## <a name="solving-common-security-requirements"></a>Lösa gemensamma säkerhetskrav

Det här dokumentet innehåller vägledning om hur du löser vanliga säkerhetskrav för nya eller befintliga program med Azure SQL Database. Det organiseras av hög nivå säkerhetsområden. För att ta itu med specifika hot, se avsnittet [gemensamma säkerhetshot och potentiella minskningar.](#common-security-threats-and-potential-mitigations) Även om vissa av de presenterade rekommendationerna är tillämpliga när du migrerar program från lokala till Azure, är migreringsscenarier inte i fokus för det här dokumentet.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>Azure SQL Database-distributionserbjudanden som omfattas av den här guiden

- [SQL-databaser:](https://docs.microsoft.com/azure/sql-database/sql-database-single-index) [enskilda databaser](sql-database-single-database.md) och [elastiska pooler](sql-database-elastic-pool.md) i [Azure SQL Database-servrar](sql-database-servers.md)
- [Hanterade instanser](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="sql-deployment-offers-not-covered-in-this-guide"></a>SQL-distributionserbjudanden som inte omfattas av den här guiden

- Azure SQL Data Warehouse
- Virtuella Azure SQL-datorer (IaaS)
- Sql Server lokalt

### <a name="audience"></a>Målgrupp

De avsedda målgrupperna för den här guiden är kunder som ställs inför frågor om hur du skyddar Azure SQL Database. Rollerna som är intresserade av den här artikeln om bästa praxis inkluderar, men inte begränsat till:

- Säkerhetsarkitekter
- Säkerhetsansvariga
- Efterlevnad Officerare
- Sekretessofficerare
- Säkerhetstekniker

### <a name="using-this-guide"></a><a id="using"></a>Använda den här guiden

Det här dokumentet är avsett som en följeslagare till vår befintliga [Azure SQL Database-säkerhetsdokumentation.](sql-database-security-overview.md)

Om inget annat anges rekommenderar vi att du följer alla metodtips som anges i varje avsnitt för att uppnå respektive mål eller krav. För att uppfylla specifika standarder för säkerhetsefterlevnad eller bästa praxis listas viktiga kontroller av regelefterlevnad under avsnittet Krav eller mål där så är tillämpligt. Dessa är de säkerhetsstandarder och föreskrifter som refereras i detta dokument:

- [FedRAMP](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): CM-3, SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): Åtkomstkontroll, Kryptografi
- [OSA-praxis (Microsoft Operational Security Assurance):](https://www.microsoft.com/en-us/securityengineering/osa/practices)Praxis #1-6 och #9
- [NIST Special Publikation 800-53 Säkerhetskontroller:](https://nvd.nist.gov/800-53)AC-5, AC-6
- [PCI DSS:](https://www.pcisecuritystandards.org/document_library)6.3.2, 6.4.2

### <a name="feedback"></a>Feedback

Vi planerar att fortsätta att uppdatera de rekommendationer och bästa praxis som anges här. Ange indata eller eventuella korrigeringar för det här dokumentet med hjälp av länken **Feedback** längst ned i den här artikeln.

## <a name="authentication"></a>Autentisering

Autentisering är processen att bevisa att användaren är den de påstår sig vara. Azure SQL Database stöder två typer av autentisering:

- SQL-autentisering
- Azure Active Directory-autentisering

> [!NOTE]
> Azure Active Directory-autentisering kanske inte stöds för alla verktyg och program från tredje part.

### <a name="central-management-for-identities"></a>Central ledning för identiteter

Central identitetshantering erbjuder följande fördelar:

- Hantera gruppkonton och kontrollera användarbehörigheter utan att duplicera inloggningar över Azure SQL Database-servrar och databaser.
- Förenklad och flexibel behörighetshantering.
- Hantering av applikationer i stor skala.

**Hur man genomför:**

- Använd Azure Active Directory-autentisering (Azure AD) för centraliserad identitetshantering.

**Metodtips:**

- Skapa en Azure AD-klientorganisation och [skapa användare](../active-directory/fundamentals/add-users-azure-active-directory.md) för att representera mänskliga användare och skapa [tjänsthuvudnamn](../active-directory/develop/app-objects-and-service-principals.md) för att representera appar, tjänster och automatiseringsverktyg. Tjänsthuvudnamn motsvarar tjänstkonton i Windows och Linux. 

- Tilldela åtkomsträttigheter till resurser till Azure AD-huvudnamn via grupptilldelning: Skapa Azure AD-grupper, bevilja åtkomst till grupper och lägg till enskilda medlemmar i grupperna. Skapa databasanvändare som mappar dina Azure AD-grupper i databasen. Om du vill tilldela behörigheter i databasen placerar du användare i databasroller med rätt behörighet.
  - Se artiklarna, [Konfigurera och hantera Azure Active Directory-autentisering med SQL](sql-database-aad-authentication-configure.md) och Använd Azure AD för [autentisering med SQL](sql-database-aad-authentication.md).
  > [!NOTE]
  > I en hanterad instans kan du också skapa inloggningar som mappas till Azure AD-huvudnamn i huvuddatabasen. Se [SKAPA INLOGGNING (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

- Genom att använda Azure AD-grupper förenklas behörighetshanteringen och både gruppägaren och resursägaren kan lägga till/ta bort medlemmar till/från gruppen. 

- Skapa en separat grupp för Azure AD-administratör för SQL DB-servrar.

  - Se [artikeln, Etablera en Azure Active Directory-administratör för din Azure SQL Database-server](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

- Övervaka ändringar i Azure AD-gruppens medlemskap med hjälp av Azure AD-granskningsaktivitetsrapporter. 

- För en hanterad instans krävs ett separat steg för att skapa Azure AD-administratör. 
  - Se [artikeln, Etablera en Azure Active Directory-administratör för din hanterade instans](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). 

> [!NOTE]
> - Azure AD-autentisering registreras i Azure SQL-granskningsloggar, men inte i Azure AD-inloggningsloggar.
> - RBAC-behörigheter som beviljas i Azure gäller inte för Azure SQL DB-behörigheter. Sådana behörigheter måste skapas/mappas manuellt i SQL DB med befintliga SQL-behörigheter.
> - Azure AD-autentisering på klientsidan behöver åtkomst till internet eller via UDR (User Defined Route) till ett virtuella nätverk.
> - Azure AD-åtkomsttoken cachelagras på klientsidan och dess livstid beror på tokenkonfiguration. Se artikeln, [Konfigurerbara token livstider i Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md)
> - Mer information om felsökning av Azure AD-autentiseringsproblem finns i följande blogg:<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

> Nämns i: OSA Practice #2, ISO Access Control (AC)

Azure Multi-Factor Authentication (MFA) ger ytterligare säkerhet genom att kräva mer än en form av autentisering.

**Hur man genomför:**

- [Aktivera MFA](../active-directory/authentication/concept-mfa-howitworks.md) i Azure AD med villkorlig åtkomst och använd interaktiv autentisering. 

- Alternativet är att aktivera MFA för hela Azure AD- eller AD-domänen.

**Metodtips:**

- Aktivera villkorlig åtkomst i Azure AD (kräver Premium-prenumeration). 
  - Se artikeln [Villkorlig åtkomst i Azure AD](../active-directory/conditional-access/overview.md).  

- Skapa Azure AD-grupper och aktivera MFA-principer för valda grupper med Azure AD-villkorlig åtkomst. 
  - Se [artikeln, Planera distribution av villkorlig åtkomst](../active-directory/conditional-access/plan-conditional-access.md). 

- MFA kan aktiveras för hela Azure AD eller för hela Active Directory som federerats med Azure AD. 

- Använd Azure AD Interactive-autentiseringsläge för SQL DB där ett lösenord begärs interaktivt, följt av MFA-autentisering:      
  - Använd universell autentisering i SSMS. Se artikeln [Använda Multifaktor AAD-autentisering med Azure SQL Database och Azure SQL Data Warehouse (SSMS-stöd för MFA).](sql-database-ssms-mfa-authentication.md)
  - Använd interaktiv autentisering som stöds i SQL Server Data Tools (SSDT). Se artikeln Azure [Active Directory-stöd i SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current).
  - Använd andra SQL-verktyg som stöder MFA. 
    - Stöd för SSMS-guiden för export-/extrahera-/distributionsdatabas  
    - [sqlpackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage): alternativet '/ua' 
    - [sqlcmd Utility:](https://docs.microsoft.com/sql/tools/sqlcmd-utility)alternativ -G (interaktiv)
    - [bcp Utility:](https://docs.microsoft.com/sql/tools/bcp-utility)alternativ -G (interaktiv) 

- Implementera dina program för att ansluta till Azure SQL Database med interaktiv autentisering med MFA-stöd. 
  - Se artikeln [Anslut till Azure SQL Database med Azure Multi-Factor Authentication](active-directory-interactive-connect-azure-sql-db.md). 
  > [!NOTE]
  > Det här autentiseringsläget kräver användarbaserade identiteter. I de fall där en betrodd identitetsmodell används som kringgår enskilda Azure AD-användarautentisering (t.ex. med hjälp av hanterad identitet för Azure-resurser), gäller inte MFA.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>Minimera användningen av lösenordsbaserad autentisering för användare

> Nämns i: OSA Practice #4, ISO Access Control (AC)

Lösenordsbaserade autentiseringsmetoder är en svagare form av autentisering. Autentiseringsuppgifter kan komprometteras eller av misstag ges bort.

**Hur man genomför:**

- Använd en Azure AD-integrerad autentisering som eliminerar användningen av lösenord.

**Metodtips:**

- Använd enkel inloggningsautentisering med Windows-autentiseringsuppgifter. Federera den lokala AD-domänen med Azure AD och använd integrerad Windows-autentisering (för domänanslutna datorer med Azure AD).
  - Se artikeln, [SSMS-stöd för Azure AD Integrated authentication](sql-database-aad-authentication-configure.md#active-directory-integrated-authentication).

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>Minimera användningen av lösenordsbaserad autentisering för program 

> Nämns i: OSA Practice #4, ISO Access Control (AC)

**Hur man genomför:**

- Aktivera Azure-hanterad identitet. Du kan också använda integrerad eller certifikatbaserad autentisering. 

**Metodtips:**

- Använd [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).
  - [Systemtilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) 
  - [Användartilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [Använda Azure SQL Database från apptjänst med hanterad identitet (utan kodändringar)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- Använd certbaserad autentisering för ett program. 
  - Se det här [kodexemplet](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token). 

- Använd Azure AD-autentisering för integrerad federerad domän och domänansluten dator (se avsnittet ovan).
  - Se [exempelprogrammet för integrerad autentisering](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated).

### <a name="protect-passwords-and-secrets"></a>Skydda lösenord och hemligheter

För fall där lösenord inte kan undvikas, se till att de är säkrade.

**Hur man genomför:**

- Använd Azure Key Vault för att lagra lösenord och hemligheter. Använd MFA för Azure SQL Database med Azure AD-användare när det är tillämpligt.

**Metodtips:**

- Om det inte är möjligt att undvika lösenord eller hemligheter kan du lagra användarlösenord och programhemligheter i Azure Key Vault och hantera åtkomst via åtkomstprinciper för Key Vault. 

- Olika ramar för apputveckling kan också erbjuda ramspecifika mekanismer för att skydda hemligheter i appen. Till exempel: [ASP.NET kärnapp](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows).

### <a name="use-sql-authentication-for-legacy-applications"></a>Använda SQL-autentisering för äldre program 

SQL-autentisering refererar till autentisering av en användare när du ansluter till Azure SQL Database med användarnamn och lösenord. En inloggning måste skapas i varje SQL Database-server eller en hanterad instans och en användare som skapas i varje databas.

**Hur man genomför:**

- Använd SQL-autentisering.

**Metodtips:**

- Som serveradministratör skapar du inloggningar och användare. Om inte innehållsanvändare med lösenord med innehålls finns lagras alla lösenord i huvuddatabasen.
  - Se [artikeln, Styra och bevilja databasåtkomst till SQL Database och SQL Data Warehouse](sql-database-manage-logins.md).

## <a name="access-management"></a>Åtkomsthantering

Åtkomsthantering är processen att kontrollera och hantera behöriga användares åtkomst och privilegier till Azure SQL Database.

### <a name="implement-principle-of-least-privilege"></a>Genomföra principen om lägsta privilegium

> Nämns i: FedRamp kontroller AC-06, NIST: AC-6, OSA Practice #3

Principen om lägsta behörighet anger att användarna inte ska ha fler privilegier än vad som behövs för att slutföra sina uppgifter. Mer information finns i artikeln [Just enough administration](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview).

**Hur man genomför:**

Tilldela endast nödvändiga [behörigheter](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) för att slutföra de uppgifter som krävs:

- I SQL Data Plane: 
    - Använd detaljerade behörigheter och användardefinierade databasroller (eller serverroller i MI): 
        1. Skapa de roller som krävs
            - [SKAPA ROLL](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)
            - [SKAPA SERVERROLL](https://docs.microsoft.com/sql/t-sql/statements/create-server-role-transact-sql)
        1. Skapa nödvändiga användare
            - [SKAPA ANVÄNDARE](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
        1. Lägga till användare som medlemmar i roller 
            - [ÄNDRA ROLL](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)
            - [ÄNDRA SERVERROLL](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)
        1. Tilldela sedan behörigheter till roller. 
            - [Bevilja](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 
    - Se till att inte tilldela användare till onödiga roller.

- I Azure Resource Manager:
  - Använd inbyggdroller om det är tillgängligt eller anpassade RBAC-roller och tilldela nödvändiga behörigheter.
    - [Inbyggda roller för Azure](../role-based-access-control/built-in-roles.md) 
    - [Anpassade roller för Azure-resurser](../role-based-access-control/custom-roles.md)

**Metodtips:**

Följande metodtips är valfria, men leder till bättre hanterbarhet och support i din säkerhetsstrategi: 

- Om möjligt, börja med minsta möjliga uppsättning behörigheter och börja lägga till behörigheter en efter en om det finns en verklig nödvändighet (och motivering) - i motsats till den motsatta metoden: att ta behörigheter bort steg för steg. 

- Avstå från att tilldela behörigheter till enskilda användare. Använd roller (databas- eller serverroller) konsekvent i stället. Roller hjälper i hög grad till med att rapportera och felsöka behörigheter. (Azure RBAC stöder endast behörighetstilldelning via roller.) 

- Skapa och använd anpassade roller med de exakta behörigheter som behövs. Typiska roller som används i praktiken: 
  - Säkerhetsdistribution 
  - Administratör 
  - Developer 
  - Stödpersonal 
  - Revisor 
  - Automatiserade processer 
  - Slutanvändare 
  
- Använd endast inbyggda roller när behörigheterna för rollerna matchar exakt de behörigheter som krävs för användaren. Du kan tilldela användare till flera roller. 

- Kom ihåg att behörigheter i SQL Server Database Engine kan tillämpas på följande scope. Ju mindre omfattning, desto mindre blir effekten av de beviljade behörigheterna: 
  - Azure SQL Database server (specialroller i huvuddatabasen) 
  - Databas 
  - Schema
      - Det är en bra idé att använda scheman för att bevilja behörigheter i en databas. (se även: [Schemadesign för SQL Server: rekommendationer för schemadesign med säkerhet i åtanke](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/))
  - Objekt (tabell, vy, procedur osv.) 
  > [!NOTE]
  > Det rekommenderas inte att tillämpa behörigheter på objektnivå eftersom den här nivån lägger till onödig komplexitet i den övergripande implementeringen. Om du bestämmer dig för att använda behörigheter på objektnivå bör de dokumenteras tydligt. Detsamma gäller behörigheter på kolumnnivå, som är ännu mindre rekommenderade av samma skäl. Tänk också på att en [nekande](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql) tabell på tabellnivå som standard inte åsidosätter ett BIDRAG på kolumnnivå. Detta kräver att de [gemensamma kriterierna kompatibilitet serverkonfiguration](https://docs.microsoft.com/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option) aktiveras.

- Utför regelbundna kontroller med hjälp av [Sårbarhetsbedömning (VA)](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) för att testa för många behörigheter.

### <a name="implement-separation-of-duties"></a>Genomföra åtskillnad av arbetsuppgifter

> Nämns i: FedRamp: AC-04, NIST: AC-5, ISO: A.6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

Separation av arbetsuppgifter, även kallad Segregering av arbetsuppgifter beskriver kravet på att dela upp känsliga uppgifter i flera uppgifter som tilldelas olika användare. Separation av arbetsuppgifter hjälper till att förhindra dataintrång.

**Hur man genomför:**

- Identifiera den nivå av åtskillnad av arbetsuppgifterna som krävs. Exempel: 
  - Mellan utvecklings-/test- och produktionsmiljöer 
  - Säkerhetsmässigt känsliga uppgifter vs databasadministratör (DBA) hanteringsnivå uppgifter vs utvecklaruppgifter. 
    - Exempel: Revisor, skapande av säkerhetsprinciper för RLS (Role-level Security), Implementera SQL Database-objekt med DDL-behörigheter.

- Identifiera en omfattande hierarki av användare (och automatiserade processer) som kommer åt systemet.

- Skapa roller enligt de användargrupper som behövs och tilldela behörigheter till roller. 
  - För uppgifter på hanteringsnivå i Azure-portalen eller via PowerShell-automation använder du RBAC-roller. Hitta antingen en inbyggd roll som matchar kravet eller skapa en anpassad RBAC-roll med hjälp av tillgängliga behörigheter 
  - Skapa serverroller för serveromfattande uppgifter (skapa nya inloggningar, databaser) i en hanterad instans. 
  - Skapa databasroller för uppgifter på databasnivå.

- För vissa känsliga uppgifter bör du överväga att skapa särskilda lagrade procedurer som signerats av ett certifikat för att utföra uppgifterna för användarnas räkning. 
  - Exempel: [Självstudiekurs: Signera lagrade procedurer med ett certifikat](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate) 

- Implementera transparent datakryptering (TDE) med kundhanterade nycklar i Azure Key Vault för att aktivera åtskillnad av uppgifter mellan dataägare och säkerhetsägare. 
  - Se artikeln [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från Azure-portalen](../storage/common/storage-encryption-keys-portal.md). 

- Om du vill vara säkra på att en DBA inte kan se data som anses vara mycket känsliga och fortfarande kan utföra DBA-uppgifter kan du använda Alltid krypterad med rollseparering. 
  - Se artiklarna Översikt [över nyckelhantering för alltid krypterad,](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) [nyckeletablering med rollseparering](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)och [kolumnnyckelrotation med rollseparering](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation). 

- I de fall då det inte är möjligt åtminstone inte utan större kostnader och insatser som kan göra systemet nära oanvändbart, kan kompromisser göras och mildras med hjälp av kompenserande kontroller såsom: 
  - Mänsklig inblandning i processer. 
  - Granskningshistorik – mer information om granskning finns i [Granska kritiska säkerhetshändelser](#audit-critical-security-events).

**Metodtips:**

- Kontrollera att olika konton används för utvecklings-/test- och produktionsmiljöer. Olika konton hjälper till att följa separationen av test & produktionssystem.

- Avstå från att tilldela behörigheter till enskilda användare. Använd roller (databas- eller serverroller) konsekvent i stället. Att ha roller hjälper i hög grad till med att rapportera och felsöka behörigheter.

- Använd inbyggda roller när behörigheterna matchar exakt de behörigheter som behövs – om union av alla behörigheter från flera inbyggda roller leder till en 100% matchning, kan du också tilldela flera roller samtidigt. 

- Skapa och använd anpassade roller när inbyggda roller ger för många behörigheter eller otillräckliga behörigheter. 

- Rolltilldelningar kan också göras tillfälligt, även kallat DSD (Dynamic Separation of Duties), antingen inom SQL Agent Job steps i T-SQL eller med Azure PIM för RBAC-roller. 

- Kontrollera att dbas inte har tillgång till krypteringsnycklar eller nyckellager och säkerhetsadministratörer med tillgång till nycklarna har ingen tillgång till databasen i sin tur. 

- Se alltid till att ha en granskningsspårning för säkerhetsrelaterade åtgärder. 

- Du kan hämta definitionen av de inbyggda RBAC-rollerna för att se de behörigheter som används och skapa en anpassad roll baserat på utdrag och kumulationer av dessa via PowerShell.

- Eftersom alla medlemmar i db_owner databasrollen kan ändra säkerhetsinställningar som Transparent datakryptering (TDE) eller ändra SLO, bör detta medlemskap beviljas med försiktighet. Det finns dock många uppgifter som kräver db_owner privilegier. Uppgift som att ändra alla databasinställningar som att ändra DB-alternativ. Granskning spelar en nyckelroll i alla lösningar.

- Det går inte att begränsa behörigheterna för en db_owner och hindrar därför ett administrativt konto från att visa användardata. Om det finns mycket känsliga data i en databas kan alltid krypterad användas för att på ett säkert sätt förhindra att db_owners eller någon annan DBA visar den.

> [!NOTE]
> Att uppnå åtskillnad av arbetsuppgifter (SoD) är en utmaning för säkerhetsrelaterade eller felsökningsuppgifter. Andra områden som utveckling och slutanvändarroller är lättare att segregera. De flesta efterlevnadsrelaterade kontroller tillåter användning av alternativa kontrollfunktioner som granskning när andra lösningar inte är praktiska.

För de läsare som vill fördjupa dig i SoD rekommenderar vi följande resurser: 

- För Azure SQL-databas:  
  - [Styra och bevilja databasåtkomst till SQL Database och SQL Data Warehouse](sql-database-manage-logins.md)
  - [Motordelning av arbetsuppgifter för applikationsutvecklaren](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100)) 
  - [Åtskillnad av arbetsuppgifter i SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=39269)
  - [Signering av lagrade procedurer i SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- För Azure Resource Management:
  - [Inbyggda roller för Azure](../role-based-access-control/built-in-roles.md) 
  - [Anpassade roller för Azure-resurser](../role-based-access-control/custom-roles.md)
  - [Använda Azure AD-privilegierad identitetshantering för förhöjd åtkomst](https://www.microsoft.com/en-us/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>Utför regelbundna kodgranskningar

> Nämns i: PCI: 6.3.2, SOC: SDL-3 

Separation av arbetsuppgifter är inte begränsad till data i databasen, men inkluderar programkod. Skadlig kod kan eventuellt kringgå säkerhetskontroller. Innan du distribuerar anpassad kod till produktion är det viktigt att granska vad som distribueras.

**Hur man genomför:**

- Använd ett databasverktyg som Azure Data Studio som stöder källkontroll. 

- Implementera en segregerad koddistributionsprocess.

- Innan du bestämmer dig för att huvudgrenen måste en person (förutom författaren till själva koden) kontrollera koden för potentiella behörighetshöjningsrisker samt skadliga dataändringar för att skydda mot bedrägeri och oseriös åtkomst. Detta kan göras med hjälp av källkontrollmekanismer.

**Metodtips:**

- Standardisering: Det hjälper till att implementera en standardprocedur som ska följas för alla koduppdateringar. 

- Sårbarhetsbedömning innehåller regler som kontrollerar om det finns för stora behörigheter, användning av gamla krypteringsalgoritmer och andra säkerhetsproblem i ett databasschema. 

- Ytterligare kontroller kan göras i en QA eller testmiljö med hjälp av avancerat skydd mot hot som söker efter kod som är sårbar för SQL-injektion.

- Exempel på vad du ska hålla utkik efter: 
  - Skapa en användare eller ändra säkerhetsinställningar från en automatiserad SQL-code-update-distribution. 
  - En lagrad procedur som, beroende på vilka parametrar som tillhandahålls, uppdaterar ett monetärt värde i en cell på ett icke-överensstämmande sätt. 

- Se till att den person som utför granskningen är en annan individ än den ursprungliga koden författare och kunniga i kod-recensioner och säker kodning.

- Var noga med att känna till alla källor till kodändringar. Kod kan finnas i T-SQL-skript. Det kan vara ad hoc-kommandon som ska köras eller distribueras i form av vyer, funktioner, utlösare och lagrade procedurer. Det kan vara en del av SQL Agent Job definitioner (Steg). Den kan också köras inifrån SSIS-paket, Azure Data Factory och andra tjänster.

## <a name="data-protection"></a>Dataskydd

Dataskydd är en uppsättning funktioner för att skydda viktig information från kompromisser genom kryptering eller förvirring.

> [!NOTE]
> Microsoft intygar att Azure SQL Database är FIPS 140-2 Nivå 1-kompatibel. Detta görs efter att ha verifierat strikt användning av FIPS 140-2 Nivå 1 godtagbara algoritmer och FIPS 140-2 Nivå 1 validerade instanser av dessa algoritmer, inklusive konsekvens med nödvändiga nyckellängder, nyckelhantering, nyckelgenerering och nyckellagring. Detta intyg är avsett att göra det möjligt för våra kunder att svara på behovet eller kravet på användning av FIPS 140-2 Nivå 1 validerade instanser vid behandling av data eller leverans av system eller applikationer. Vi definierar termerna "FIPS 140-2 Level 1-kompatibla" och "FIPS 140-2 Nivå 1-efterlevnad" som används i ovanstående uttalande för att visa deras avsedda tillämplighet på usa och kanadas användning av den olika termen "FIPS 140-2 Nivå 1 validerad." 


### <a name="encrypt-data-in-transit"></a>Kryptera data under transport

> Nämns i: OSA Practice #6, ISO Control Family: Cryptography

Skyddar dina data medan data flyttas mellan klienten och servern. Se [Nätverkssäkerhet](#network-security).

### <a name="encrypt-data-at-rest"></a>Kryptera data i vila

> Nämns i: OSA Practice #6, ISO Control Family: Cryptography

Kryptering i vila är kryptografiskt skydd av data när den sparas i databas-, logg- och säkerhetskopieringsfiler.

**Hur man genomför:**

- [Transparent databaskryptering (TDE)](transparent-data-encryption-azure-sql.md) med tjänsthanterade nycklar är aktiverade som standard för alla databaser som skapas efter 2017 i Azure SQL Database.
- I en hanterad instans, om databasen skapas från en återställningsåtgärd med hjälp av en lokal server, kommer TDE-inställningen för den ursprungliga databasen att uppfyllas. Om den ursprungliga databasen inte har TDE aktiverat rekommenderar vi att TDE aktiveras manuellt för den hanterade instansen.

**Metodtips:**

- Lagra inte data som kräver kryptering i vila i huvuddatabasen. Huvuddatabasen kan inte krypteras med TDE.

- Använd kundhanterade nycklar i Azure Key Vault om du behöver ökad transparens och detaljerad kontroll över TDE-skyddet. Azure Key Vault gör det möjligt att när som helst återkalla behörigheter för att göra databasen otillgänglig. Du kan centralt hantera TDE-skydd tillsammans med andra nycklar, eller rotera TDE-skyddet enligt ditt eget schema med Azure Key Vault.

- Om du använder kundhanterade nycklar i Azure Key Vault följer du artiklarna, [Riktlinjer för att konfigurera TDE med Azure Key Vault](transparent-data-encryption-byok-azure-sql.md#recommendations-when-configuring-akv) och hur du [konfigurerar Geo-DR med Azure Key Vault](transparent-data-encryption-byok-azure-sql.md#geo-dr-and-customer-managed-tde).

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>Skydda känsliga data som används från högprivilegierat, obehöriga användare

Data som används är de data som lagras i minnet av databassystemet under körningen av SQL-frågor. Om databasen lagrar känsliga data kan din organisation vara skyldig att se till att användare med hög behörighet hindras från att visa känsliga data i databasen. Användare med hög behörighet, till exempel Microsoft-operatörer eller DBA:er i organisationen, bör kunna hantera databasen, men förhindras från att visa och eventuellt exfiltraa känsliga data från minnet i SQL Server-processen eller genom att fråga databasen.

De principer som avgör vilka data som är känsliga och om känsliga data måste krypteras i minnet och inte är tillgängliga för administratörer i klartext, är specifika för din organisation och efterlevnadsregler som du måste följa. Se det relaterade kravet: [Identifiera och tagga känsliga data](#identify-and-tag-sensitive-data).

**Hur man genomför:**

- Använd [Alltid krypterad](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) för att säkerställa att känsliga data inte exponeras i klartext i Azure SQL Database, även i minnet/används. Alltid krypterad skyddar data från databasadministratörer (DBA) och molnadministratörer (eller dåliga aktörer som kan personifiera högprivilegierade men obehöriga användare) och ger dig mer kontroll över vem som kan komma åt dina data.

**Metodtips:**

- Alltid krypterad är inte en ersättning för att kryptera data i vila (TDE) eller under överföring (SSL/TLS). Alltid krypterad bör inte användas för icke-känsliga data för att minimera prestanda och funktionalitetspåverkan. Användning Alltid krypterad tillsammans med TDE och Transport Layer Security (TLS) rekommenderas för omfattande skydd av data i vila, under överföring och användning. 

- Utvärdera effekten av att kryptera de identifierade känsliga datakolumnerna innan du distribuerar Alltid krypterad i en produktionsdatabas. I allmänhet minskar alltid krypterad funktionaliteten för frågor på krypterade kolumner och har andra begränsningar, som anges i [Alltid krypterad - Funktionsinformation](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details). Därför kan du behöva bakåtaktivera ditt program för att åter implementera funktionen, en fråga stöder inte, på klientsidan eller /och refactor ditt databasschema, inklusive definitioner av lagrade procedurer, funktioner, vyer och utlösare. Befintliga program kanske inte fungerar med krypterade kolumner om de inte följer begränsningarna och begränsningarna i Alltid krypterad. Ekosystemet för Microsofts verktyg, produkter och tjänster som stöder Always Encrypted växer, men ett antal av dem fungerar inte med krypterade kolumner. Om du krypterar en kolumn kan frågeprestanda också påverka frågeprestanda, beroende på arbetsbelastningens egenskaper. 

- Hantera alltid krypterade nycklar med rollseparering om du använder Alltid krypterad för att skydda data från skadliga DBAs. Med rollseparering skapar en säkerhetsadministratör de fysiska nycklarna. DBA skapar kolumnhuvudnyckeln och kolumnkrypteringsnyckelmetadataobjekten, som beskriver de fysiska nycklarna, i databasen. Under den här processen behöver säkerhetsadministratören inte åtkomst till databasen och DBA behöver inte åtkomst till de fysiska nycklarna i klartext. 
  - Mer information finns i artikeln [Managing Keys with Role Separation.](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) 

- Lagra dina kolumnhuvudnycklar i Azure Key Vault för enkel hantering. Undvik att använda Windows Certificate Store (och i allmänhet distribuerade nyckellagringslösningar, i motsats till centrala nyckelhanteringslösningar) som gör nyckelhanteringen svår. 

- Tänk noga igenom kompromisserna med att använda flera nycklar (kolumnhuvudnyckel eller kolumnkrypteringsnycklar). Håll antalet nycklar litet för att minska kostnaderna för nyckelhantering. En kolumnhuvudnyckel och en kolumnkrypteringsnyckel per databas är vanligtvis tillräcklig i miljöer med stabilt tillstånd (inte mitt i en nyckelrotation). Du kan behöva ytterligare nycklar om du har olika användargrupper, var och en med olika nycklar och åtkomst till olika data.  

- Rotera kolumnhuvudtangenterna enligt dina efterlevnadskrav. Om du också behöver rotera krypteringsnycklar för kolumner bör du överväga att använda onlinekryptering för att minimera stilleståndstiden för programmet. 
  - Se artikeln, [Prestanda och tillgänglighet överväganden](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations). 

- Använd deterministisk kryptering om beräkningar (likhet) på data måste stödjas. Annars använder du randomiserad kryptering. Undvik att använda deterministisk kryptering för datauppsättningar med låg entropin eller datauppsättningar med allmänt känd distribution. 

- Om du är orolig för att tredje part ska komma åt dina data lagligt utan ditt medgivande, se till att alla program och verktyg som har åtkomst till nycklar och data i klartext körs utanför Microsoft Azure Cloud. Utan åtkomst till nycklarna, kommer den tredje parten har ingen möjlighet att dekryptera data om de inte kringgå krypteringen.

- Alltid krypterad stöder inte enkelt att bevilja tillfällig åtkomst till nycklarna (och skyddade data). Till exempel, om du behöver dela nycklarna med en DBA så att DBA att göra vissa rensningsåtgärder på känsliga och krypterade data. Det enda sättet att tillförlitlighet återkalla åtkomsten till data från DBA kommer att rotera både kolumn krypteringsnycklar och kolumn huvudnycklar skydda data, vilket är en dyr åtgärd. 

- För att komma åt oformaterade värden i krypterade kolumner måste en användare ha åtkomst till CMK som skyddar kolumnerna, som är konfigurerad i nyckelarkivet som innehåller CMK. Användaren måste också ha **visa alla kolumn huvudnyckel definition** och visa alla kolumn kryptering nyckel **definition** databas behörigheter.

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>Kontrollera programanvändarnas åtkomst till känsliga data via kryptering

Kryptering kan användas som ett sätt att säkerställa att endast specifika programanvändare som har åtkomst till kryptografiska nycklar kan visa eller uppdatera data.

**Hur man genomför:**

- Använd kryptering på cellnivå (CLE). Mer information finns i artikeln [Kryptera en kolumn med data.](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) 
- Använd Alltid krypterad, men var medveten om dess begränsning. Begränsningarna anges nedan.

**Metodtips**

När du använder CLE:

- Kontrollera åtkomsten till nycklar via SQL-behörigheter och roller. 

- Använd AES (AES 256 rekommenderas) för datakryptering. Algoritmer, sådana RC4, DES och TripleDES, är inaktuella och bör inte användas på grund av kända sårbarheter. 

- Skydda symmetriska nycklar med asymmetriska nycklar/certifikat (inte lösenord) för att undvika att använda 3DES. 

- Var försiktig när du migrerar en databas med cellnivåkryptering via export/import (bacpac-filer). 
  - Se artikeln [Rekommendationer för att använda cellnivåkryptering i Azure SQL Database](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/) om hur du förhindrar att förlora nycklar när du migrerar data och för andra riktlinjer för bästa praxis.

Tänk på att Always Encrypted främst är utformad för att skydda känsliga data som används från användare med hög behörighet från Azure SQL Database (molnoperatörer, DBAs) - se [Skydda känsliga data som används från obehöriga användare](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)med hög behörighet . Var medveten om följande utmaningar när du använder Alltid krypterad för att skydda data från programanvändare:

- Som standard upprätthåller alla Microsoft-klientdrivrutiner som stöder Always Encrypted en global cache (en per program) med kolumnkrypteringsnycklar. När en klientdrivrutin hämtar en krypteringsnyckel för en klartextkolumn genom att kontakta ett nyckelarkiv med en kolumnnyckel, cachelagras krypteringsnyckeln för enkeltext. Detta gör att isolera data från användare av ett program med flera användare utmanande. Om ditt program personifierar slutanvändare när de interagerar med en nyckellagring (till exempel Azure Key Vault) kommer en efterföljande fråga som kräver samma nyckel men som utlöses av en annan användare att använda cachelagrade nyckeln. Drivrutinen anropar inte nyckelarkivet och det kontrollerar inte om den andra användaren har behörighet att komma åt kolumnkrypteringsnyckeln. Som ett resultat kan användaren se krypterade data även om användaren inte har tillgång till nycklarna. Om du vill uppnå isolering av användare i ett program för flera användare kan du inaktivera cachelagring av kolumnkrypteringsnyckel. Om du inaktiverar cachelagring får du ytterligare prestandakostnader, eftersom drivrutinen måste kontakta nyckelarkivet för varje datakryptering eller dekrypteringsåtgärd.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>Skydda data mot obehörig visning av programanvändare samtidigt som dataformatet bevaras
En annan teknik för att förhindra obehöriga användare från att visa data är att fördunkla eller maskera data samtidigt som datatyper och format bevaras för att säkerställa att användarprogram kan fortsätta att hantera och visa data.

**Hur man genomför:**

- Använd [Dynamisk datamaskering](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) för att dölja tabellkolumner.

> [!NOTE]
> Alltid krypterad fungerar inte med dynamisk datamaskering. Det går inte att kryptera och maskera samma kolumn, vilket innebär att du måste prioritera skydd av data som används jämfört med att maskera data för appanvändarna via Dynamisk datamaskering.

**Metodtips:**

> [!NOTE]
> Dynamisk datamaskering kan inte användas för att skydda data från användare med hög behörighet. Maskeringsprinciper gäller inte för användare med administrativ åtkomst som db_owner.

- Tillåt inte appanvändare att köra ad hoc-frågor (eftersom de kanske kan komma runt dynamisk datamaskering).  
  - Se artikeln, [Kringgå maskering med inferens eller brute-force tekniker](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) för detaljer.  

- Använd en korrekt åtkomstkontrollprincip (via SQL-behörigheter, roller, RLS) för att begränsa användarbehörigheter för att göra uppdateringar i de maskerade kolumnerna. Att skapa en mask i en kolumn förhindrar inte uppdateringar av den kolumnen. Användare som får maskerade data när de frågar den maskerade kolumnen kan uppdatera data om de har skrivbehörighet.    

-  Dynamisk datamaskering bevarar inte de statistiska egenskaperna för de maskerade värdena. Detta kan påverka frågeresultat (till exempel frågor som innehåller filtreringspredikater eller kopplingar på maskerade data).

## <a name="network-security"></a>Nätverkssäkerhet
Nätverkssäkerhet avser åtkomstkontroller och metodtips för att skydda dina data under överföring till Azure SQL Database.

### <a name="configure-my-client-to-connect-securely-to-azure-sql-database"></a>Konfigurera min klient så att den är säker anslutning till Azure SQL Database 
Metodtips för hur du förhindrar klientdatorer och program med välkända sårbarheter (till exempel med hjälp av äldre TLS-protokoll och chiffersviter) från att ansluta till Azure SQL Database.

**Hur man genomför:**

- Kontrollera att klientdatorer som ansluter till Azure SQL Database använder [TLS (Transport Layer Security).](sql-database-security-overview.md#transport-layer-security-tls-encryption-in-transit)

**Metodtips:**

- Konfigurera alla dina appar och verktyg för att ansluta till SQL DB med kryptering aktiverad 
  - Kryptera = På, TrustServerCertificate = Av (eller motsvarande med drivrutiner som inte kommer från Microsoft). 

- Om din app använder en drivrutin som inte stöder TLS eller stöder en äldre version av TLS ersätter du om möjligt drivrutinen. Om det inte är möjligt, noggrant utvärdera säkerhetsriskerna. 

- Minska angreppsvektorer via säkerhetsproblem i SSL 2.0, SSL 3.0, TLS 1.0 och TLS 1.1 genom att inaktivera dem på klientdatorer som ansluter till Registerinställningarna för Azure SQL Database per [Transport Layer Security (TLS).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10) 

- Kontrollera chiffersviter som är tillgängliga på klienten: [Cipher Suites i TLS/SSL (Schannel SSP).](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel) Inaktivera 3DES per [konfigurera TLS Cipher Suite Order](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order). 

- För Azure SQL Database tillämpas kryptering för både proxy- och omdirigeringsanslutningstyper. Om du använder en hanterad **Proxy** instans använder du proxyanslutningstypen (standard) eftersom krypteringen från serversidan efterlyssningar. Anslutningstypen **Omdirigera** stöder för närvarande inte tvingande kryptering och är endast tillgänglig på privata IP-anslutningar. 

- Mer information finns i [Azure SQL Connectivity Architecture - Connection policy](sql-database-connectivity-architecture.md#connection-policy).


### <a name="minimize-attack-surface"></a>Minimera attackytan
Minimera antalet funktioner som kan angripas av en obehörig användare. Implementera nätverksåtkomstkontroller för Azure SQL Database.

> Nämns i: OSA Practice #5

**Hur man genomför:**

I en Azure SQL Database-server (som innehåller singleton-databas eller elastiska pooler):
- Ange Tillåt åtkomst till Azure-tjänster till OFF.

- Använd slutpunkter för VNet-tjänsten och VNet-brandväggsregler.

- Använd privat länk (förhandsgranskning).

I en hanterad instans:
- Följ riktlinjerna i [Nätverkskrav](sql-database-managed-instance-connectivity-architecture.md#network-requirements). 

**Metodtips:**

- Begränsa åtkomsten till Azure SQL Database genom att ansluta på en privat slutpunkt (till exempel med hjälp av en privat datasökväg): 
  - En hanterad instans kan isoleras i ett virtuella nätverk för att förhindra extern åtkomst. Program och verktyg som finns i samma eller peer-peer-virtuella nätverk i samma region kan komma åt det direkt. Program och verktyg som finns i olika regioner kan använda VNet-till-VNet-anslutning eller ExpressRoute-krets peering för att upprätta anslutning. Kunden bör använda NSG (Network Security Groups) för att begränsa åtkomsten över port 1433 endast till resurser som kräver åtkomst till en hanterad instans 
  - För en SQL Database-server (som innehåller enskilda databaser eller elastiska pooler) använder du funktionen [Privat länk](sql-database-private-endpoint-overview.md) som tillhandahåller en dedikerad privat IP för SQL Database-servern i ditt virtuella nätverk. Du kan också använda [VNet-tjänstslutpunkter med VNet-brandväggsregler](sql-database-vnet-service-endpoint-rule-overview.md) för att begränsa åtkomsten till SQL Database-servrarna.
  - Mobila användare bör använda punkt-till-plats VPN-anslutningar för att ansluta via datasökvägen.
  - Användare som är anslutna till deras lokala nätverk bör använda VPN-anslutning från plats till plats eller ExpressRoute för att ansluta via datasökvägen.

- Du kan komma åt Azure SQL Database genom att ansluta till en offentlig slutpunkt (till exempel med hjälp av en offentlig datasökväg). Följande bästa praxis bör beaktas: 
  - För en SQL Database-server använder du [IP-brandväggsregler](sql-database-firewall-configure.md) för att begränsa åtkomsten till endast auktoriserade IP-adresser.
  - För en hanterad instans använder du NSG (Network Security Groups) för att begränsa åtkomsten över port 3342 endast till nödvändiga resurser. Mer information finns i [Använda en Hanterad Azure SQL-databas-instans på ett säkert sätt med offentliga slutpunkter](sql-database-managed-instance-public-endpoint-securely.md). 

> [!NOTE]
> En offentlig slutpunkt för hanterade instanser är inte aktiverad som standard och måste uttryckligen aktiveras. Om företagsprincipen inte tillåter användning av offentliga slutpunkter använder du [Azure Policy](../governance/policy/overview.md) för att förhindra att offentliga slutpunkter aktiveras från början.

- Konfigurera Azure Networking-komponenter: 
  - Följ [Azures metodtips för nätverkssäkerhet](../security/fundamentals/network-best-practices.md).
  - Planera konfigurationen för virtuellt nätverk (VNet) enligt metodtips som beskrivs i Vanliga frågor och svar om vanliga frågor och svar [om Azure Virtual Network(Vanliga frågor och svar).](../virtual-network/virtual-networks-faq.md) 
  - Segmentera ett virtuella nätverk i flera undernät och tilldela resurser för liknande roll till samma undernät (till exempel front-end vs backend-resurser).
  - Använd [NSG (Network Security Groups)](../virtual-network/security-overview.md) för att styra trafiken mellan undernäten innanför Azure VNet-gränsen.
  - Aktivera [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) för din prenumeration för att övervaka inkommande och utgående nätverkstrafik.

### <a name="configure-power-bi-for-secure-connections-to-azure-sql-database"></a>Konfigurera Power BI för säkra anslutningar till Azure SQL Database

**Metodtips:**

- Använd privat datasökväg när det är möjligt för Power BI Desktop. 

- Kontrollera att Power BI Desktop ansluter med TLS1.2 genom att ange registernyckeln på klientdatorn enligt [TLS-registerinställningar (Transport Layer Security).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) 

- Begränsa dataåtkomsten för specifika användare via [RLS (Row-level security) med Power BI](https://docs.microsoft.com/power-bi/service-admin-rls). 

- För Power BI-tjänsten använder du den [lokala datagatewayen](https://docs.microsoft.com/power-bi/service-gateway-onprem)med tanke på [begränsningar och överväganden](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway).

### <a name="configure-app-service-for-secure-connections-to-azure-sql-database"></a>Konfigurera AppTjänst för säkra anslutningar till Azure SQL Database

**Metodtips:**

- För en enkel webapp kräver anslutning via offentlig slutpunkt inställningen **Tillåt Azure Services** till ON. 

- [Integrera din app med ett Virtuellt Azure-nätverk](../app-service/web-sites-integrate-with-vnet.md) för privat datasökvägsanslutning till en hanterad instans. Du kan också distribuera en webbapp med [ASE (App Service Environments).](../app-service/environment/intro.md) 

- För Web App med ASE eller VNet Integrated Web App som ansluter till en databas i SQL Database-servern kan du använda [VNet-tjänstslutpunkter och VNet-brandväggsregler](sql-database-vnet-service-endpoint-rule-overview.md) för att begränsa åtkomsten från ett visst virtuella nätverk och undernät. Ställ sedan in **Tillåt Azure-tjänster** till OFF. Du kan också ansluta ASE till en hanterad instans via en privat datasökväg.  

- Kontrollera att din webbapp är konfigurerad enligt artikeln, [Metodtips för att skydda PaaS webb- och mobilappar med Hjälp av Azure App Service](../security/security-paas-applications-using-app-services.md). 

- Installera [WAF (Web Application Firewall)](../application-gateway/waf-overview.md) för att skydda din webbapp från vanliga kryphål och sårbarheter.

### <a name="configure-azure-vm-hosting-for-secure-connections-to-azure-sql-database"></a>Konfigurera Azure VM-värd för säkra anslutningar till Azure SQL Database

**Metodtips:**

- Använd en kombination av Tillåt och neka regler för NSG:erna för virtuella Azure-datorer för att styra vilka regioner som kan nås från den virtuella datorn.

- Se till att din virtuella dator är konfigurerad enligt artikeln, [Säkerhetstips för IaaS-arbetsbelastningar i Azure](../security/azure-security-iaas.md).

- Kontrollera att alla virtuella datorer är associerade med ett visst virtuella nätverk och undernät. 

- Utvärdera om du behöver standardväg 0.0.0.0/Internet per vägledning vid [om påtvingad tunnel .](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling) 
  - Om ja – till exempel frontend-undernätet – behåller du standardrutten.
  - Om nej – till exempel mellannivå eller backend-undernät – aktiverar du krafttunneler så att ingen trafik går över Internet för att nå lokalt (även om det finns flera lokaler). 

- Implementera [valfria standardvägar](../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) om du använder peering eller anslutning till lokalt. 

- Implementera [användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md#user-defined) om du behöver skicka all trafik i det virtuella nätverket till en virtuell nätverksinstallation för paketbesiktning. 

- Använd [VNet-tjänstslutpunkter](sql-database-vnet-service-endpoint-rule-overview.md) för säker åtkomst till PaaS-tjänster som Azure Storage via Azure-stamnätet. 

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>Skydda mot DDoS-attacker (Distributed Denial of Service)
DDoS-attacker (Distributed Denial of Service) är försök av en obehörig användare att skicka en flod av nätverkstrafik till Azure SQL Database i syfte att överväldiga Azure-infrastrukturen och få den att avvisa giltiga inloggningar och arbetsbelastningar.

> Nämns i: OSA Practice #9

**Hur man genomför:**

DDoS-skydd aktiveras automatiskt som en del av Azure-plattformen. Det omfattar alltid trafikövervakning och begränsning i realtid av attacker på nätverksnivå mot offentliga slutpunkter. 

- Använd [Azure DDoS Protection](../virtual-network/ddos-protection-overview.md) för att övervaka offentliga IP-adresser som är associerade till resurser som distribueras i virtuella nätverk.

- Använd [avancerat skydd mot hot för Azure SQL Database för](sql-database-threat-detection-overview.md) att identifiera DoS-attacker (Denial of Service) mot databaser.

**Metodtips:**

- Följ de metoder som beskrivs i [Minimera attackytan](#minimize-attack-surface) hjälper till att minimera DDoS-angreppshot. 

- Den avancerade threat protection **brute force SQL-autentiseringsuppgifter** varning hjälper till att upptäcka brute force attacker. I vissa fall kan aviseringen till och med skilja mellan arbetsbelastningar för penetrationstester. 

- För Azure VM-värdprogram som ansluter till SQL Database: 
  - Följ rekommendationen att begränsa åtkomst via Internet-vända slutpunkter i Azure Security Center. 
  - Använd skalningsuppsättningar för virtuella datorer för att köra flera instanser av ditt program på virtuella Azure-datorer. 
  - Inaktivera RDP och SSH från Internet för att förhindra brute force-attack. 

## <a name="monitoring-logging-and-auditing"></a>Övervakning, loggning och revision  
Det här avsnittet refererar till funktioner som hjälper dig att identifiera avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Den beskriver också metodtips för att konfigurera databasgranskning för att spåra och samla in databashändelser.

### <a name="protect-databases-against-attacks"></a>Skydda databaser mot attacker 
Avancerat skydd mot hot gör att du kan identifiera och svara på potentiella hot när de uppstår genom att tillhandahålla säkerhetsaviseringar om avvikande aktiviteter.

**Hur man genomför:**

- Använd [Avancerat skydd mot hot för SQL för](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts) att identifiera ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser, inklusive:
  - SQL injektion attack.
  - Autentiseringsuppgifter stöld / läcka.
  - Privilegium missbruk.
  - Dataexfiltration.

**Metodtips:**

- Konfigurera [ADVANCED Data Security (ADS)](sql-database-advanced-data-security.md#getting-started-with-ads) för Azure SQL Database för en specifik SQL Database-server eller en hanterad instans. Du kan också konfigurera ADS för alla SQL Database-servrar och hanterade instanser i en prenumeration genom att byta till [Azure Security Center Standard-nivå](../security-center/security-center-pricing.md). 

- För en fullständig undersökningsupplevelse rekommenderas att [SQL Database Auditing](sql-database-auditing.md)aktiveras . Med granskning kan du spåra databashändelser och skriva dem till en granskningslogg i ett Azure Storage-konto eller Azure Log Analytics-arbetsyta. 

### <a name="audit-critical-security-events"></a>Granska kritiska säkerhetshändelser
Spårning av databashändelser hjälper dig att förstå databasaktivitet. Du kan få insikt i avvikelser och avvikelser som kan tyda på affärsproblem eller misstänkta säkerhetsöverträdelser. Det möjliggör och underlättar också efterlevnaden av efterlevnadsstandarder. 

**Hur man genomför:**

- Aktivera [SQL Database Auditing](sql-database-auditing.md) för att spåra databashändelser och skriva dem till en granskningslogg i ditt Azure Storage-konto, Log Analytics-arbetsyta (förhandsversion) eller Event Hubs (förhandsversion). 

- Granskningsloggar kan skrivas till ett Azure Storage-konto, till en Log Analytics-arbetsyta för förbrukning av Azure Monitor-loggar eller till händelsehubb för förbrukning med hjälp av händelsehubb. Du kan konfigurera valfri kombination av dessa alternativ och granskningsloggar skrivs till var och en. 

**Metodtips:**

- Genom att konfigurera [SQL Database Auditing](sql-database-auditing.md) på databasservern för granskning av händelser granskas alla befintliga och nyskapade databaser på den servern.
- Som standard omfattar granskningsprincipen alla åtgärder (frågor, lagrade procedurer och lyckade och misslyckade inloggningar) mot databaserna, vilket kan resultera i stora mängder granskningsloggar. Det rekommenderas för kunder att [konfigurera granskning för olika typer av åtgärder och åtgärdsgrupper med PowerShell](sql-database-auditing.md#subheading-7). Konfigurera detta hjälper till att kontrollera antalet granskade åtgärder och minimera risken för händelseförlust. Anpassad granskningskonfiguration gör det möjligt för kunder att samla in endast granskningsdata som behövs.
- Granskningsloggar kan förbrukas direkt i [Azure-portalen](https://portal.azure.com/)eller från lagringsplatsen som konfigurerades. 


> [!NOTE]
> Om du aktiverar granskning i Log Analytics medför du kostnader baserat på inmatningshastigheter. Tänk på den associerade kostnaden med att använda det här [alternativet](https://azure.microsoft.com/pricing/details/monitor/)eller överväg att lagra granskningsloggarna i ett Azure-lagringskonto. 

**Ytterligare resurser:**

- [Granskning av SQL-databas](sql-database-auditing.md)
- [GRANSKNING av SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) 

### <a name="secure-audit-logs"></a>Säkra granskningsloggar
Begränsa åtkomsten till lagringskontot för att stödja ansvarsfördelning och för att skilja DBA från revisorer. 

**Hur man genomför:**

- När du sparar granskningsloggar i Azure Storage kontrollerar du att åtkomsten till lagringskontot är begränsad till de minimala säkerhetsprinciperna. Kontrollera vem som har åtkomst till lagringskontot.
    - Mer information finns i [Auktorisera åtkomst till Azure Storage](../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

**Metodtips:**

- Att kontrollera tillgången till revisionsmålet är ett nyckelbegrepp för att skilja DBA från revisorer. 

- När du granskar åtkomsten till känsliga data bör du överväga att skydda data med datakryptering för att undvika informationsläckage till revisorn. Mer information finns i avsnittet [Skydda känsliga data som används från obehöriga användare](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)med hög behörighet .

## <a name="security-management"></a>Säkerhetshantering

I det här avsnittet beskrivs de olika aspekterna och metodtipsen för att hantera säkerhetspositionen för databaser. Den innehåller metodtips för att se till att dina databaser är konfigurerade för att uppfylla säkerhetsstandarder, för att upptäcka och för att klassificera och spåra åtkomst till potentiellt känsliga data i dina databaser. 

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>Se till att databasen/databaserna är konfigurerade för att uppfylla bästa säkerhetspraxis 

Förbättra databasens säkerhet proaktivt genom att upptäcka och åtgärda potentiella databassårbarheter.

**Hur man genomför:**

- Aktivera [SQL Vulnerability Assessment](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) (VA) för att söka igenom databasen efter säkerhetsproblem och för att automatiskt köras regelbundet i dina databaser.

**Metodtips:**

- Kör initialt VA på dina databaser och iterera genom att åtgärda felaktiga kontroller som motsätter sig bästa säkerhetspraxis. Ställ in originalplaner för godkända konfigurationer tills genomsökningen kommer ut _ren_, eller alla kontroller har passerat.  

- Konfigurera regelbundna återkommande genomsökningar så att de körs en gång i veckan och konfigurera den relevanta personen så att den får sammanfattningsmeddelanden. 

- Granska VA-sammanfattningen efter varje veckogenomsökning. För alla säkerhetsproblem som hittas utvärderas avdriften från föregående skanningsresultat och ta reda på om kontrollen ska lösas. Granska om det finns en legitim orsak till ändringen i konfigurationen.   

- Lös kontroller och uppdatera originalplaner där det är relevant. Skapa biljettobjekt för att lösa åtgärder och spåra dessa tills de är lösta. 

**Ytterligare resurser:**

- [Sårbarhetsbedömning för SQL](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 
- [SQL Vulnerability Assessment-tjänsten hjälper dig att identifiera databassäkerhetsproblem](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>Identifiera och tagga känsliga data 

Upptäck kolumner som kan innehålla känsliga data. Vad som anses vara känsliga data beror i hög grad på kunden, efterlevnadsreglering, etc., och måste utvärderas av de användare som ansvarar för dessa data. Klassificera kolumnerna för att använda avancerade känslighetsbaserade gransknings- och skyddsscenarier. 

**Hur man genomför:**

- Använd [SQL Data Discovery and Classification](sql-database-data-discovery-and-classification.md) för att identifiera, klassificera, etikettera och skydda känsliga data i dina databaser. 
  - Visa klassificeringsrekommendationerna som skapas av den automatiska identifieringen i instrumentpanelen för SQL Data Discovery and Classification. Acceptera relevanta klassificeringar, så att dina känsliga data är ständigt taggade med klassificeringsetiketter. 
  - Lägg manuellt till klassificeringar för ytterligare känsliga datafält som inte upptäcktes av den automatiserade mekanismen. 
- Mer information finns i [SQL Data Discovery & Classification](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification).

**Metodtips:**

- Övervaka klassificeringsinstrumentpanelen regelbundet för en korrekt bedömning av databasens klassificeringstillstånd. En rapport om databasklassificeringstillståndet kan exporteras eller skrivas ut för att dela för efterlevnads- och granskningsändamål.

- Övervaka kontinuerligt status för rekommenderade känsliga data i SQL Vulnerability Assessment. Spåra regeln för identifiering av känsliga data och identifiera eventuell drift i de rekommenderade kolumnerna för klassificering.  

- Använd klassificeringen på ett sätt som är anpassat till organisationens specifika behov. Anpassa din informationsskyddsprincip (känslighetsetiketter, informationstyper, identifieringslogik) i [SQL-informationsskyddsprincipen](../security-center/security-center-info-protection-policy.md) i Azure Security Center. 

### <a name="track-access-to-sensitive-data"></a>Spåra åtkomst till känsliga data 
Övervaka vem som kommer åt känsliga data och samla in frågor om känsliga data i granskningsloggar.

**Hur man genomför:**

- Använd SQL Audit och dataklassificering i kombination. 
  - I [loggen](sql-database-auditing.md) för SQL Database Audit kan du spåra åtkomst specifikt till känsliga data. Du kan också visa information som data som har åtkomst till, samt dess känslighetsetikett. Mer information finns i [Granska åtkomst till känsliga data](sql-database-data-discovery-and-classification.md#subheading-3). 

**Metodtips:**

- Se metodtips för avsnitten Granskning och dataklassificering: 
  - [Granska kritiska säkerhetshändelser](#audit-critical-security-events) 
  - [Identifiera och tagga känsliga data](#identify-and-tag-sensitive-data) 

### <a name="visualize-security-and-compliance-status"></a>Visualisera säkerhets- och efterlevnadsstatus 

Använd ett enhetligt säkerhetshanteringssystem för infrastruktur som stärker säkerhetspositionen för dina datacenter (inklusive SQL-databaser). Visa en lista med rekommendationer om säkerheten i dina databaser och efterlevnadsstatus.

**Hur man genomför:**

- Övervaka SQL-relaterade säkerhetsrekommendationer och aktiva hot i [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/).

## <a name="common-security-threats-and-potential-mitigations"></a>Vanliga säkerhetshot och potentiella begränsningar

Det här avsnittet hjälper dig att hitta säkerhetsåtgärder för att skydda mot vissa angreppsvektorer. Det förväntas att de flesta mildrande åtgärder kan uppnås genom att följa en eller flera av säkerhetsriktlinjerna ovan.

### <a name="security-threat-data-exfiltration"></a>Säkerhetshot: Dataexfiltration

Dataexfiltration är otillåten kopiering, överföring eller hämtning av data från en dator eller server. Se en definition för [dataexfiltration](https://en.wikipedia.org/wiki/Data_exfiltration) på Wikipedia.

När du ansluter till Azure SQL Database-servern via en offentlig slutpunkt utgörs en dataexfiltrationsrisk eftersom det kräver att kunderna öppnar sina brandväggar för offentliga IPs.  

**Scenario 1**: Ett program på en Virtuell Azure-dator ansluter till en databas i en Azure SQL Database-server. En skurk skådespelare får tillgång till den virtuella datorn och äventyrar den. I det här fallet innebär dataexfiltration att en extern entitet som använder den oseriösa virtuella datorn ansluter till databasen, kopierar personuppgifter och lagrar den i en blob-lagring eller en annan SQL-databas i en annan prenumeration.

**Scenario 2**: A Rouge DBA. Detta scenario tas ofta upp av säkerhetskänsliga kunder från reglerade branscher. I det här fallet kan en användare med hög behörighet kopiera data från Azure SQL Database till en annan prenumeration som inte kontrolleras av dataägaren.

**Potentiella mildrande åtgärder:**

Idag erbjuder Azure SQL Database följande tekniker för att minska dataexfiltrationshot: 

- Använd en kombination av Tillåt och neka regler för NSG:erna för virtuella Azure-datorer för att styra vilka regioner som kan nås från den virtuella datorn. 
- Om du använder en Azure SQL Database-server (som innehåller singleton-databaser eller elastiska pooler) anger du följande alternativ:
  - Tillåt Azure-tjänster att STÄNGA av.
  - Tillåt endast trafik från undernätet som innehåller din virtuella Azure-dator genom att konfigurera en VNet-brandväggsregel.
  - Använd [privat länk](sql-database-private-endpoint-overview.md)
- För en hanterad instans, med hjälp av privat IP-åtkomst som standard adresser den första data exfiltration oro för en skurken VM. Aktivera funktionen för undernätsdelegering i ett undernät för att automatiskt ange den mest restriktiva principen för ett hanterat förekomstundernät.
- Rogue DBA-problemet är mer exponerat med en hanterad instans eftersom den har en större yta och nätverkskraven är synliga för kunderna. Den bästa begränsningen för detta är att tillämpa alla metoder i den här säkerhetsguiden för att förhindra Rogue DBA-scenariot i första hand (inte bara för dataexfiltration). Alltid krypterad är en metod för att skydda känsliga data genom att kryptera den och hålla nyckeln otillgänglig för DBA.

## <a name="security-aspects-of-business-continuity-and-availability"></a>Säkerhetsaspekter av kontinuitet och tillgänglighet

De flesta säkerhetsstandarder behandlar datatillgänglighet när det gäller operativ kontinuitet, vilket uppnås genom att implementera redundans- och redundansfunktioner för att undvika enskilda felpunkter. För katastrofscenarier är det vanligt att säkerhetskopior av data- och loggfiler behålls.Följande avsnitt innehåller en översikt på hög nivå över de funktioner som är inbyggda i Azure. Det ger också ytterligare alternativ som kan konfigureras för att uppfylla specifika behov: 

- Azure erbjuder inbyggd hög tillgänglighet: [Hög tillgänglighet och Azure SQL Database](sql-database-high-availability.md) 

- Den affärskritiska nivån omfattar redundansgrupper, zoner med flera tillgängligheter, fullständiga och differentiella loggsäkerhetskopior och säkerhetskopiering av point-in-time-restore aktiverat som standard:  
  - [Hög tillgänglighet och Azure SQL Database - Zon redundant konfiguration](sql-database-high-availability.md#zone-redundant-configuration)
  - [Automatiserade säkerhetskopieringar](sql-database-automated-backups.md)
  - [Återställa en Azure SQL-databas med automatiska säkerhetskopieringar av databaser – återställning i point-in-time](sql-database-recovery-using-backups.md#point-in-time-restore)

- Ytterligare funktioner för affärskontinuitet, till exempel grupper för automatisk redundans i olika Azure-geos, kan konfigureras enligt beskrivningen här: [Översikt över affärskontinuitet med Azure SQL Database](sql-database-business-continuity.md)

## <a name="next-steps"></a>Nästa steg

- Se [En översikt över azure SQL Database-säkerhetsfunktioner](sql-database-security-overview.md)

---
title: TDE-Azure Key Vault-integrering eller Bring Your Own Key (BYOK) – Azure SQL Database | Microsoft Docs
description: Bring Your Own Key (BYOK) stöd för transparent datakryptering (TDE) med Azure Key Vault för SQL Database och informations lager. TDE med BYOK-översikt, fördelar, hur det fungerar, överväganden och rekommendationer.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 07/18/2019
ms.openlocfilehash: 6b1b706e68b090090ed4268b70b7c9d254f8b629
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596712"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-keys-in-azure-key-vault-bring-your-own-key-support"></a>Azure SQL transparent datakryptering med Kundhanterade nycklar i Azure Key Vault: Bring Your Own Key support

[Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) med Azure Key Vault-integrering kan kryptera databas krypterings nyckeln (DEK) med en kundhanterad asymmetrisk nyckel som kallas TDE-skydd. Detta kallas även Bring Your Own Key (BYOK) stöd för transparent datakryptering.  I BYOK-scenariot lagras TDE-skyddet i ett kundägda och hanterat [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), Azures molnbaserade hanterings system för extern nyckel. TDE-skydd kan [genereras](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates) av nyckel valvet eller [överföras](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) till nyckel valvet från en lokal HSM-enhet. TDE-DEK, som lagras på Start sidan i en databas, krypteras och dekrypteras av TDE-skyddskomponenten som lagras i Azure Key Vault, vilket den aldrig lämnar.  SQL Database måste beviljas behörighet till det kundägda nyckel valvet för att dekryptera och kryptera DEK. Om behörigheterna för den logiska SQL-servern till nyckel valvet har återkallats går det inte att komma åt databasen, anslutningarna nekas och alla data krypteras. För Azure SQL Database anges TDE-skydd på den logiska SQL Server-nivån och ärvs av alla databaser som är kopplade till den servern. För [Azure SQL-hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance)anges TDE-skydd på instans nivå och den ärvs av alla *krypterade* databaser på den instansen. Termen *Server* avser både server och instans i det här dokumentet, om inget annat anges.

> [!NOTE]
> Transparent datakryptering med Azure Key Vault integration (Bring Your Own Key) för Azure SQL Database hanterade instansen är i för hands version.


Med TDE med Azure Key Vault-integrering kan användare styra viktiga hanterings uppgifter, till exempel nyckel rotationer, nyckel valv, nyckel säkerhets kopior och aktivera granskning/rapportering på alla TDE-skydd med hjälp av Azure Key Vault-funktioner. Key Vault tillhandahåller central nyckel hantering, använder tätt övervakade HSM: er (Hardware Security modules) och gör det möjligt att dela upp uppgifter mellan hantering av nycklar och data för att möta efterlevnaden av säkerhets principer.  

TDE med Azure Key Vault-integrering ger följande fördelar:

- Ökad transparens och detaljerad kontroll med möjligheten att själv hantera TDE-skyddet
- Möjlighet att återkalla behörigheter när som helst för att rendera databasen otillgänglig
- Central hantering av TDE-skydd (tillsammans med andra nycklar och hemligheter som används i andra Azure-tjänster) genom att vara värd för dem i Key Vault
- Separering av ansvar för nyckel-och data hantering inom organisationen, för att stödja separering av uppgifter
- Bättre förtroende från dina egna klienter eftersom Key Vault har utformats så att Microsoft inte kan se eller extrahera några krypterings nycklar.
- Stöd för nyckel rotation

> [!IMPORTANT]
> För de som använder tjänstehanterade TDE som vill börja använda Key Vault förblir TDE aktiverade under processen för att växla över till ett TDE-skydd i Key Vault. Det finns ingen stillestånds tid eller Omkryptering av databasfilerna. Om du växlar från en tjänst-hanterad nyckel till en Key Vault-nyckel krävs bara återkryptering av databas krypterings nyckeln (DEK), som är en snabb och online-åtgärd.

## <a name="how-does-tde-with-azure-key-vault-integration-support-work"></a>Hur fungerar TDE med Azure Key Vault integrations support

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

![Autentisering av servern till Key Vault](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

När TDE först konfigureras för att använda ett TDE-skydd från Key Vault, skickar servern DEK för varje TDE-aktiverad databas till Key Vault för en begäran om att packa upp nycklar. Key Vault returnerar krypterings nyckeln för krypterade databaser, som lagras i användar databasen.  

> [!IMPORTANT]
> Det är viktigt att Observera att **när ett TDE-skydd lagras i Azure Key Vault lämnas aldrig Azure Key Vault**. Servern kan bara skicka nyckel åtgärds begär anden till nyckel materialet för TDE-skydd inom Key Vault och **får aldrig åtkomst till eller cachelagrar TDE-skyddet**. Key Vault administratören har rätt att återkalla Key Vault behörigheter för servern när som helst, och i så fall nekas alla anslutningar till databasen.

## <a name="guidelines-for-configuring-tde-with-azure-key-vault"></a>Rikt linjer för att konfigurera TDE med Azure Key Vault

### <a name="general-guidelines"></a>Allmänna riktlinjer

- Se till att Azure Key Vault och Azure SQL Database/hanterade instansen kommer att finnas i samma klient organisation.  Nyckel valv för flera klienter och Server interaktioner **stöds inte**.
- Om du planerar att en klient ska flyttas måste TDE med AKV konfigureras om, lära dig mer om att [Flytta resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- När du konfigurerar TDE med Azure Key Vault är det viktigt att tänka på belastningen i nyckel valvet genom upprepade omslutning/unwrap-åtgärder. Eftersom alla databaser som är kopplade till en SQL Database-Server använder samma TDE-skydd, utlöses till exempel en redundansväxling av servern som många viktiga åtgärder mot valvet eftersom det finns databaser på servern. Vi rekommenderar att du kopplar högst 500 standard-/Generell användning [-](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits)eller 200 Premium/affärskritisk-databaser med en Azure Key Vault i en enda prenumeration för att säkerställa en konsekvent hög tillgänglighet vid åtkomst till TDE-skyddskomponenten i valvet.
- Rekommenderas Behåll en kopia av TDE-skyddskomponenten lokalt.  Detta kräver en HSM-enhet för att skapa ett TDE-skydd lokalt och ett nyckel depositions-system för att lagra en lokal kopia av TDE-skyddskomponenten.  Lär dig [hur du överför en nyckel från en lokal HSM till Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).


### <a name="guidelines-for-configuring-azure-key-vault"></a>Rikt linjer för att konfigurera Azure Key Vault

- Skapa ett nyckel valv med skydd mot [mjuk borttagning](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) och rensning som är aktiverat för att skydda mot data förlust i händelse av oavsiktlig nyckel – eller Key Vault – borttagning.  Du måste använda [PowerShell för att aktivera egenskapen "mjuk borttagning"](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) i nyckel valvet (det här alternativet är inte tillgängligt från AKV-portalen ännu – men krävs av Azure SQL):  
  - Mjuk borttagna resurser behålls under en viss tids period, 90 dagar om de inte återställs eller rensas.
  - Åtgärder för att **återställa** och **Rensa** har sina egna behörigheter som är kopplade till en åtkomst princip för nyckel valv.
- Ange ett resurs lås i nyckel valvet för att kontrol lera vem som kan ta bort den här kritiska resursen och hjälp för att förhindra oavsiktlig eller obehörig borttagning.  [Läs mer om resurs lås](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- Ge SQL Database Server åtkomst till nyckel valvet med hjälp av Azure Active Directory (Azure AD)-identiteten.  När du använder användar gränssnittet för portalen skapas Azure AD-identiteten automatiskt och åtkomst behörigheterna för nyckel valvet beviljas till servern.  Genom att använda PowerShell för att konfigurera TDE med BYOK måste Azure AD-identiteten skapas och slutföras kontrol leras. Se [Konfigurera TDE med BYOK](transparent-data-encryption-byok-azure-sql-configure.md) och [Konfigurera TDE med BYOK för hanterad instans](https://aka.ms/sqlmibyoktdepowershell) för detaljerade steg-för-steg-instruktioner när du använder PowerShell.

   > [!NOTE]
   > Om Azure AD-identiteten **tas bort av misstag eller om serverns behörigheter återkallas** med nyckel valvets åtkomst princip eller oavsiktligt genom att flytta servern till en annan klient, förlorar servern åtkomst till nyckel valvet och TDE krypterade databaser kommer inte att vara tillgänglig och inloggningar nekas förrän den logiska serverns Azure AD-identitet och behörigheter har återställts.  

- När du använder brand väggar och virtuella nätverk med Azure Key Vault måste du tillåta att betrodda Microsoft-tjänster kringgår den här brand väggen. Välj Ja.

   > [!NOTE]
   > Om TDE krypterade SQL-databaser förlorar åtkomst till nyckel valvet, eftersom de inte kan kringgå brand väggen, kommer databaserna inte att vara tillgängliga och inloggningar nekas förrän kringgåde brand Väggs behörigheter har återställts.

- Aktivera granskning och rapportering på alla krypterings nycklar: Key Vault innehåller loggar som är lätta att mata in i andra verktyg för säkerhets informations-och händelse hantering (SIEM). [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) i Operations Management Suite (OMS) är ett exempel på en tjänst som redan är integrerad.
- För att säkerställa hög tillgänglighet för krypterade databaser konfigurerar du varje SQL Database Server med två Azure Key Vault som finns i olika regioner.


### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key"></a>Rikt linjer för att konfigurera TDE-skydd (asymmetrisk nyckel)

- Skapa din krypterings nyckel lokalt på en lokal HSM-enhet. Se till att det är en asymmetrisk, RSA 2048-eller RSA HSM 2048-nyckel så att den är storable i Azure Key Vault.
- Depositions nyckeln i ett nyckel depositions system.  
- Importera krypterings nyckel filen (. pfx,. BYOK eller. backup) till Azure Key Vault.

   > [!NOTE]
   > I test syfte är det möjligt att skapa en nyckel med Azure Key Vault, men den här nyckeln kan inte deponerats eftersom den privata nyckeln inte kan lämna nyckel valvet.  Säkerhetskopiera alltid och Depositions nycklar som används för att kryptera produktions data, eftersom den förlorade nyckeln (oavsiktlig borttagning i Key Vault, förfallo datum osv.) resulterar i permanent data förlust.

- Om du använder en nyckel med ett utgångs datum – implementera ett varnings system för förfallo datum för att rotera nyckeln innan den upphör att gälla: **när nyckeln har gått ut förlorar de krypterade databaserna åtkomst till deras TDE-skydd och kommer inte att vara tillgängligt** och alla inloggningar kommer att nekas tills nyckeln har roterats till en ny nyckel och valts som den nya nyckeln och standard skydds TDE för den logiska SQL-servern.
- Se till att nyckeln är aktive rad och har behörighet att utföra *Get*-, *wrap*-och unwrap-nycklar.
- Skapa en säkerhets kopia av en Azure Key Vault nyckel innan du använder nyckeln i Azure Key Vault för första gången. Läs mer om kommandot [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) .
- Skapa en ny säkerhets kopia när ändringar görs i nyckeln (till exempel lägga till ACL: er, Lägg till taggar, Lägg till nyckelattribut).
- **Behåll tidigare versioner** av nyckeln i nyckel valvet när du roterar nycklar, så att äldre databas säkerhets kopior kan återställas. När TDE-skyddet ändras för en databas, uppdateras gamla säkerhets kopior av databasen **inte** för att använda det senaste TDE-skyddet.  Varje säkerhets kopiering måste ha TDE-skyddskomponenten som den skapades med vid återställnings tiden. Nyckel rotationer kan utföras genom att följa anvisningarna i [rotera Transparent datakryptering-skydd med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Behåll alla tidigare använda nycklar i Azure Key Vault när du har ändrat tillbaka till tjänst hanterade nycklar.  Detta säkerställer att databas säkerhets kopior kan återställas med TDE-skydd som lagras i Azure Key Vault.  TDE-skydd som skapats med Azure Key Vault måste upprätthållas tills alla lagrade säkerhets kopior har skapats med tjänstens hanterade nycklar.  
- Gör återställnings bara säkerhets kopior av dessa nycklar med hjälp av [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey).
- Om du vill ta bort en potentiellt komprometterad nyckel under en säkerhets incident utan risken för data förlust, följer du stegen i [ta bort en potentiellt komprometterad nyckel](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

### <a name="guidelines-for-monitoring-the-tde-with-azure-key-vault-configuration"></a>Rikt linjer för att övervaka TDE med Azure Key Vault-konfiguration

Om den logiska SQL-servern förlorar åtkomsten till det Kundhanterade TDE-skyddskomponenten i Azure Key Vault, kommer databasen att neka alla anslutningar och visas oåtkomlig i Azure Portal.  De vanligaste orsakerna till detta är:
- Nyckel valv har tagits bort av misstag eller bakom en brand vägg
- Key Vault-nyckeln har tagits bort av misstag, inaktiverats eller gått ut
- Den logiska SQL Server instansen AppId togs bort av misstag
- Viktiga behörigheter för den logiska SQL Server instansen med AppId har återkallats

 > [!NOTE]
 > Databasen kommer att återställas själv och bli online automatiskt om åtkomsten till det Kundhanterade TDE-skyddet återställs inom 48 timmar.  Om databasen inte är tillgänglig på grund av ett tillfälligt nätverks avbrott, krävs ingen åtgärd och databaserna kommer att anslutas igen automatiskt.
  
- Mer information om hur du felsöker befintliga konfigurationer finns i [FELSÖKA TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde)

- Konfigurera följande Azure-funktioner för att övervaka databas tillstånd och aktivera avisering för förlust av TDE-skydds åtkomst:
    - [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview). En oåtkomlig databas som har förlorat åtkomst till TDE-skyddet visas som "ej tillgänglig" efter att den första anslutningen till databasen har nekats.
    - [Aktivitets logg](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) när åtkomst till TDE-skydd i det Kundhanterade nyckel valvet Miss lyckas, läggs poster till i aktivitets loggen.  Genom att skapa aviseringar för dessa händelser kan du återställa åtkomst så snart som möjligt.
    - [Åtgärds grupper](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) kan definieras för att skicka aviseringar och aviseringar baserat på dina inställningar, t. ex. e-post/SMS/push/röst, Logic app, webhook, ITSM eller Automation Runbook.
    

## <a name="high-availability-geo-replication-and-backup--restore"></a>Hög tillgänglighet, geo-replikering och säkerhets kopiering/återställning

### <a name="high-availability-and-disaster-recovery"></a>Hög tillgänglighet och haveriberedskap

Hur du konfigurerar hög tillgänglighet med Azure Key Vault beror på databasens konfiguration och SQL Database servern, och här är de rekommenderade konfigurationerna för två olika fall.  Det första fallet är en fristående databas eller SQL Database Server utan konfigurerad GEO-redundans.  Det andra fallet är en databas eller SQL Database Server som kon figurer ATS med failover-grupper eller GEO-redundans, där det måste säkerställas att varje Geo-redundant kopia har en lokal Azure Key Vault i gruppen redundans för att säkerställa att GEO-redundans fungerar.

I det första fallet rekommenderar vi att om du behöver hög tillgänglighet för en databas och SQL Database Server utan konfigurerad GEO-redundans, rekommenderar vi starkt att du konfigurerar servern att använda två olika nyckel valv i två olika regioner med samma nyckel material. Detta kan åstadkommas genom att skapa ett TDE-skydd med hjälp av den primära Key Vault samplacerad i samma region som SQL Database-servern och klona nyckeln till ett nyckel valv i en annan Azure-region, så att servern har åtkomst till ett andra nyckel valv ska vara den primära Key Vault upplever ett avbrott när databasen är igång. Använd cmdleten backup-AzKeyVaultKey för att hämta nyckeln i krypterat format från primär nyckel valvet och sedan använda cmdleten Restore-AzKeyVaultKey och ange ett nyckel valv i den andra regionen.

![En-server HA och ingen geo-Dr](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>Så här konfigurerar du geo-DR med Azure Key Vault

För att upprätthålla hög tillgänglighet för TDE-skydd för krypterade databaser, måste du konfigurera redundanta Azure Key Vault utifrån befintliga eller önskade SQL Database Redundansrelationer eller aktiva instanser av geo-replikering.  Varje geo-replikerad Server kräver ett separat nyckel valv, som måste samplaceras med servern i samma Azure-region. Om en primär databas blir otillgänglig på grund av ett avbrott i en region och redundansväxlingen utlöses kan den sekundära databasen ta över med det sekundära nyckel valvet.

Följande Azure Key Vault konfiguration krävs för geo-replikerade Azure SQL-databaser:

- En primär databas med ett nyckel valv i en region och en sekundär databas med ett nyckel valv i en region.
- Minst en sekundär krävs, upp till fyra sekundär servrar stöds.
- Sekundär zoner (länkning) stöds inte.

Följande avsnitt går igenom konfigurations-och konfigurations stegen i detalj.

### <a name="azure-key-vault-configuration-steps"></a>Azure Key Vault konfigurations steg

- Installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)
- Skapa två Azure Key Vault i två olika regioner med [PowerShell för att aktivera egenskapen "mjuk borttagning"](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) i nyckel valven (det här alternativet är inte tillgängligt från AKV-portalen ännu – men krävs av SQL).
- Både Azure Key Vault måste finnas i de två regionerna i samma Azure geo för att säkerhets kopiering och återställning av nycklar ska fungera.  Om du behöver de två nyckel valven som ska finnas i olika geografiska områden för att uppfylla SQL geo-DR-krav, följer du [BYOK-processen](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) som tillåter att nycklar importeras från en lokal HSM.
- Skapa en ny nyckel i det första nyckel valvet:  
  - RSA/RSA – HSA 2048-nyckel
  - Inga förfallo datum
  - Nyckeln är aktive rad och har behörighet att utföra get-, wrap-och unwrap Key-åtgärder
- Säkerhetskopiera primär nyckeln och Återställ nyckeln till det andra nyckel valvet.  Se [BackupAzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) och [restore-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey).

### <a name="azure-sql-database-configuration-steps"></a>Azure SQL Database konfigurations steg

Följande konfigurations steg skiljer sig från om du börjar med en ny SQL-distribution eller om du arbetar med en befintlig SQL geo-DR-distribution.  Vi disponerar konfigurations stegen för en ny distribution först och förklarar sedan hur du tilldelar TDE-skydd som lagras i Azure Key Vault till en befintlig distribution som redan har en geo-DR-länk etablerad.

**Steg för en ny distribution**:

- Skapa de två SQL Database-servrarna i samma två regioner som de tidigare skapade nyckel valvena.
- Välj fönstret SQL Database Server TDE och för varje SQL Database-Server:  
  - Välj AKV i samma region
  - Välj den nyckel som ska användas som TDE-skydd – varje server kommer att använda den lokala kopian av TDE-skyddet.
  - Om du gör detta i portalen skapas ett [AppID](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) för SQL Database-servern, som används för att tilldela SQL Database-Server behörighet att komma åt nyckel valvet – ta inte bort den här identiteten. Åtkomst kan återkallas genom att ta bort behörigheterna i Azure Key Vault i stället för SQL Database-servern, som används för att tilldela SQL Database Server behörighet att komma åt nyckel valvet.
- Skapa den primära databasen.
- Följ den [aktiva vägledningen för geo-replikering](sql-database-geo-replication-overview.md) för att slutföra scenariot, vilket innebär att den sekundära databasen skapas i det här steget.

![Redundansväxla grupper och geo-Dr](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

> [!NOTE]
> Det är viktigt att se till att samma TDE-skydd finns i båda nyckel valven innan du fortsätter att upprätta geo-länken mellan databaserna.

**Steg för en befintlig SQL-databas med geo-Dr-distribution**:

Eftersom SQL Database-servrarna redan finns och primära och sekundära databaser redan har tilldelats, måste stegen för att konfigurera Azure Key Vault utföras i följande ordning:

- Börja med SQL Database Server som är värd för den sekundära databasen:
  - Tilldela nyckel valvet som finns i samma region
  - Tilldela TDE-skyddskomponenten
- Gå nu till den SQL Database Server som är värd för den primära databasen:
  - Välj samma TDE-skydd som används för den sekundära databasen

![Redundansväxla grupper och geo-Dr](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

> [!NOTE]
> När du tilldelar nyckel valvet till servern är det viktigt att börja med den sekundära servern.  I det andra steget tilldelar du nyckel valvet till den primära servern och uppdaterar TDE-skyddet. geo-DR-länken fortsätter att fungera eftersom det TDE-skydd som används av den replikerade databasen är tillgängligt för båda servrarna.

Innan du aktiverar TDE med Kundhanterade nycklar i Azure Key Vault för ett SQL Database geo-DR-scenario, är det viktigt att skapa och underhålla två Azure Key Vault med identiskt innehåll i samma regioner som ska användas för SQL Database geo-replikering.  "Identiskt innehåll" innebär särskilt att båda nyckel valven måste innehålla kopior av samma TDE-skydd så att båda servrarna har åtkomst till TDE-skydd som används av alla databaser.  När du går vidare måste du hålla båda nyckel valven synkroniserade, vilket innebär att de måste innehålla samma kopior av TDE-skydd efter nyckel rotation, underhålla gamla versioner av nycklar som används för loggfiler eller säkerhets kopior. TDE-skydd måste ha samma nyckel egenskaper och nyckeln valv måste ha samma åtkomst behörigheter för SQL.  

Följ stegen i [Översikt över aktiv geo-replikering](sql-database-geo-replication-overview.md) för att testa och utlösa en redundansväxling, som bör utföras regelbundet för att bekräfta att åtkomst behörigheterna för SQL till båda nyckel valvena har underhållits.

### <a name="backup-and-restore"></a>Säkerhetskopiera och återställ

När en databas har krypterats med TDE med hjälp av en nyckel från Key Vault krypteras även eventuella genererade säkerhets kopior med samma TDE-skydd.

Om du vill återställa en säkerhets kopia som är krypterad med ett TDE-skydd från Key Vault kontrollerar du att nyckel materialet fortfarande finns kvar i det ursprungliga valvet under det ursprungliga nyckel namnet. När TDE-skyddet ändras för en databas, uppdateras gamla säkerhets kopior av databasen **inte** för att använda det senaste TDE-skyddet. Därför rekommenderar vi att du behåller alla gamla versioner av TDE-skyddskomponenten i Key Vault, så att databas säkerhets kopior kan återställas.

Om en nyckel som kan behövas för att återställa en säkerhets kopia inte längre finns i det ursprungliga nyckel valvet, returneras följande fel meddelande: "Mål servern `<Servername>` har inte åtkomst till alla AKV-URI: er \<som skapats mellan tidsstämpel #1 > och \<tidsstämpel #2 >. Försök igen när du har återställt alla AKV-URI: er. "

Du kan åtgärda detta genom att köra cmdleten [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) för att returnera listan över nycklar från Key Vault som har lagts till på servern (om de inte har tagits bort av en användare). Säkerställ att alla säkerhets kopior kan återställas genom att kontrol lera att mål servern för säkerhets kopian har åtkomst till alla dessa nycklar.

```powershell
Get-AzSqlServerKeyVaultKey `
  -ServerName <LogicalServerName> `
  -ResourceGroup <SQLDatabaseResourceGroupName>
```

Mer information om säkerhets kopierings återställning för SQL Database finns i [återställa en Azure SQL-databas](sql-database-recovery-using-backups.md). Mer information om säkerhets kopierings återställning för SQL Data Warehouse finns i [återställa en Azure SQL Data Warehouse](../sql-data-warehouse/backup-and-restore.md).

Ytterligare överväganden för säkerhetskopierade loggfiler: Säkerhetskopierade loggfiler förblir krypterade med den ursprungliga TDE-Krypteraren, även om TDE-skyddet roterades och databasen nu använder ett nytt TDE-skydd.  Vid återställnings tiden krävs båda nycklarna för att återställa databasen.  Om logg filen använder ett TDE-skydd som lagras i Azure Key Vault, krävs den här nyckeln vid återställnings tiden, även om databasen har ändrats för att använda tjänstehanterade TDE under tiden.

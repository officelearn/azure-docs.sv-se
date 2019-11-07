---
title: Kundhanterad transparent data kryptering (TDE) – Azure SQL Database | Microsoft Docs
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
ms.date: 11/04/2019
ms.openlocfilehash: 49ffed06936f8de2aed6d34ed83fca9e71ac0daf
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615715"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Azure SQL transparent datakryptering med kundhanterad nyckel

Azure SQL [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) med kundhanterad nyckel möjliggör Bring Your Own Key-scenario (BYOK) för data skydd i vila och gör det möjligt för organisationer att implementera separering av uppgifter i hanteringen av nycklar och data. Med kundhanterad transparent data kryptering är kunden ansvarig för och i en fullständig kontroll över en nyckel livs cykel hantering (nyckel skapande, uppladdning, rotation, borttagning), nyckel användnings behörigheter och granskning av åtgärder på nycklar.

I det här scenariot är den nyckel som används för kryptering av databas krypterings nyckeln (DEK), som kallas TDE-skydd, en kundhanterad asymmetrisk nyckel som lagras i ett kundägda och Kundhanterade [Azure Key Vault (AKV)](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), en molnbaserad hantering av externa nycklar säker. Key Vault har hög tillgänglighet och skalbart säkert lagrings utrymme för kryptografiska RSA-nycklar, som backas upp av FIPS 140-2 nivå 2, verifierade HSM: er (Hardware Security modules). Den tillåter inte direkt åtkomst till en lagrad nyckel, men tillhandahåller tjänster för kryptering/dekryptering med hjälp av nyckeln till auktoriserade entiteter. Nyckeln kan genereras av nyckel valvet, importeras eller [överföras till nyckel valvet från en lokal HSM-enhet](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).

För Azure SQL Database och Azure SQL Data Warehouse anges TDE-skydd på den logiska Server nivån och ärvs av alla krypterade databaser som är kopplade till den servern. För Azure SQL-hanterad instans anges TDE-skydd på instans nivå och ärvs av alla krypterade databaser på den instansen. Termen *Server* avser både att SQL Database logisk server och hanterad instans i det här dokumentet, om inget annat anges. 

> [!IMPORTANT]
> För de som använder tjänstehanterade TDE som vill börja använda Kundhanterade TDE-data förblir data krypterade under växlings processen och det finns ingen nedtid eller Omkryptering av databasfilerna. Om du växlar från en tjänst-hanterad nyckel till en kundhanterad nyckel krävs bara Omkryptering av DEK, vilket är en snabb och online-åtgärd.

## <a name="benefits-of-the-customer-managed-tde"></a>Fördelar med kund hanterade TDE

Kundhanterade TDE ger kunden följande fördelar:

- Fullständig och detaljerad kontroll över användning och hantering av TDE-skyddskomponenten;

- Transparens för användningen av TDE-skydd;

- Möjlighet att införa separering av uppgifter i hanteringen av nycklar och data inom organisationen.

- Key Vault administratör kan återkalla nyckel åtkomst behörigheter för att göra den krypterade databasen oåtkomlig.

- Central hantering av nycklar i AKV;

- Bättre förtroende från dina slut kunder, eftersom AKV är utformat så att Microsoft inte kan se eller extrahera krypterings nycklar.

## <a name="how-customer-managed-tde-works"></a>Så här fungerar Kundhanterade TDE

![Installation och funktion av Kundhanterade TDE](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

För att servern ska kunna använda TDE-skyddskomponenten som lagras i AKV för kryptering av DEK måste nyckel valv administratören ge följande åtkomst behörighet till servern med hjälp av sin unika AAD-identitet:

- **Hämta** för att hämta den offentliga delen och egenskaperna för nyckeln i Key Vault

- **wrapKey** -för att kunna skydda (kryptera) DEK

- **unwrapKey** -för att kunna ta bort skyddet (DEKRYPTERA) DEK

Key Vault-administratören kan också [Aktivera loggning av nyckel valv gransknings händelser](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault), så att de kan granskas senare.

När servern har kon figurer ATS för att använda ett TDE-skydd från AKV skickar servern DEK för varje TDE-aktiverad databas till nyckel valvet för kryptering. Key Vault returnerar den krypterade DEK, som sedan lagras i användar databasen.

Vid behov skickar servern skyddade DEK till nyckel valvet för dekryptering.

Granskare kan använda Azure Monitor för att granska Key Vault-AuditEvent loggar om loggning är aktiverat.


## <a name="requirements-for-configuring-customer-managed-tde"></a>Krav för att konfigurera Kundhanterade TDE

### <a name="requirements-for-configuring-akv"></a>Krav för att konfigurera AKV

- Key Vault och SQL Database/Managed instance måste tillhöra samma Azure Active Directory-klient. Nyckel valv för flera klienter och Server interaktioner stöds inte. Om du vill flytta resurserna efteråt måste TDE med AKV konfigureras om. Lär dig mer om att [Flytta resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

- Funktionen för [mjuk borttagning](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) måste vara aktive rad i nyckel valvet för att det ska gå att ta bort data förlust, oavsiktlig nyckel (eller Key Vault). Mjuka, borttagna resurser behålls i 90 dagar, såvida de inte återställs eller rensas av kunden under tiden. Åtgärder för att *återställa* och *Rensa* har sina egna behörigheter som är kopplade till en åtkomst princip för nyckel valv. Funktionen mjuk borttagning är inaktive rad som standard och kan aktive ras via [PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) eller [CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete). Den kan inte aktive ras via Azure Portal.  

- Bevilja SQL Database Server eller hanterad instans åtkomst till nyckel valvet (get, wrapKey, unwrapKey) med hjälp av dess Azure Active Directory identitet. När du använder Azure Portal skapas Azure AD-identiteten automatiskt. När du använder PowerShell eller CLI måste Azure AD-identiteten skapas och slutföras måste verifieras. Se [Konfigurera TDE med BYOK](transparent-data-encryption-byok-azure-sql-configure.md) och [Konfigurera TDE med BYOK för hanterad instans](https://aka.ms/sqlmibyoktdepowershell) för detaljerade steg-för-steg-instruktioner när du använder PowerShell.

- När du använder en brand vägg med AKV måste du aktivera alternativet *Tillåt att betrodda Microsoft-tjänster kringgår brand väggen*.

### <a name="requirements-for-configuring-tde-protector"></a>Krav för att konfigurera TDE-skydd

- TDE-skydd kan bara vara asymmetriskt, RSA 2048-eller RSA HSM 2048-nyckel.

- Nyckeln kan inte ha aktivering eller förfallo datum angivet.

- Nyckeln måste vara i aktiverat tillstånd i nyckel valvet.

- Om du importerar en befintlig nyckel till nyckel valvet ska du se till att tillhandahålla den i de fil format som stöds (. pfx,. BYOK eller. säkerhets kopiering).

## <a name="recommendations-when-configuring-customer-managed-tde"></a>Rekommendationer vid konfigurering av Kundhanterade TDE

### <a name="recommendations-when-configuring-akv"></a>Rekommendationer när du konfigurerar AKV

- Associera högst 500 Generell användning eller 200 Affärskritisk-databaser totalt med ett nyckel valv i en enda prenumeration för att säkerställa hög tillgänglighet när servern använder TDE-skydd i nyckel valvet. Dessa siffror baseras på upplevelsen och dokumenterat i [nyckel valvs tjänstens gränser](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits). Avsikten med detta är att förhindra problem efter serverns redundans, eftersom det utlöses som många viktiga åtgärder mot valvet eftersom det finns databaser på den servern. 

- Ange ett resurs lås i nyckel valvet för att kontrol lera vem som kan ta bort den här kritiska resursen och förhindra oavsiktlig eller obehörig borttagning. Läs mer om [resurs lås](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

- Aktivera granskning och rapportering på alla krypterings nycklar: Key Vault innehåller loggar som är lätta att mata in i andra säkerhets informations-och händelse hanterings verktyg. Operations Management Suite [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) är ett exempel på en tjänst som redan är integrerad.

- Länka varje server med två nyckel valv som finns i olika regioner och håll samma viktiga material för att säkerställa hög tillgänglighet för krypterade databaser. Markera bara nyckeln från nyckel valvet i samma region som ett TDE-skydd. Systemet kommer att använda

### <a name="recommendations-when-configuring-tde-protector"></a>Rekommendationer när du konfigurerar TDE-skydd
- Behåll en kopia av TDE-skydd på en säker plats eller depositions det till depositions-tjänsten. 

- Om nyckeln genereras i nyckel valvet skapar du en nyckel säkerhets kopia innan du använder nyckeln i AKV för första gången. Säkerhets kopieringen kan bara återställas till en Azure Key Vault. Läs mer om kommandot [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) .

- Skapa en ny säkerhets kopia när ändringar görs i nyckeln (t. ex. nyckelattribut, taggar, ACL: er).

- **Behåll tidigare versioner** av nyckeln i nyckel valvet när du roterar nycklar, så att äldre databas säkerhets kopior kan återställas. När TDE-skyddet ändras för en databas, uppdateras gamla säkerhets kopior av databasen **inte** för att använda det senaste TDE-skyddet. Vid återställnings tillfället behöver varje säkerhets kopiering TDE-skyddskomponenten som den krypterades med när den skapades. Nyckel rotationer kan utföras genom att följa anvisningarna i [rotera Transparent datakryptering-skydd med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).

- Behåll alla tidigare använda nycklar i AKV även efter att ha växlat till tjänstens hanterade nycklar. Det garanterar att säkerhets kopior av databaser kan återställas med TDE-skydd som lagras i AKV.  TDE-skydd som skapats med Azure Key Vault måste underhållas tills alla återstående lagrade säkerhets kopior har skapats med tjänst hanterade nycklar. Gör återställnings bara säkerhets kopior av dessa nycklar med hjälp av [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey).

- Om du vill ta bort en potentiellt komprometterad nyckel under en säkerhets incident utan risken för data förlust följer du stegen i [ta bort en potentiellt komprometterad nyckel](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

## <a name="inaccessible-tde-protector"></a>Oåtkomligt TDE-skydd

När transparent data kryptering har kon figurer ATS för att använda en kundhanterad nyckel, krävs kontinuerlig åtkomst till TDE-skydd för att databasen ska vara online. Om servern förlorar åtkomsten till det Kundhanterade TDE-skyddskomponenten i AKV, på upp till 10 minuter kommer en databas att starta neka alla anslutningar med motsvarande fel meddelande och ändra dess tillstånd till *otillgängligt*. Den enda åtgärden som tillåts för en databas i det otillgängliga läget tar bort den.

> [!NOTE]
> Om databasen inte är tillgänglig på grund av ett tillfälligt nätverks avbrott, krävs ingen åtgärd och databaserna kommer att anslutas igen automatiskt.

När åtkomst till nyckeln har återställts krävs ytterligare tid och steg, vilket kan variera beroende på hur lång tid som förflutit utan åtkomst till nyckeln och storleken på data i databasen:

- Om nyckel åtkomsten återställs inom 8 timmar kommer databasen automatiskt att korrigeras inom nästa timma.

- Om nyckel åtkomsten återställs efter mer än 8 timmar är den automatiska återställningen inte möjlig och det kan ta lång tid att återställa databasen, beroende på databasens storlek och du måste öppna ett support ärende. När databasen är online igen, har tidigare konfigurerade inställningar på server nivå, till exempel konfiguration av [redundanskonfiguration](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) , punkt-i-tid-återställnings historik och taggar går förlorade. Därför rekommenderar vi att du implementerar ett meddelande system som gör det möjligt att identifiera och åtgärda de underliggande åtkomst problemen för nycklar inom 8 timmar.

### <a name="accidental-tde-protector-access-revocation"></a>Oavsiktlig åtkomst till återkallning av TDE-skydd

Det kan hända att någon med tillräckliga åtkomst rättigheter till Key Vault oavsiktligt inaktiverar Server åtkomst till nyckeln av:

- återkallar nyckel valvets *Get*-, *wrapKey*-, *unwrapKey* -behörigheter från servern

- tar bort nyckeln

- tar bort nyckel valvet

- ändra brand Väggs regler för nyckel valvet

- tar bort den hanterade identiteten för servern i Azure Active Directory

Lär dig mer om [de vanligaste orsakerna till att databasen blir oåtkomlig](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible).

## <a name="monitoring-of-the-customer-managed-tde"></a>Övervakning av Kundhanterade TDE

Konfigurera följande Azure-funktioner för att övervaka databas tillstånd och aktivera avisering för förlust av TDE-skydds åtkomst:
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview). En oåtkomlig databas som har förlorat åtkomst till TDE-skyddet visas som "ej tillgänglig" efter att den första anslutningen till databasen har nekats.
- [Aktivitets logg](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) när åtkomst till TDE-skydd i det Kundhanterade nyckel valvet Miss lyckas, läggs poster till i aktivitets loggen.  Genom att skapa aviseringar för dessa händelser kan du återställa åtkomst så snart som möjligt.
- [Åtgärds grupper](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) kan definieras för att skicka aviseringar och aviseringar baserat på dina inställningar, t. ex. e-post/SMS/push/röst, Logic app, webhook, ITSM eller Automation Runbook.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Säkerhets kopiering och återställning av databasen med Kundhanterade TDE

När en databas har krypterats med TDE med hjälp av en nyckel från Key Vault krypteras även eventuella nyligen genererade säkerhets kopior med samma TDE-skydd. När TDE-skyddet ändras, **uppdateras inte** gamla säkerhets kopior av databasen för att använda det senaste TDE-skyddet.

För att återställa en säkerhets kopia krypterad med ett TDE-skydd från Key Vault, se till att nyckel materialet är tillgängligt för mål servern. Därför rekommenderar vi att du behåller alla gamla versioner av TDE-skyddskomponenten i Key Vault, så att databas säkerhets kopior kan återställas. 

> [!IMPORTANT]
> Det kan aldrig finnas fler än en TDE-skydds uppsättning för en server. Det är nyckeln markerat med "gör nyckeln till standard TDE skydd" på bladet Azure Portal. Flera ytterligare nycklar kan dock länkas till en server utan att markera dem som TDE skydd. Dessa nycklar används inte för att skydda DEK, men kan användas vid återställning från en säkerhets kopia, om säkerhets kopierings filen krypteras med nyckeln med motsvarande tumavtryck.

Om den nyckel som behövs för att återställa en säkerhets kopia inte längre är tillgänglig för mål servern, returneras följande fel meddelande på återställnings försöket: "mål servern `<Servername>` har inte åtkomst till alla AKV-URI: er som skapats mellan \<tidsstämpel #1 > och \<tidsstämpel #2 >. Försök igen när du har återställt alla AKV-URI: er. "

För att minimera den, kör cmdleten [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) för målet SQL Database logisk server eller [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) för mål hanterad instans för att returnera listan över tillgängliga nycklar och identifiera de som saknas. Se till att mål servern för återställningen har åtkomst till alla nycklar som behövs för att säkerställa att alla säkerhets kopior kan återställas. Dessa nycklar behöver inte markeras som TDE-skydd.

Mer information om säkerhets kopierings återställning för SQL Database finns i [återställa en Azure SQL-databas](sql-database-recovery-using-backups.md). Mer information om säkerhets kopierings återställning för SQL Data Warehouse finns i [återställa en Azure SQL Data Warehouse](../sql-data-warehouse/backup-and-restore.md). För SQL Server interna säkerhets kopiering/återställning med hanterade instanser, se [snabb start: återställa en databas till en hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) 

Ytterligare överväganden för loggfiler: säkerhetskopierade loggfiler förblir krypterade med det ursprungliga TDE-skyddet, även om det roterats och databasen nu använder ett nytt TDE-skydd.  Vid återställnings tiden krävs båda nycklarna för att återställa databasen.  Om logg filen använder ett TDE-skydd som lagras i Azure Key Vault, krävs den här nyckeln vid återställnings tiden, även om databasen har ändrats för att använda tjänstehanterade TDE under tiden.

## <a name="high-availability-with-customer-managed-tde"></a>Hög tillgänglighet med Kundhanterade TDE

Även om det inte finns någon konfigurerad GEO-redundans för Server, rekommenderar vi starkt att du konfigurerar servern att använda två olika nyckel valv i två olika regioner med samma nyckel material. Du kan åstadkomma detta genom att skapa ett TDE-skydd med hjälp av primär nyckel valvet som finns i samma region som servern och klona nyckeln till ett nyckel valv i en annan Azure-region, så att servern har åtkomst till ett andra nyckel valv ska exper för primär nyckel valvet ience ett avbrott när databasen är igång. 

Använd cmdleten backup-AzKeyVaultKey för att hämta nyckeln i krypterat format från primär nyckel valvet och sedan använda cmdleten Restore-AzKeyVaultKey och ange ett nyckel valv i den andra regionen för att klona nyckeln. Du kan också använda Azure Portal för att säkerhetskopiera och återställa nyckeln. Nyckeln i det sekundära nyckel valvet i se annan region bör inte markeras som TDE-skydd och det är inte ens tillåtet.

 Om det uppstår ett avbrott som påverkar primär nyckel valvet kommer systemet automatiskt att växla till den andra länkade nyckeln med samma tumavtryck i det sekundära nyckel valvet, om det finns. Observera att växeln inte sker om TDE-skyddskomponenten inte kan nås på grund av återkallade åtkomst rättigheter, eller att nyckel-eller nyckel valvet har tagits bort, eftersom det kan indikera att kunden avsiktligt ville begränsa servern från att komma åt nyckeln.

![En-server HA](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geo-DR och Kundhanterade TDE

Vid både [aktiva scenarier för geo-replikering](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) och [failover-grupper](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) kräver varje server som krävs ett separat nyckel valv, som måste samplaceras med servern i samma Azure-region. Kunden ansvarar för att hålla nyckel materialet över nyckel valven konsekvent, så att geo-Secondary är synkroniserat och kan ta över samma nyckel från det lokala nyckel valvet om primärt blir otillgängligt på grund av ett avbrott i regionen och redundansväxlingen utlöses . Upp till fyra sekundära servrar kan konfigureras och länkning (sekundär nyckel zoner) stöds inte.

För att undvika problem vid etablering eller under geo-replikering på grund av ofullständigt nyckel material, är det viktigt att följa dessa regler när du konfigurerar Kundhanterade TDE:

- Alla nyckel valv som ingår måste ha samma egenskaper och samma åtkomst rättigheter för respektive server.

- Alla nyckel valv som ingår måste innehålla identiska nyckel material. Den gäller inte bara för det aktuella TDE-skyddet, utan till alla tidigare TDE-skydd som kan användas i säkerhetskopieringsfilerna.

- Både inledande installation och rotation av TDE-skyddskomponenten måste utföras på den sekundära först och sedan på primär.

![Redundansväxla grupper och geo-Dr](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

Om du vill testa en redundansväxling följer du stegen i [Översikt över aktiv geo-replikering](sql-database-geo-replication-overview.md). Det bör göras regelbundet för att bekräfta att åtkomst behörigheterna för SQL till båda nyckel valvena har behållits.

## <a name="next-steps"></a>Nästa steg

Du kanske också vill kontrol lera följande PowerShell-exempel skript för vanliga åtgärder med Kundhanterade TDE:

- [Rotera transparent datakryptering-skyddet för SQL Database med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [Ta bort ett transparent datakryptering (TDE) skydd för SQL Database med hjälp av PowerShell](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [Hantera transparent datakryptering i en hanterad instans med din egen nyckel med hjälp av PowerShell](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)

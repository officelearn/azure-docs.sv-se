---
title: Kundhanterad transparent datakryptering (TDE)
description: Få ditt eget nyckel (BYOK) stöd för Transparent Data Encryption (TDE) med Azure Key Vault för SQL Database och Azure Synapse. TDE med BYOK översikt, fördelar, hur det fungerar, överväganden och rekommendationer.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/18/2020
ms.openlocfilehash: 462326fb16663a6f25ff4b51ea11791201086fd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528736"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Azure SQL Transparent datakryptering med kundhanterad nyckel

Azure SQL [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) med kundhanterad nyckel möjliggör Bring Your Own Key (BYOK) scenario för dataskydd i vila, och tillåter organisationer att implementera åtskillnad av uppgifter i hanteringen av nycklar och data. Med kundhanterad transparent datakryptering är kunden ansvarig för och i full kontroll över en nyckellivscykelhantering (nyckelskapande, uppladdning, rotation, borttagning), behörigheter för nyckelanvändning och granskning av åtgärder på nycklar.

I det här fallet är nyckeln som används för kryptering av databaskrypteringsnyckeln (DEK), kallad TDE-skydd, en kundhanterad asymmetrisk nyckel som lagras i ett kundägt och kundhanterat [Azure Key Vault (AKV),](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)ett molnbaserat externt nyckelhanteringssystem. Key Vault är mycket tillgänglig och skalbar säker lagring för RSA kryptografiska nycklar, eventuellt backas upp av FIPS 140-2 Nivå 2 validerade maskinvarusäkerhetsmoduler (HSM). Det tillåter inte direkt åtkomst till en lagrad nyckel, men tillhandahåller tjänster för kryptering/dekryptering med hjälp av nyckeln till de auktoriserade entiteterna. Nyckeln kan genereras av nyckelvalvet, importeras eller [överföras till nyckelvalvet från en HSM-enhet på prem](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).

För Azure SQL Database och Azure Synapse anges TDE-skydd på logisk servernivå och ärvs av alla krypterade databaser som är associeras med den servern. För Azure SQL Managed Instance anges TDE-protector på instansnivå och ärvs av alla krypterade databaser på den instansen. Termen *server* refererar både till SQL Database logisk server och hanterad instans i hela det här dokumentet, om inte annat anges.

> [!IMPORTANT]
> För dem som använder tjänsthanterad TDE som vill börja använda kundhanterad TDE förblir data krypterade under övergången, och det finns inga driftstopp eller omkryptering av databasfilerna. Att byta från en tjänsthanterad nyckel till en kundhanterad nyckel kräver bara omkryptering av DEK, vilket är en snabb och online-åtgärd.

## <a name="benefits-of-the-customer-managed-tde"></a>Fördelar med den kundhanterade TDE

Kundhanterad TDE ger kunden följande fördelar:

- Fullständig och detaljerad kontroll över användning och hantering av TDE-skydd.

- Transparens för användningen av TDE-skydd.

- Förmåga att genomföra åtskillnad mellan uppgifter i hanteringen av nycklar och data inom organisationen;

- Key Vault-administratören kan återkalla behörigheter för nyckelåtkomst för att göra krypterad databas otillgänglig.

- Central hantering av nycklar i AKV;

- Större förtroende från dina slutkunder, eftersom AKV är utformat så att Microsoft inte kan se eller extrahera krypteringsnycklar.

## <a name="how-customer-managed-tde-works"></a>Så här fungerar kundhanterad TDE

![Installation och funktion av den kundhanterade TDE](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

För att servern ska kunna använda TDE-skydd som lagras i AKV för kryptering av DEK måste nyckelvalvsadministratören ge följande åtkomsträttigheter till servern med hjälp av sin unika AAD-identitet:

- **get** - för att hämta den offentliga delen och egenskaperna för nyckeln i Key Vault

- **wrapKey** - för att kunna skydda (kryptera) DEK

- **unwrapKey** - för att kunna ta bort skydd (dekryptera) DEK

Nyckelvalvsadministratör kan också [aktivera loggning av viktiga valvgranskningshändelser,](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault)så att de kan granskas senare.

När servern är konfigurerad för att använda ett TDE-skydd från AKV skickar servern DEK för varje TDE-aktiverad databas till nyckelvalvet för kryptering. Nyckelvalv returnerar den krypterade DEK, som sedan lagras i användardatabasen.

När det behövs skickar servern skyddad DEK till nyckelvalvet för dekryptering.

Granskare kan använda Azure Monitor för att granska viktiga granskningsobjektloggar för valv, om loggning är aktiverat.

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Krav för att konfigurera kundhanterad TDE

### <a name="requirements-for-configuring-akv"></a>Krav för att konfigurera AKV

- Nyckelvalv och SQL Database/hanterad instans måste tillhöra samma Azure Active Directory-klientorganisation. Nyckelvalv och serverinteraktioner mellan klienter stöds inte. För att flytta resurser efteråt måste TDE med AKV konfigureras om. Läs mer om [att flytta resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

- [Mjuk borttagningsfunktionen](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) måste vara aktiverad i nyckelvalvet för att skydda mot oavsiktlig borttagning av oavsiktlig nyckel (eller nyckelvalv). Mjuk borttagna resurser behålls i 90 dagar, såvida de inte har återställts eller rensats av kunden under tiden. *Återställnings-* och *rensningsåtgärderna* har sina egna behörigheter som är associerade i en princip för nyckelvalvsåtkomst. Funktionen Mjuk borttagning är inaktiverad som standard och kan aktiveras via [PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) eller [CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete). Det går inte att aktivera via Azure-portalen.  

- Bevilja SQL Database-servern eller hanterad instansåtkomst till nyckelvalvet (hämta, wrapKey, unwrapKey) med hjälp av dess Azure Active Directory-identitet. När du använder Azure-portalen skapas Azure AD-identiteten automatiskt. När du använder PowerShell eller CLI måste Azure AD-identiteten uttryckligen skapas och slutförande kontrolleras. Se [Konfigurera TDE med BYOK](transparent-data-encryption-byok-azure-sql-configure.md) och [Konfigurera TDE med BYOK för hanterad instans](https://aka.ms/sqlmibyoktdepowershell) för detaljerade steg-för-steg-instruktioner när du använder PowerShell.

- När du använder brandvägg med AKV måste du aktivera alternativet *Tillåt betrodda Microsoft-tjänster att kringgå brandväggen*.

### <a name="requirements-for-configuring-tde-protector"></a>Krav för att konfigurera TDE-skydd

- TDE-skydd kan endast vara asymmetriskt, RSA 2048 eller RSA HSM 2048.TDE protector can be only asymmetric, RSA 2048 or RSA HSM 2048 key.

- Nyckelaktiveringsdatumet (om det är inställt) måste vara ett datum och en tid tidigare. Utgångsdatum (om det är inställt) måste vara ett framtida datum och en framtida tid.

- Nyckeln måste vara i tillståndet *Aktiverad.*

- Om du importerar befintlig nyckel till nyckelvalvet måste du ange den i de filformat som stöds (.pfx, .byok eller .backup).

## <a name="recommendations-when-configuring-customer-managed-tde"></a>Rekommendationer vid konfiguration av kundhanterad TDE

### <a name="recommendations-when-configuring-akv"></a>Rekommendationer vid konfiguration av AKV

- Associera högst 500 databaser med allmänt syfte eller 200 affärskritiska databaser totalt med ett nyckelvalv i en enda prenumeration för att säkerställa hög tillgänglighet när servern kommer åt TDE-skydd i nyckelvalvet. Dessa siffror är baserade på erfarenheten och dokumenteras i [de viktigaste valv tjänst gränser](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits). Avsikten här är att förhindra problem efter server redundans, eftersom det kommer att utlösa så många viktiga åtgärder mot valvet som det finns databaser i den servern.

- Ange ett resurslås på nyckelvalvet för att styra vem som kan ta bort den här kritiska resursen och förhindra oavsiktlig eller obehörig borttagning. Läs mer om [resurslås](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

- Aktivera granskning och rapportering på alla krypteringsnycklar: Key Vault tillhandahåller loggar som är lätta att injicera i annan säkerhetsinformation och händelsehanteringsverktyg. Operations Management Suite [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) är ett exempel på en tjänst som redan är integrerad.

- Länka varje server med två nyckelvalv som finns i olika regioner och har samma nyckelmaterial för att säkerställa hög tillgänglighet för krypterade databaser. Markera bara nyckeln från nyckelvalvet i samma region som ett TDE-skydd. Systemet växlar automatiskt till nyckelvalvet i fjärrområdet om det finns ett avbrott som påverkar nyckelvalvet i samma region.

### <a name="recommendations-when-configuring-tde-protector"></a>Rekommendationer vid konfiguration av TDE-skydd
- Förvara en kopia av TDE-skyddet på en säker plats eller spärra den till spärrtjänsten.

- Om nyckeln genereras i nyckelvalvet skapar du en nyckelsäkerhetskopia innan du använder nyckeln i AKV för första gången. Säkerhetskopiering kan återställas till ett Azure Key Vault. Läs mer om kommandot [Backup-AzKeyVaultKeyKey.](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)

- Skapa en ny säkerhetskopia när några ändringar görs i nyckeln (t.ex. nyckelattribut, taggar, ACL:er).

- **Behåll tidigare versioner** av nyckeln i nyckelvalvet när du roterar nycklar, så att äldre säkerhetskopieringar av databaser kan återställas. När TDE-skydd ändras för en databas **uppdateras inte** gamla säkerhetskopior av databasen för att använda det senaste TDE-skyddet. Vid återställningstillfället behöver varje säkerhetskopia det TDE-skydd som den krypterades med vid skapande. Nyckelrotationer kan utföras enligt instruktionerna på [Rotera transparent datakrypteringsskydd med PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).

- Behåll alla tidigare använda nycklar i AKV även efter byte till tjänsthanterade nycklar. Det säkerställer att säkerhetskopiering av databaser kan återställas med TDE-skydd som lagras i AKV.  TDE-skydd som skapats med Azure Key Vault måste underhållas tills alla återstående lagrade säkerhetskopior har skapats med tjänsthanterade nycklar. Gör återställningsbara säkerhetskopior av dessa nycklar med [Backup-AzKeyVaultKey .](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)

- Om du vill ta bort en potentiellt komprometterade nyckel under en säkerhetsincident utan risk för dataförlust följer du stegen från [nyckeln Ta bort en potentiellt komprometterade](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

## <a name="inaccessible-tde-protector"></a>Otillgänglig TDE-skydd

När transparent datakryptering är konfigurerad för att använda en kundhanterad nyckel krävs kontinuerlig åtkomst till TDE-skydd för att databasen ska vara online. Om servern förlorar åtkomsten till det kundhanterade TDE-skydd i AKV börjar en databas om upp till 10 minuter neka alla anslutningar med motsvarande felmeddelande och ändra dess tillstånd till *Otillgänglig*. Den enda åtgärd som tillåts i en databas i det otillgängliga tillståndet är att ta bort den.

> [!NOTE]
> Om databasen inte är tillgänglig på grund av ett intermittent nätverksutfall krävs ingen åtgärd och databaserna ansluts automatiskt igen.

När åtkomsten till nyckeln har återställts kräver det ytterligare tid och steg att ta databasen online igen, vilket kan variera beroende på den tid som förflutit utan åtkomst till nyckeln och storleken på data i databasen:

- Om nyckelåtkomsten återställs inom 8 timmar kommer databasen att läka automatiskt inom en timme.

- Om nyckelåtkomst återställs efter mer än 8 timmar är automatisk läkning inte möjlig och för att återställa databasen kräver ytterligare steg på portalen och kan ta en betydande tid beroende på databasens storlek. När databasen är online igen går tidigare konfigurerade inställningar på servernivå, till exempel [redundansgruppskonfiguration,](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) point-in-time-restore-historik och taggar **förlorade**. Därför rekommenderas att implementera ett meddelandesystem som gör att du kan identifiera och åtgärda de underliggande viktiga åtkomstproblemen inom 8 timmar.

### <a name="accidental-tde-protector-access-revocation"></a>Återkalla åtkomst för oavsiktlig åtkomst till TDE-skydd

Det kan hända att någon med tillräcklig åtkomst till nyckelvalvet av misstag inaktiverar serveråtkomst till nyckeln genom att:

- återkalla nyckelvalvets *hämta,* *wrapKey,* *unwrapKey-behörigheter* från servern

- ta bort nyckeln

- ta bort nyckelvalvet

- ändra brandväggsreglerna för nyckelvalvet

- ta bort serverns hanterade identitet i Azure Active Directory

Läs mer om [de vanligaste orsakerna till att databasen blir otillgänglig](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible).

## <a name="monitoring-of-the-customer-managed-tde"></a>Övervakning av den kundhanterade TDE

Om du vill övervaka databastillståndet och aktivera aviseringar för förlust av åtkomst till TDE-skydd konfigurerar du följande Azure-funktioner:
- [Azure Resource Hälsa](https://docs.microsoft.com/azure/service-health/resource-health-overview). En otillgänglig databas som har förlorat åtkomst till TDE-skydd visas som "Ej tillgänglig" efter att den första anslutningen till databasen har nekats.
- [Aktivitetslogg](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) när åtkomst till TDE-skydd i det kundhanterade nyckelvalvet misslyckas, läggs transaktioner till i aktivitetsloggen.  Genom att skapa aviseringar för dessa händelser kan du återställa åtkomsten så snart som möjligt.
- [Åtgärdsgrupper](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) kan definieras för att skicka meddelanden och aviseringar till dig baserat på dina inställningar, t.ex.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Säkerhetskopiering och återställning av databasen med kundhanterad TDE

När en databas är krypterad med TDE med hjälp av en nyckel från Key Vault krypteras även alla nyligen genererade säkerhetskopior med samma TDE-skydd. När TDE-skydd ändras **uppdateras inte** gamla säkerhetskopior av databasen för att använda det senaste TDE-skyddet.

Om du vill återställa en säkerhetskopia krypterad med ett TDE-skydd från Key Vault kontrollerar du att nyckelmaterialet är tillgängligt för målservern. Därför rekommenderar vi att du behåller alla gamla versioner av TDE-skydd i nyckelvalvet, så att säkerhetskopiering av databaser kan återställas.

> [!IMPORTANT]
> När som helst kan det inte finnas mer än ett TDE-skydd som ställts in för en server. Det är nyckeln som är markerad med "Gör nyckeln till standard-TDE-skydd" i Azure-portalbladet. Flera ytterligare nycklar kan dock länkas till en server utan att markera dem som ett TDE-skydd. Dessa nycklar används inte för att skydda DEK, men kan användas under återställning från en säkerhetskopia, om säkerhetskopian är krypterad med nyckeln med motsvarande tumavtryck.

Om nyckeln som behövs för att återställa en säkerhetskopia inte längre är tillgänglig för målservern returneras `<Servername>` följande felmeddelande vid återställningsförsöket: \<"Målservern har inte åtkomst \<till alla AKV-URI:er som skapats mellan tidsstämpel #1> och tidsstämpel #2>. Försök igen när du har återställt alla AKV-URI:er."

För att minska det, kör [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet för målet SQL Database logisk server eller [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) för målhanterad instans för att returnera listan över tillgängliga nycklar och identifiera de saknade. Se till att alla säkerhetskopior kan återställas genom att se till att målservern för återställningen har åtkomst till alla nycklar som behövs. Dessa nycklar behöver inte markeras som TDE-skydd.

Mer information om återställning av säkerhetskopiering för SQL Database finns i [Återställa en Azure SQL-databas](sql-database-recovery-using-backups.md). Mer information om återställning av säkerhetskopiering för SQL Pool finns i [Återställa en SQL Pool](../synapse-analytics/sql-data-warehouse/backup-and-restore.md). För SQL Server inbyggda säkerhetskopiering/återställning med hanterad instans finns i [Snabbstart: Återställa en databas till en hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore)

Ytterligare övervägande för loggfiler: Säkerhetskopierade loggfiler förblir krypterade med det ursprungliga TDE-skyddet, även om det roterades och databasen nu använder ett nytt TDE-skydd.  Vid återställningstillfället kommer båda nycklarna att behövas för att återställa databasen.  Om loggfilen använder ett TDE-skydd som lagras i Azure Key Vault, kommer den här nyckeln att behövas vid återställningstillfället, även om databasen har ändrats för att använda tjänsthanterad TDE under tiden.

## <a name="high-availability-with-customer-managed-tde"></a>Hög tillgänglighet med kundhanterad TDE

Även i de fall då det inte finns någon konfigurerad geo-redundans för servern, rekommenderas det starkt att servern ska använda två olika nyckelvalv i två olika regioner med samma nyckelmaterial. Det kan åstadkommas genom att skapa ett TDE-skydd med hjälp av primärnyckelvalvet som finns i samma region som servern och klona nyckeln till ett nyckelvalv i en annan Azure-region, så att servern har tillgång till ett andra nyckelvalv bör primärnyckelvalvet uppleva ett avbrott medan databasen är igång.

Använd cmdleten Backup-AzKeyVaultKey för att hämta nyckeln i krypterat format från primärnyckelvalvet och använd sedan cmdleten Restore-AzKeyVaultKey och ange ett nyckelvalv i den andra regionen för att klona nyckeln. Du kan också använda Azure-portalen för att säkerhetskopiera och återställa nyckeln. Nyckeln i det sekundära nyckelvalvet i den andra regionen bör inte markeras som TDE-skydd och det är inte ens tillåtet.

 Om det finns ett avbrott som påverkar primärnyckelvalvet, och först då, växlar systemet automatiskt till den andra länkade nyckeln med samma tumavtryck i det sekundära nyckelvalvet, om det finns. Observera dock att växeln inte kommer att ske om TDE-skydd är otillgänglig på grund av återkallade åtkomsträttigheter, eller på grund av att nyckel- eller nyckelvalv tas bort, eftersom det kan tyda på att kunden avsiktligt ville begränsa servern från att komma åt nyckeln.

![Ha för en server](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geo-DR och kundhanterad TDE

I både [aktiva geo-replikerings-](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) och [redundansgrupper](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) kräver varje berörd server ett separat nyckelvalv som måste samlokalas med servern i samma Azure-region. Kunden är ansvarig för att hålla nyckelmaterialet i nyckelvalven konsekventa, så att geo-sekundärt är synkroniserat och kan ta över med samma nyckel från sitt lokala nyckelvalv om primärt blir otillgängligt på grund av ett avbrott i regionen och en redundans utlöses . Upp till fyra sekundärer kan konfigureras och kedja (sekundärer av sekundärer) stöds inte.

För att undvika problem när du upprättar eller under geo-replikering på grund av ofullständigt nyckelmaterial är det viktigt att följa dessa regler när du konfigurerar kundhanterad TDE:

- Alla inblandade nyckelvalv måste ha samma egenskaper och samma åtkomsträttigheter för respektive servrar.

- Alla inblandade nyckelvalv måste innehålla identiskt nyckelmaterial. Det gäller inte bara för det aktuella TDE-skyddet, utan för alla tidigare TDE-skydd som kan användas i säkerhetskopieringsfilerna.

- Både den första inställningen och rotationen av TDE-skydd måste göras på den sekundära först och sedan på primär.

![Redundansgrupper och geo-dr](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

Om du vill testa en redundans följer du stegen i [Översikt över aktiv georeplikering](sql-database-geo-replication-overview.md). Det bör göras regelbundet för att bekräfta åtkomstbehörigheter för SQL till båda viktiga valv har underhållits.

## <a name="next-steps"></a>Nästa steg

Du kanske också vill kontrollera följande PowerShell-exempelskript för vanliga åtgärder med kundhanterad TDE:

- [Rotera transparent datakrypteringsskydd för SQL-databas med PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [Ta bort ett TDE-skydd (Transparent Data Encryption) för SQL Database med PowerShell](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [Hantera transparent datakryptering i en hanterad instans med din egen nyckel med PowerShell](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)

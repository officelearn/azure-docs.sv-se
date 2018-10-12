---
title: TDE - Bring Your Own Key (BYOK) – Azure SQL Database | Microsoft Docs
description: Stöd för Bring Your Own Key (BYOK) för Transparent datakryptering (TDE) med Azure Key Vault för SQL-databasen och informationslagret. Transparent Datakryptering med BYOK översikt, fördelar, hur det fungerar, överväganden och rekommendationer.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: aff1d59000a95f2b8f029b9db30ff1facb2f8ba6
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114676"
---
# <a name="azure-sql-transparent-data-encryption-bring-your-own-key-support"></a>Azure SQL Transparent datakryptering: Bring Your Own Key-stöd

Stöd för Bring Your Own Key (BYOK) för [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) kan du kryptera den databasen Datakrypteringsnyckeln (DEK) med en asymmetrisk nyckel som heter TDE-skydd.  TDE-skyddet lagras under din kontroll i [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), Azures molnbaserade externa nyckelhanteringssystem. Azure Key Vault är den första nyckelhanteringstjänst som TDE har inbyggt stöd för BYOK. TDE DEK, som är lagrad på startsidan av en databas krypteras och dekrypteras av TDE-skyddet. TDE-skyddet lagras i Azure Key Vault och lämnar aldrig nyckelvalvet. Serverns åtkomst till nyckelvalvet har återkallats, en databas dekrypteras och läsa in i minnet.  TDE-skyddet är inställd på logiska servernivå och ärvs av alla databaser som är associerade med den här servern.

Användare kan nu styra viktiga hanteringsaktiviteter, inklusive nyckelrotationer med BYOK-stöd, nyckelvalvet behörigheter, ta bort nycklar och aktivera granskning/rapportering på alla TDE-skydd med hjälp av Azure Key Vault-funktioner. Key Vault ger central nyckelhantering, utnyttjar nära övervakade maskinvarusäkerhetsmoduler (HSM) och låter uppdelning av uppgifter mellan hantering av nycklar och data för att uppfylla regelefterlevnad.  

Transparent Datakryptering med BYOK ger följande fördelar:

- Ökad insyn och detaljerad kontroll med möjlighet att själv hantera TDE-skydd
- Central hantering av TDE-skydd (tillsammans med andra nycklar och hemligheter som används i andra Azure-tjänster) genom att lägga upp dem i Key Vault
- Avgränsning av ansvarsområden för nyckeln och data management i organisationen, för att stödja uppdelning av uppgifter
- Större förtroende från dina egna klienter, eftersom Key Vault har utformats så att Microsoft varken se eller extrahera krypteringsnycklar.
- Stöd för nyckelrotation

> [!IMPORTANT]
> För de som använder tjänsthanterad TDE som vill börja använda Key Vault, förblir TDE aktiv under processen med att växla till ett TDE-skydd i Key Vault. Det finns ingen nedtid eller omkryptering av databasfilerna. Byta från en tjänsthanterad nyckel till en Key Vault-nyckeln kräver endast omkryptering av databaskrypteringsnyckeln (DEK), vilket är en snabb och online igen.

## <a name="how-does-tde-with-byok-support-work"></a>Hur stöder transparent Datakryptering med BYOK arbete

![Autentisering av servern till Nyckelvalvet](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

När TDE först konfigureras för att använda ett TDE-skydd från Key Vault, skickar servern DEK av varje TDE-aktiverad databas till Key Vault för en förfrågan om radbyte. Key Vault returnerar krypterat databaskrypteringsnyckeln, som är lagrad i databasen.  

> [!IMPORTANT]
> Det är viktigt att notera att **när ett TDE-skydd finns i Azure Key Vault, den aldrig lämnar Azure Key Vault**. Den logiska servern kan bara skicka nyckelåtgärd begäranden till TDE-skydd nyckelmaterial i Key Vault och **aldrig ansluter till eller cachelagrar TDE-skyddet**. Key Vault-administratören har behörighet att återkalla behörigheter för Key Vault för servern när som helst, i så fall alla anslutningar till servern klipps bort.

## <a name="guidelines-for-configuring-tde-with-byok"></a>Riktlinjer för att konfigurera transparent Datakryptering med BYOK

### <a name="general-guidelines"></a>Allmänna riktlinjer

- Se till att Azure Key Vault och Azure SQL Database kommer att vara i samma klientorganisation.  Flera klienter key vault och server interaktioner **stöds inte**.
- Bestäm vilka prenumerationer kommer att användas för resurserna som krävs – flytta servern över prenumerationer senare måste en ny installation av TDE med BYOKs. Läs mer om [flyttar resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- När du konfigurerar transparent Datakryptering med BYOK, är det viktigt att ha i åtanke placeras i nyckelvalvet av upprepade radbyte/packa upp åtgärder. Exempelvis kan utlösa en redundansväxling av den här servern eftersom många viktiga åtgärder mot valvet eftersom det är databaser på servern eftersom alla databaser som är associerade med en logisk server använder samma TDE-skyddet. Baserat på vår erfarenhet och dokumenteras [nyckeln vault-tjänstbegränsningar](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits), rekommenderar vi kopplar högst 500 Standard / generell användning eller 200 Premium / affärskritiska databaser med en Azure Key Vault för en enskild prenumeration så konsekvent hög tillgänglighet vid åtkomst till TDE-skydd i valvet.
- Rekommenderat: Behåll en kopia av TDE-skydd lokalt.  Detta kräver en HSM-enheten för att skapa ett TDE-skydd lokalt och en nyckel escrow-systemet för att lagra en lokal kopia av TDE-skydd.  Lär dig [hur du överför en nyckel från en lokal HSM till Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).

### <a name="guidelines-for-configuring-azure-key-vault"></a>Riktlinjer för att konfigurera Azure Key Vault

- Skapa ett nyckelvalv med [mjuk borttagning](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) aktiverad för att skydda mot data går förlorade vid oavsiktlig nyckel- eller key vault-borttagning.  Du måste använda [PowerShell för att aktivera egenskapen ”mjuk borttagning”](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) i nyckelvalvet (det här alternativet inte är tillgängliga från AKV-Portal ännu – men krävs av SQL):  
  - Ej permanent borttagna resurser finns kvar för en angiven tidsperiod, 90 dagar, såvida inte de återställs eller rensas.
  - Den **återställa** och **Rensa** åtgärder har sina egna behörigheter som är associerade i en åtkomstprincip för nyckelvalvet.
- Ange ett lås för nyckelvalvet för att kontrollera vem som kan ta bort den här kritisk resurs och bidra till att förhindra oavsiktliga eller obehöriga borttagning.  [Mer information om resurslås](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- Bevilja logisk server-åtkomst till nyckelvalvet med sin identitet med Azure Active Directory (AD Azure).  När du använder Användargränssnittet i Portal, Azure AD-identitet skapas automatiskt och åtkomstbehörigheter för nyckelvalv har beviljats till servern.  Använda PowerShell för att konfigurera transparent Datakryptering med BYOK, Azure AD-identitet måste skapas och slutförande bör verifieras. Se [konfigurera transparent Datakryptering med BYOK](transparent-data-encryption-byok-azure-sql-configure.md) stegvisa anvisningar när du använder PowerShell.

  > [!NOTE]
  > Om Azure AD Identity **av misstag har tagits bort eller återkallas serverns behörigheter** med hjälp av den nyckelvalvets åtkomstprincip, servern förlorat åtkomst till nyckelvalvet och transparent Datakryptering krypteras databaser tas bort inom 24 timmar.

- När du använder brandväggar och virtuella nätverk med Azure Key Vault, måste du konfigurera följande: 
  - Tillåta åtkomst från ”valda nätverk” 
  - Lägg till befintliga virtuella nätverk och välj SQL Database-nätverket om det är tillämpligt (detta är valfritt för singleton-databaser och krävs för hanterade instanser) 
  - Tillåt att betrodda Microsoft-tjänster kringgår den här brandväggen – väljer Ja 
         
    > [!NOTE] 
    > Om TDE krypterad SQL-databaser förlorar åtkomst till nyckelvalvet eftersom de inte kan kringgå brandväggen, databaserna hamnar inom 24 timmar.

- Aktivera granskning och rapportera alla krypteringsnycklar: Key Vault ger loggar som är lätt att mata in i andra säkerhet och händelsehantering (SIEM) hanteringsverktyg. Operations Management Suite (OMS) [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) är ett exempel på en tjänst som redan har integrerat.
- För att säkerställa hög tillgänglighet med krypterade databaser, att konfigurera varje logisk server med två Azure-Nyckelvalv som finns i olika regioner.

### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key"></a>Riktlinjer för att konfigurera TDE-skydd (asymmetrisk nyckel)

- Skapa krypteringsnyckeln lokalt på en lokal HSM-enhet. Kontrollera att detta är en asymmetrisk, RSA 2048-nyckel så att den är lagringsbart i Azure Key Vault.
- Deponera nyckeln i ett system för viktiga escrow.  
- Importera filen med krypteringsnyckeln (.pfx, .byok eller .backup) till Azure Key Vault.

   > [!NOTE]
   > I testsyfte är det möjligt att skapa en nyckel med Azure Key Vault, men den här nyckeln inte kan vara escrowed, eftersom den privata nyckeln kan aldrig lämnar nyckelvalvet.  Säkerhetskopiera alltid och deponera nycklar som används för att kryptera produktionsdata, som förlust av nyckeln (oavsiktlig borttagning i nyckeln valvet, förfallodatum osv) resulterar i permanenta data går förlorade.

- Använda en nyckel utan ett förfallodatum – och aldrig har ett utgångsdatum på en nyckel redan: **när nyckeln upphör att gälla krypterade databaser att förlora åtkomst till sina TDE-skydd och tas bort inom 24 timmar**.
- Kontrollera att nyckeln är aktiverad och har behörighet att utföra *hämta*, *omsluta nyckel*, och *nyckelomslutning* åtgärder.
- Skapa en säkerhetskopiering av Azure Key Vault nycklar innan du använder nyckeln i Azure Key Vault för första gången. Läs mer om den [Backup-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1) kommando.
- Skapa en ny säkerhetskopia varje gång några ändringar görs till nyckeln (till exempel lägga till ACL: er, lägga till taggar, lägga till viktiga attribut).
- **Behåll tidigare versioner** för nyckeln i nyckelvalvet när du roterar nycklar, så äldre säkerhetskopior av databasen kan återställas. När TDE-skyddet ändras för en databas, gamla säkerhetskopior av databasen **uppdateras inte** att använda den senaste TDE-skydd.  Varje säkerhetskopiering måste TDE-skydd som den skapades vid en återställning. Nyckelrotationer kan utföras följa anvisningarna i [rotera Transparent Data Encryption skydd med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Behåll alla tidigare nycklar i Azure Key Vault när du har ändrat till tjänst-hanterade nycklar.  Detta säkerställer att säkerhetskopior av databasen kan återställas med TDE-skydd som lagras i Azure Key Vault.  TDE-skydd som skapats med Azure Key Vault måste finnas kvar tills alla lagrade säkerhetskopior har skapats med tjänst-hanterade nycklar.  
- Säkerhetskopiera återställningsbara nycklarna på [Backup-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1).
- Om du vill ta bort en potentiellt komprometterade nyckel under en säkerhetsincident utan risk för dataförlust, följer du stegen i [tar bort en potentiellt komprometterade nyckel](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

## <a name="high-availability-geo-replication-and-backup--restore"></a>Hög tillgänglighet, Geo-replikering och säkerhetskopiering / återställning

### <a name="high-availability-and-disaster-recovery"></a>Hög tillgänglighet och haveriberedskap

Hur du konfigurerar hög tillgänglighet med Azure Key Vault beror på konfigurationen av din databas och en logisk server och följer de rekommenderade konfigurationerna för två olika metoder.  Det första fallet är en fristående databas eller en logisk server med inga konfigurerade geo-redundans.  Det andra fallet är en databas eller en logisk server som konfigurerats med redundansgrupper eller geo-redundans, där det säkerställas att varje geo-redundant kopia har en lokal Azure Key Vault i redundansgruppen så att arbetsrelaterad geo-redundans.

I det första fallet om du behöver hög tillgänglighet för en databas och en logisk server med inga konfigurerade geo-redundans, rekommenderar vi starkt att konfigurera servern för att använda två olika nyckelvalv i två olika regioner med samma nyckelmaterial. Detta kan åstadkommas genom att skapa en TDE-skydd med den primära Key Vault är placerade i samma region som den logiska servern och kloning av nyckeln till ett nyckelvalv i en annan Azure-region, så att servern har åtkomst till andra key vault bör den primära nyckeln v andard uppleva ett avbrott när databasen är igång. Använd Backup-AzureKeyVaultKey-cmdlet för att hämta nyckeln i krypterat format i primära key Vault och Använd cmdleten Restore-AzureKeyVaultKey och ange ett nyckelvalv i den andra regionen.

![Enskild Server hög tillgänglighet och ingen geohaveriberedskap](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>Konfigurera Geo-DR med Azure Key Vault

Om du vill upprätthålla hög tillgänglighet för TDE-skydd för krypterade databaser du behöver konfigurera redundant Azure-Nyckelvalv baserat på befintliga eller önskade SQL Database-redundansgrupper eller aktiv geo-replikering instanser.  Varje geo-replikerade server kräver ett separat nyckelvalv, som måste samordnas med server i samma Azure-region. En primär databas blir otillgängliga på grund av ett avbrott i en region och redundans utlöses den sekundära databasen kan ta över med sekundära key vault.

För Geo-Replicated Azure SQL-databaser krävs följande Azure Key Vault-konfiguration:

- En primär databas med ett nyckelvalv i region och en sekundär databas med ett key vault i regionen.
- Minst en sekundär krävs, upp till fyra sekundära databaser stöds.
- Sekundärservrar för sekundära databaser (länkning) stöds inte.

I följande avsnitt får du gå igenom stegen för installation och konfiguration i detalj.

### <a name="azure-key-vault-configuration-steps"></a>Konfigurationsstegen för Azure Key Vault

- Installera [PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.6.0)
- Skapa två Azure-Nyckelvalv i två olika regioner med hjälp av [PowerShell för att aktivera egenskapen ”mjuk borttagning”](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) på nyckelvalven (det här alternativet inte är tillgängliga från AKV-Portal ännu – men krävs av SQL).
- Både Azure-Nyckelvalv måste befinna sig i två regioner som är tillgängliga på samma Azure-geoplats för säkerhetskopiering och återställning av nycklar för att fungera.  Om du behöver två nyckelvalven finnas i olika geografiska områden till uppfyller kraven för SQL-Geohaveriberedskap, följer du de [BYOK processen](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) som gör det möjligt för nycklar som ska importeras från en lokal HSM.
- Skapa en ny nyckel i det första nyckelvalvet:  
  - RSA/RSA-HSA 2048 nyckel
  - Inget förfallodatum
  - Nyckeln är aktiverad och har behörighet att utföra get, wrap key och packa upp nyckelåtgärder
- Säkerhetskopiera den primära nyckeln och återställa nyckeln till andra nyckelvalvet.  Se [BackupAzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1) och [Restore-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-5.5.0).

### <a name="azure-sql-database-configuration-steps"></a>Konfigurationsstegen för Azure SQL Database

Följande konfigurationssteg skiljer sig om börjar med en ny SQL-distribution eller om du arbetar med en redan befintlig SQL Geo-DR-distribution.  Vi beskriver konfigurationsstegen för en ny distribution först och förklarar hur du tilldelar TDE-skydd som lagras i Azure Key Vault till en befintlig distribution som redan har en Geo-DR-länk upprättas.

**Steg för att en ny distribution**:

- Skapa två logiska SQL-servrar i samma två regioner som de tidigare skapade nyckelvalv.
- Välj den logiska server TDE-rutan, och för varje logisk SQL-server:  
  - Välj AKV i samma region
  - Välj nyckeln som ska användas som TDE-skydd – varje server ska använda den lokala kopian av TDE-skydd.
  - Detta i portalen skapas en [AppID](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) för den logiska SQL-servern, som används för att tilldela de logiska SQL Server-behörigheterna för åtkomst till key vault – ta inte bort den här identiteten. Åtkomsten kan återkallas genom att ta bort behörigheter i Azure Key Vault i stället för den logiska SQL-servern, som används för att tilldela de logiska SQL Server-behörigheterna för att få åtkomst till nyckelvalvet.
- Skapa den primära databasen.
- Följ den [aktiv geo-replikering vägledning](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) för att slutföra scenariot, det här steget skapar den sekundära databasen.

![Redundansgrupper och -geohaveriberedskap](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

> [!NOTE]
> Det är viktigt att se till att samma TDE-skydd finns i båda nyckelvalv, innan du fortsätter att upprätta geo-länk mellan databaser.

**Steg för en befintlig SQL-databas med Geo-DR-distribution**:

Eftersom det finns redan en logisk SQL-servrar och primära och sekundära databaser har redan tilldelats, måste hur du konfigurerar Azure Key Vault utföras i följande ordning:

- Börja med den logiska SQL-Server som är värd för den sekundära databasen:
  - Tilldela key vault finns i samma region
  - Tilldela TDE-skydd
- Gå nu till den logiska SQL-Server som är värd för den primära databasen:
  - Välj samma TDE-skydd som används för den sekundära databasen

![Redundansgrupper och -geohaveriberedskap](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

>[!NOTE]
>När du tilldelar servern nyckelvalvet, är det viktigt att utgå från den sekundära servern.  I det andra steget tilldela nyckeln valvet till den primära servern och uppdatera TDE-skydd, fortsätter Geo-DR-länk att fungera eftersom TDE-skydd som används av den replikerade databasen är nu tillgänglig för båda servrarna.

Innan du aktiverar transparent Datakryptering med kunden hanterade nycklar i Azure Key Vault för en SQL Database Geo-DR-scenario, är det viktigt att skapa och underhålla två Azure-Nyckelvalv med identiska innehållet i samma regioner som ska användas för SQL Database geo-replikering.  ”Identiskt innehåll” innebär mer specifikt som båda nyckelvalv måste innehålla kopior av samma TDE Protector(s) så att båda servrarna har åtkomst till TDE-skydd användning av alla databaser.  Framöver kommer det krävs att båda nyckelvalv synkroniserade, vilket innebär att de måste innehålla samma kopior av TDE-skydd efter rotation av, underhålla äldre versioner av nycklar som används för loggfiler eller säkerhetskopior, TDE-skydd måste ha samma nyckelegenskaper och nyckeln valv måste ha samma behörigheter för SQL.  

Följ stegen i [översikt över aktiv geo-replikering](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) för att testa och utlösa redundans, som ska göras med jämna mellanrum att bekräfta åtkomstbehörigheter för SQL till båda nyckelvalv upprätthållits.

### <a name="backup-and-restore"></a>Säkerhetskopiera och återställ

När en databas är krypterad med transparent Datakryptering med hjälp av en nyckel från Key Vault, krypteras också eventuella genererade säkerhetskopior med samma TDE-skydd.

Om du vill återställa en säkerhetskopia som krypterats med ett TDE-skydd från Key Vault, se till att nyckelmaterial är fortfarande i ursprungliga valvet som hör till den ursprungliga nyckelnamnen. När TDE-skyddet ändras för en databas, gamla säkerhetskopior av databasen **inte** uppdaterats så att den senaste TDE-skydd. Därför rekommenderar vi att du behåller alla äldre versioner av TDE-skydd i Key Vault, så att säkerhetskopior av databasen kan återställas.

Om en nyckel som kan behövas för att återställa en säkerhetskopia är inte längre i dess ursprungliga nyckelvalv, returneras följande felmeddelande ”: målservern `<Servername>` har inte åtkomst till alla AKV-URI: er som skapats mellan < tidsstämpel #1 > och < tidsstämpel #2 >. Försök igen när du återställer alla AKV-URI: er ”.

Åtgärda detta genom att köra den [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) cmdleten returnerade listan med nycklar från Key Vault som har lagts till servern (om de har tagits bort av en användare). För att säkerställa att alla säkerhetskopieringar kan återställas, kontrollera att målservern för säkerhetskopiering har åtkomst till alla de här nycklarna.

```powershell
Get-AzureRmSqlServerKeyVaultKey `
  -ServerName <LogicalServerName> `
  -ResourceGroup <SQLDatabaseResourceGroupName>
```

Läs mer om säkerhetskopiering och återställning för SQL-databas i [återställa en Azure SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups). Läs mer om säkerhetskopiering och återställning för SQL Data Warehouse i [återställa en Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-overview).

Ytterligare överväganden för säkerhetskopierade loggfiler: säkerhetskopieras log filer förblir krypterade med den ursprungliga TDE-Krypterare, även om TDE-skyddet har roterats och databasen är med hjälp av en ny TDE-skydd.  Vid en återställning krävs båda nycklarna för att återställa databasen.  Om loggfilen använder ett TDE-skydd som lagras i Azure Key Vault, krävs den här nyckeln vid en återställning, även om databasen har ändrats för att använda tjänsthanterad TDE under tiden.

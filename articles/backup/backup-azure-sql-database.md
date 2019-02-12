---
title: Säkerhetskopiera SQL Server-databaser till Azure | Microsoft Docs
description: I den här självstudien beskrivs hur du säkerhetskopierar SQL Server till Azure. Den här artikeln beskriver även återställning av SQL Server.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: raynew
ms.openlocfilehash: fa154b79625fffb8174c510156b3a67df8bff785
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55770443"
---
# <a name="back-up-sql-server-databases-to-azure"></a>Säkerhetskopiera SQL Server-databaser till Azure

SQL Server-databaser är kritiska arbetsbelastningar som kräver ett lågt mål för återställningspunkt (RPO) och långsiktig kvarhållning. Azure Backup är en lösning för säkerhetskopiering av SQL Server som inte kräver någon infrastruktur för att hanteras, varken komplex säkerhetskopieringsserver, hanteringsagent eller lagring av säkerhetskopior. Azure Backup tillhandahåller centraliserad hantering av dina säkerhetskopior på alla servrar som kör SQL Server eller till och med olika arbetsbelastningar.

I den här artikeln lär du dig:

> [!div class="checklist"]
> * Förutsättningarna för säkerhetskopiering av en SQL Server-instans till Azure.
> * Hur du skapar och använder ett Recovery Services-valv.
> * Hur du konfigurerar säkerhetskopior av SQL Server-databas.
> * Hur du anger en policy för säkerhetskopiering (eller kvarhållning) för återställningspunkterna.
> * Hur du återställer databasen.

Innan du påbörjar procedurerna i den här artikeln bör du ha en SQL Server-instans som körs i Azure. [Använda virtuella SQL Marketplace-datorer för att snabbt skapa en SQL Server-instans](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Begränsningar för offentlig förhandsversion

Följande är kända begränsningar för den offentliga förhandsversionen:

- Den virtuella SQL-datorn (VM) kräver en Internetanslutning för åtkomst till offentliga Azure-IP-adresser. Mer information finns i avsnittet om att [etablera nätverksanslutning](backup-azure-sql-database.md#establish-network-connectivity).
- Skydda upp till 2 000 SQL-databaser i ett Recovery Services-valv. Ytterligare SQL-databaser bör lagras i ett separat Recovery Services-valv.
- [Säkerhetskopior av distribuerade tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) har begränsningar.
- AlwaysOn-redundansklusterinstanser för SQL Server (FCI:er) stöds inte för säkerhetskopiering.
- Använd Azure-portalen till att konfigurera Azure Backup för att skydda SQL Server-databaser. Azure PowerShell, Azure CLI och REST-API:er stöds inte för närvarande.
- Åtgärder för säkerhetskopiering/återställning för speglade FCI-databaser, ögonblicksbilder av databaser samt databaser stöds inte.
- Databaser med ett stort antal filer kan inte skyddas. Det maximala antalet filer som stöds är inte en särskilt förutsägbar siffra, eftersom det inte bara beror på antalet filer utan även sökvägslängden för filerna. Sådana fall är dock mindre vanliga. Vi arbetar med att skapa en lösning för att hantera detta.

Mer information om support/scenarier som inte stöds finns i [avsnittet med vanliga frågor och svar](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#faq).

## <a name="support-for-azure-geos"></a>Stöd för Azure-regioner

Azure Backup stöds för följande regioner:

- Australien, sydöstra (ASE)
- Brasilien, södra (BRS)
- Kanada, centrala (CNC)
- Kanada, östra (CE)
- USA, centrala (CUS)
- Asien, östra (EA)
- Australien, östra (AE)
- USA, östra (EUS)
- USA, östra 2 (EUS2)
- Indien, centrala (INC)
- Indien, södra (INS)
- Japan, östra (JPE)
- Japan, västra (JPW)
- Sydkorea, centrala (KRC)
- Sydkorea, södra (KRS)
- USA, norra centrala (NCUS)
- Europa, norra (NE)
- USA, södra centrala (SCUS)
- Asien, sydöstra (SEA)
- Storbritannien, södra (UKS)
- Storbritannien, västra (UKW)
- USA, västra centrala (WCUS)
- Europa, västra (WE)
- USA, västra (WUS)
- USA, västra 2 (WUS 2)

## <a name="support-for-operating-systems-and-sql-server-versions"></a>Stöd för operativsystem och SQL Server-versioner

I det här avsnittet beskrivs Azure Backup-stöd för operativsystem och versioner av SQL Server. Virtuella Azure-datorer för SQL Marketplace och virtuella icke-Marketplace-datorer (där SQL Server installeras manuellt) stöds.

### <a name="supported-operating-systems"></a>Operativsystem som stöds

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Linux stöds inte för närvarande.

### <a name="supported-sql-server-versions-and-editions"></a>SQL Server-versioner och utgåvor som stöds

- SQL Server 2012 Enterprise, Standard, Web, Developer, Express
- SQL Server 2014 Enterprise, Standard, Web, Developer, Express
- SQL Server 2016 Enterprise, Standard, Web, Developer, Express
- SQL Server 2017 Enterprise, Standard, Web, Developer, Express

## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera följande villkor innan du säkerhetskopierar SQL Server-databasen:

- Identifiera eller [skapa ett Recovery Services-valv](backup-azure-sql-database.md#create-a-recovery-services-vault) i samma region eller språkinställning som den virtuella dator som är värd för din SQL Server-instans.
- [Kontrollera behörigheterna på den virtuella datorn](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) som behövs för att säkerhetskopiera SQL-databaser.
- Kontrollera att [den virtuella SQL-datorn är ansluten till nätverket](backup-azure-sql-database.md#establish-network-connectivity).
- Kontrollera om SQL-databaserna har namn som följer [riktlinjerna för namngivning](backup-azure-sql-database.md#sql-database-naming-guidelines-for-azure-backup) så att Azure Backup kan skapa säkerhetskopior på rätt sätt.

> [!NOTE]
> Du kan endast ha en säkerhetskopieringslösning åt gången för att säkerhetskopiera SQL Server-databaser. Inaktivera alla andra SQL-säkerhetskopior innan du använder den här funktionen; annars störs den av säkerhetskopiorna och misslyckas. Du kan aktivera Azure Backup för IaaS VM tillsammans med SQL-säkerhetskopiering utan någon konflikt.
>

Om de här villkoren föreligger i din miljö fortsätter du till [Konfigurera säkerhetskopiering för SQL Server-databaser](backup-azure-sql-database.md#configure-backup-for-sql-server-databases). Om någon av förutsättningarna saknas läser du vidare.


## <a name="establish-network-connectivity"></a>Etablera nätverksanslutning

Den virtuella SQL-datorn behöver ha anslutning till offentliga Azure-IP-adresser för alla åtgärder. SQL VM-åtgärder (till exempel databasidentifiering, konfiguration av säkerhetskopieringar, schemaläggning av säkerhetskopieringar, återställning av återställningspunkter och så vidare) misslyckas utan anslutning till de offentliga IP-adresserna. Använd något av följande alternativ för att tillhandahålla en tydlig väg för säkerhetskopieringstrafik:

- Tillåt IP-intervallen för Azure-datacenter: För att tillåtna IP-intervallen för Azure-datacenter använder du [Download Center-sidan med information om IP-intervall och instruktioner](https://www.microsoft.com/download/details.aspx?id=41653).
- Distribuera en HTTP-proxyserver för att dirigera trafik: När du säkerhetskopierar en SQL-databas på en virtuell dator använder säkerhetskopieringstillägget på den virtuella datorn HTTPS-API:er för att skicka hanteringskommandon till Azure Backup och data till Azure Storage. Säkerhetskopieringstillägget använder också Azure Active Directory (Azure AD) för autentisering. Dirigera trafiken för säkerhetskopieringstillägget för dessa tre tjänster via HTTP-proxyn. Tillägget är den enda komponenten som är konfigurerad för åtkomst till det offentliga Internet.

Kompromisserna mellan alternativen rör hanterbarhet, detaljnivån för kontroll samt kostnad.

> [!NOTE]
> Tjänsttaggar för Azure Backup bör vara tillgängliga med allmän tillgänglighet.
>

| Alternativ | Fördelar | Nackdelar |
| ------ | ---------- | ------------- |
| Tillåt IP-intervall | Inga ytterligare kostnader. <br/> För åtkomst till att öppna i en NSG använder du cmdleten **Set-AzureNetworkSecurityRule**. | Komplicerat att hantera eftersom de berörda IP-intervallen ändras över tid. <br/>Ger åtkomst till hela Azure, inte bara Azure Storage.|
| Använda en HTTP-proxy   | Detaljerad kontroll i proxyn över lagrings-URL:er tillåts. <br/>Enskild punkt för Internetåtkomst till virtuella datorer. <br/> Inte föremål för Azure-IP-adressändringar. | Ytterligare kostnader för att köra en virtuell dator med proxyprogramvaran. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Ange behörigheter för virtuella SQL-datorer som inte kommer från Marketplace

För att kunna säkerhetskopiera en virtuell dator kräver Azure Backup att tillägget **AzureBackupWindowsWorkload** installeras. Om du använder virtuella Azure Marketplace-datorer kan du fortsätta att [identifiera SQL Server-databaser](backup-azure-sql-database.md#discover-sql-server-databases). Om den virtuella dator som är värd för dina SQL-databaser inte skapas från Azure Marketplace slutför du följande procedur för att installera tillägget och ange lämpliga behörigheter. Utöver tillägget **AzureBackupWindowsWorkload** kräver Azure Backup SQL-sysadmin-behörigheter för att skydda SQL-databaser. För att identifiera databaser på den virtuella datorn skapar Azure Backup kontot **NT SERVICE\AzureWLBackupPluginSvc**. Det här kontot används för säkerhetskopiering och återställning och behöver ha SQL-sysadmin-behörighet. Dessutom utnyttjar Azure Backup kontot **NT AUTHORITY\SYSTEM** för DB-identifiering/-förfrågan. Därför behöver det här kontot vara en offentlig inloggning på SQL.

Så här konfigurerar du behörigheter:

1. I [Azure-portalen](https://portal.azure.com) öppnar du det Recovery Services-valv som du använder för att skydda SQL-databaserna.

2. På instrumentpanelen för **Recovery Services-valvet** väljer du **Säkerhetskopiering**. Menyn **Säkerhetskopieringsmål** öppnas.

   ![Välj Säkerhetskopiering för att öppna menyn Säkerhetskopieringsmål](./media/backup-azure-sql-database/open-backup-menu.png)

3. På menyn **Säkerhetskopieringsmål** anger du **Var körs din arbetsbelastning** till standardinställningen: **Azure**.

4. Expandera listrutan **Vad vill du säkerhetskopiera** och välj **SQL Server på Azure VM**.

    ![Välj SQL Server på Azure VM för säkerhetskopieringen](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Menyn **Säkerhetskopieringsmål** visar två steg: **Identifiera databaser på virtuella datorer** och **Konfigurera säkerhetskopiering**. Steget **Identifiera databaser på virtuella datorer** startar en sökning efter virtuella Azure-datorer.

    ![Gå igenom de två stegen för Säkerhetskopieringsmål](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. Under **Identifiera databaser på virtuella datorer** väljer du **Starta identifiering** för att söka efter oskyddade virtuella datorer i prenumerationen. Det kan ta en stund att söka igenom alla de virtuella datorerna. Söktiden beror på antalet oskyddade virtuella datorer i prenumerationen.

    ![Säkerhetskopieringen väntar under sökningen efter databaser på virtuella datorer](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. I listan över virtuella datorer väljer du den virtuella dator som har den SQL-databas som ska säkerhetskopieras och väljer sedan **Identifiera databaser**.

    Identifieringsprocessen installerar tillägget **AzureBackupWindowsWorkload** på den virtuella datorn. Tillägget tillåter Azure Backup-tjänsten att kommunicera med den virtuella datorn så att den kan säkerhetskopiera SQL-databaserna. När tillägget har installerats skapar Azure Backup kontot för virtuell Windows-tjänst, **NT Service\AzureWLBackupPluginSvc**, på den virtuella datorn. Kontot för virtuell tjänst kräver SQL-sysadmin-behörighet. Om du under installationsprocessen för kontot för virtuell tjänst får felet `UserErrorSQLNoSysadminMembership` läser du avsnittet om att [åtgärda SQL-sysadmin-behörigheter](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

    Området **Meddelanden** visar förloppet för databasidentifieringen. Det kan ta en stund innan jobbet är klart. Jobbtiden beror på hur många databaser som finns på den virtuella datorn. När de valda databaserna identifieras visas ett meddelande.

    ![Meddelande som anger att distributionen lyckades](./media/backup-azure-sql-database/notifications-db-discovered.png)

När du har associerat databasen med Recovery Services-valvet är nästa steg att [konfigurera säkerhetskopieringsjobbet](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

### <a name="fix-sql-sysadmin-permissions"></a>Åtgärda SQL-sysadmin-behörigheter

Om du under installationsprocessen får felet `UserErrorSQLNoSysadminMembership` använder du ett konto med SQL Server-sysadmin-behörighet för att logga in till SQL Server Management Studio (SSMS). Såvida du inte behöver specialbehörigheter bör Windows-autentisering fungera.

1. På SQL Server öppnar du mappen Security/Logins (Säkerhet/Inloggningar).

    ![Öppna mappen Security/inloggningar för att se konton](./media/backup-azure-sql-database/security-login-list.png)

2. Högerklicka på mappen Logins och välj **Ny inloggning**. I dialogrutan **Inloggning – ny** väljer du **Sök**.

    ![I dialogrutan Inloggning – ny väljer du Sök](./media/backup-azure-sql-database/new-login-search.png)

3. Kontot för virtuell Windows-tjänst, **NT SERVICE\AzureWLBackupPluginSvc**, skapades under registreringen av den virtuell datorn och SQL-identifieringsfasen. Ange kontonamnet såsom det visas i rutan **Ange ett objektnamn du vill markera**. Välj **Kontrollera namn** för att matcha namnet.

    ![Välj Kontrollera namn för att matcha det okända tjänstnamnet](./media/backup-azure-sql-database/check-name.png)

4. Välj **OK** för att stänga dialogrutan.

5. I rutan **Serverroller** ser du till rollen **sysadmin** har valts. Välj **OK** för att stänga dialogrutan.

    ![Kontrollera att serverrollen sysadmin har valts](./media/backup-azure-sql-database/sysadmin-server-role.png)

    De behörigheter som krävs bör nu finnas.

6. Även om du har åtgärdat behörighetsfelet behöver du associera databasen med Recovery Services-valvet. I Azure-portalen går du till listan **Skyddade servrar**, högerklickar på den server som är i ett felaktigt tillstånd och välj **Identifiera databaser på nytt**.

    ![Kontrollera att servern har rätt behörigheter](./media/backup-azure-sql-database/check-erroneous-server.png)

    Området **Meddelanden** visar förloppet för databasidentifieringen. Det kan ta en stund innan jobbet är klart. Jobbtiden beror på hur många databaser som finns på den virtuella datorn. När de valda databaserna hittas visas ett meddelande.

    ![Meddelande som anger att distributionen lyckades](./media/backup-azure-sql-database/notifications-db-discovered.png)

När du har associerat databasen med Recovery Services-valvet är nästa steg att [konfigurera säkerhetskopieringsjobbet](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

## <a name="sql-database-naming-guidelines-for-azure-backup"></a>Riktlinjer för namngivning av SQL-databaser för Azure Backup
Säkerställ korrekta säkerhetskopieringar med hjälp av Azure Backup för SQL Server i IaaS VM genom att undvik följande när du namnger databaserna:

  * Avslutande/inledande blanksteg
  * Avslutande ”!”
  * Avslutande hakparentes ”]”

Vi har alias för tecken som inte stöds i Azure-tabellen, men vi rekommenderar att du undviker dem också. Mer information finns i [den här artikeln](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Identifiera SQL Server-databaser

Azure Backup identifierar alla databaser på en SQL Server-instans. Du kan skydda databaserna efter dina behov för säkerhetskopiering. Använd följande procedur för att identifiera den virtuella dator som är värd för SQL-databaser. När du har identifierat den virtuella datorn installerar Azure Backup ett enkelt tillägg för att identifiera SQL Server-databaser.

1. Logga in på din prenumeration i [Azure-portalen](https://portal.azure.com/).

2. På menyn till vänster väljer du **Alla tjänster**.

    ![Välj Alla tjänster](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. I dialogrutan **Alla tjänster** anger du **Recovery Services**. När du skriver filtreras listan över resurser av din inmatning. Välj **Recovery Services-valv** i listan.

  ![Ange och välja Recovery Services-valv](./media/backup-azure-sql-database/all-services.png) <br/>

    Listan över Recovery Services-valv i prenumerationen visas.

4. I listan över Recovery Services-valv väljer du det valv som ska användas för att skydda dina SQL-databaser.

5. På instrumentpanelen för **Recovery Services-valvet** väljer du **Säkerhetskopiering**. Menyn **Säkerhetskopieringsmål** öppnas.

   ![Välj Säkerhetskopiering för att öppna menyn Säkerhetskopieringsmål](./media/backup-azure-sql-database/open-backup-menu.png)

6. På menyn **Säkerhetskopieringsmål** anger du **Var körs din arbetsbelastning** till standardinställningen: **Azure**.

7. Expandera listrutan **Vad vill du säkerhetskopiera** och välj **SQL Server på Azure VM**.

    ![Välj SQL Server på Azure VM för säkerhetskopieringen](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Menyn **Säkerhetskopieringsmål** visar två steg: **Identifiera databaser på virtuella datorer** och **Konfigurera säkerhetskopiering**.

    ![Gå igenom de två stegen för Säkerhetskopieringsmål](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. Under **Identifiera databaser på virtuella datorer** väljer du **Starta identifiering** för att söka efter oskyddade virtuella datorer i prenumerationen. Det kan ta en stund att söka igenom alla de virtuella datorerna. Söktiden beror på antalet oskyddade virtuella datorer i prenumerationen.

    ![Säkerhetskopieringen väntar under sökningen efter databaser på virtuella datorer](./media/backup-azure-sql-database/discovering-sql-databases.png)

    När en oskyddad virtuell dator identifieras visas den i listan. Flera virtuella datorer kan ha samma namn. Virtuella datorer med samma namn hör dock till olika resursgrupper. Oskyddade virtuella datorer listas efter VM-namn och resursgrupp. Om en förväntad virtuell dator inte visas kan du kontrollera om den virtuella datorn redan skyddas i ett valv.

9. I listan över virtuella datorer väljer du den virtuella dator som har den SQL-databas som ska säkerhetskopieras och väljer sedan **Identifiera databaser**.

    Azure Backup identifierar alla SQL-databaser på den virtuella datorn. Information om vad som händer under fasen för databasidentifiering finns i [Bakgrundsåtgärder](backup-azure-sql-database.md#background-operations). När SQL-databaserna har identifierats är du redo att [konfigurera säkerhetskopieringsjobbet](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

### <a name="background-operations"></a>Bakgrundsåtgärder

När du använder verktyget **Identifiera databaser** kör Azure Backup följande åtgärder i bakgrunden:

- Registrera den virtuella datorn med Recovery Services-valvet för säkerhetskopiering av arbetsbelastning. Alla databaser på den registrerade virtuella datorn kan endast säkerhetskopieras till det här Recovery Services-valvet.

- Installera tillägget **AzureBackupWindowsWorkload** på den virtuella datorn. Säkerhetskopiering av en SQL-databas är en agentfri lösning. Tillägget installeras på den virtuella datorn, och det installeras inte någon agent SQL-databasen.

- Skapa tjänstkontot **NT Service\AzureWLBackupPluginSvc** på den virtuella datorn. Alla åtgärder för säkerhetskopiering och återställning använder tjänstkontot. **NT Service\AzureWLBackupPluginSvc** behöver SQL-sysadmin-behörigheter. Alla virtuella SQL Marketplace-datorer har **SqlIaaSExtension** installerat. Tillägget **AzureBackupWindowsWorkload** använder **SQLIaaSExtension** för att automatiskt hämta de behörigheter som krävs. Om den virtuella datorn inte har **SqlIaaSExtension** installerat misslyckas åtgärden Identifiera databas med felmeddelandet `UserErrorSQLNoSysAdminMembership`. Om du vill lägga till sysadmin-behörighet för säkerhetskopiering följer du anvisningarna i avsnittet om att [konfigurera Azure Backup-behörigheter för virtuella SQL-datorer som inte kommer från Marketplace](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms).

    ![Välj den virtuella datorn och databasen](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-databases"></a>Konfigurera säkerhetskopiering för SQL Server-databaser

Azure Backup tillhandahåller hanteringstjänster för att skydda dina SQL Server-databaser och hantera säkerhetskopieringsjobb. Hanterings- och övervakningsfunktionerna beror på ditt Recovery Services-valv.

> [!NOTE]
> Du kan endast ha en säkerhetskopieringslösning åt gången för att säkerhetskopiera SQL Server-databaser. Inaktivera alla andra SQL-säkerhetskopior innan du använder den här funktionen; annars störs den av säkerhetskopiorna och misslyckas. Du kan aktivera Azure Backup för IaaS VM tillsammans med SQL-säkerhetskopiering utan någon konflikt.
>

Så här konfigurerar du skydd för en SQL-databas:

1. Öppna det Recovery Services-valv som har registrerats med den virtuella SQL-datorn.

2. På instrumentpanelen för **Recovery Services-valvet** väljer du **Säkerhetskopiering**. Menyn **Säkerhetskopieringsmål** öppnas.

   ![Välj Säkerhetskopiering för att öppna menyn Säkerhetskopieringsmål](./media/backup-azure-sql-database/open-backup-menu.png)

3. På menyn **Säkerhetskopieringsmål** anger du **Var körs din arbetsbelastning** till standardinställningen: **Azure**.

4. Expandera listrutan **Vad vill du säkerhetskopiera** och välj **SQL Server på Azure VM**.

    ![Välj SQL Server på Azure VM för säkerhetskopieringen](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Menyn **Säkerhetskopieringsmål** visar två steg: **Identifiera databaser på virtuella datorer** och **Konfigurera säkerhetskopiering**.

    Om du har slutfört stegen i den här artikeln i ordning har du upptäckt att de oskyddade virtuella datorerna och det här valvet har registrerats med en virtuell dator. Nu är du redo att konfigurera skydd för SQL-databaserna.

5. På menyn **Säkerhetskopieringsmål** väljer du **Konfigurera säkerhetskopiering**.

    ![Välj Konfigurera säkerhetskopiering](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Azure Backup-tjänsten visar alla SQL Server-instanser med fristående databaser och AlwaysOn-tillgänglighetsgrupper för SQL Server. Om du vill visa de fristående databaserna i SQL Server-instansen väljer du sparren till vänster om instansnamnet. På samma sätt väljer du sparren till vänster om AlwaysOn-tillgänglighetsgruppen för att visa listan över databaser. Följande bild är ett exempel på en fristående instans och en AlwaysOn-tillgänglighetsgrupp.

      ![Visa alla SQL Server-instanser med fristående databaser](./media/backup-azure-sql-database/list-of-sql-databases.png)

6. I listan över databaser markerar du alla databaser som du vill skydda och klickar på **OK**.

    ![Skydda databasen](./media/backup-azure-sql-database/select-database-to-protect.png)

    Du kan välja upp till 50 databaser åt gången. Om du vill skydda fler än 50 databaser gör du flera omgångar. När du har skyddat de första 50 databaserna upprepar du det här steget om du vill skydda nästa uppsättning databaser.

    > [!Note]
    > För att optimera säkerhetskopieringsbelastning delar Azure Backup upp stora säkerhetskopieringsjobb i flera batchar. Det maximala antalet databaser i ett säkerhetskopieringsjobb är 50.
    >

      Alternativt kan du aktivera [automatiskt skydd](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) på hela instansen eller AlwaysOn-tillgänglighetsgruppen genom att välja alternativet **PÅ** i motsvarande listruta i kolumnen **AUTOPROTECT** (Automatiskt skydd). Funktionen [automatiskt skydd](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) aktiverar inte bara skydd på alla befintliga databaser i ett svep, utan skyddar även automatiskt alla nya nya databaser som läggs till i den instansen eller tillgänglighetsgruppen i framtiden.  

      ![Aktivera automatiskt skydd i AlwaysOn-tillgänglighetsgruppen](./media/backup-azure-sql-database/enable-auto-protection.png)

7. För att skapa eller välja en säkerhetskopieringspolicy går du till menyn **Säkerhetskopiering** och väljer **Säkerhetskopieringspolicy**. Menyn **Säkerhetskopieringspolicy** öppnas.

    ![Välja säkerhetskopieringspolicy](./media/backup-azure-sql-database/select-backup-policy.png)

8. I listrutan **Välj säkerhetskopieringspolicy** väljer du en säkerhetskopieringspolicy och väljer **OK**. Information om hur du skapar en Säkerhetskopieringspolicy finns i [Definiera en säkerhetskopieringspolicy](backup-azure-sql-database.md#define-a-backup-policy).

   > [!NOTE]
   > Under förhandsversion kan du inte redigera säkerhetskopieringspolicyer. Om du vill ha en annan policy än vad som finns i listan måste du skapa den policyn. Information om hur du skapar en ny säkerhetskopieringspolicy finns i avsnittet [Definiera en säkerhetskopieringspolicy](backup-azure-sql-database.md#define-a-backup-policy).

    ![Välja en säkerhetskopieringspolicy i listan](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    På menyn **Säkerhetskopieringspolicy** kan du i listrutan **Välj säkerhetskopieringspolicy** göra följande:
    - Välja standardpolicy: **HourlyLogBackup**.
    - Välj en befintlig säkerhetskopieringspolicy som har skapats för SQL.
    - [Definiera en ny policy](backup-azure-sql-database.md#define-a-backup-policy) baserat på ditt RPO och kvarhållningsintervall.

    > [!Note]
    > Azure Backup stöder långsiktig kvarhållning som baseras på säkerhetskopieringsschemat ”farfar-far-son”. Schemat optimerar användningen av lagringsutrymme för serverdelen samtidigt som det uppfyller efterlevnadskrav.
    >

9. När du har valt en säkerhetskopieringspolicy går du till menyn **Säkerhetskopiering** och väljer **Aktivera säkerhetskopiering**.

    ![Aktivera den valda säkerhetskopieringspolicyn](./media/backup-azure-sql-database/enable-backup-button.png)

    Spåra konfigurationsförloppet i **meddelandefältet** på portalen.

    ![Meddelandefältet](./media/backup-azure-sql-database/notifications-area.png)


## <a name="auto-protect-sql-server-in-azure-vm"></a>Automatiskt skydd för SQL Server i virtuell Azure-dator  

Automatiskt skydd kan du automatiskt skydda alla befintliga databaser och databaser som du skulle lägga till i framtiden till en fristående SQL Server-instans eller en SQL Server alltid på tillgänglighetsgruppen. Om automatiskt skydd slås **PÅ** och en säkerhetskopieringspolicy väljs tillämpas det för nyligen skyddade databaser. De befintliga skyddade databaserna fortsätter att använda föregående policy.

![Aktivera automatiskt skydd i AlwaysOn-tillgänglighetsgruppen](./media/backup-azure-sql-database/enable-auto-protection.png)

Det finns ingen gräns för det antal databaser som väljs på en och samma gång med hjälp av funktionen för automatiskt skydd. Konfiguration av säkerhetskopiering utlöses för alla databaser tillsammans och kan spåras i **Säkerhetskopieringsjobb**.

Om du behöver inaktivera automatiskt skydd på i en instans klickar du på instansens namn i **Konfigurera säkerhetskopiering** för att öppna informationspanelen till höger, där **Inaktivera automatiskt skydd** finns längst upp. Klicka på **Inaktivera automatiskt skydd** om du vill inaktivera automatiskt skydd i den instansen.

![Inaktivera automatiskt skydd i den instansen](./media/backup-azure-sql-database/disable-auto-protection.png)

Alla databaser i den instansen fortsätter att skyddas. Den här åtgärden inaktiverar dock automatiskt skydd i alla databaser som läggs till i framtiden.

### <a name="define-a-backup-policy"></a>definierar en säkerhetskopieringspolicy

En säkerhetskopieringspolicy definierar en matris för när säkerhetskopior skapas och hur länge de behålls. Använda Azure Backup för att schemalägga tre typer av säkerhetskopiering för SQL-databaser:

* Fullständig säkerhetskopia: En fullständig säkerhetskopia av databas säkerhetskopierar hela databasen. En fullständig säkerhetskopia innehåller alla data i en specifik databas eller en uppsättning filgrupper eller filer samt tillräckligt med loggar för att återställa dessa data. Du kan endast utlösa en fullständig säkerhetskopiering per dag. Du kan välja att skapa en fullständig säkerhetskopiering en gång per dag eller per vecka.
* Differentiell säkerhetskopia: En differentiell säkerhetskopia baseras på den senaste föregående fullständiga säkerhetskopieringen av data. En differentiell säkerhetskopia sparar endast de data som har ändrats sedan den fullständiga säkerhetskopian. Du kan endast utlösa en differentiell säkerhetskopia per dag. Du kan inte konfigurera en fullständig säkerhetskopia och en differentiell säkerhetskopia samma dag.
* Säkerhetskopia av transaktionslogg: Med en loggsäkerhetskopia kan en återställning som baseras på tidpunkt utföras upp till en specifik sekund. Du kan som mest konfigurera loggsäkerhetskopior var 15:e minut.

Policyn skapas på Recovery Services-valvets nivå. Flera valv kan använda samma säkerhetskopieringspolicy, men du måste tillämpa säkerhetskopieringspolicyn på varje valv. När du skapar en säkerhetskopieringspolicy används den dagliga fullständiga säkerhetskopian som standard. Du kan lägga till en differentiell säkerhetskopia, men endast om du konfigurerar så att fullständiga säkerhetskopior utförs varje vecka. Följande procedur beskriver hur du skapar en säkerhetskopieringspolicy för en SQL Server-instans på en virtuell Azure-dator.

> [!NOTE]
> Under förhandsversion kan du inte redigera säkerhetskopieringspolicyer. I stället måste du skapa en ny policy med önskad information.  

Så här skapar du en säkerhetskopieringspolicy:

1. I det Recovery Services-valv som skyddar SQL-databasen klickar du på **säkerhetskopieringspolicyer** och sedan på **Lägg till**.

   ![Öppna dialogrutan för att skapa ny säkerhetskopieringspolicy](./media/backup-azure-sql-database/new-policy-workflow.png)

   Menyn **Lägg till** visas.

2. På menyn **Lägg till** klickar du på **SQL Server på Azure VM**.

   ![Välj en policytyp för den nya säkerhetskopieringspolicyn](./media/backup-azure-sql-database/policy-type-details.png)

   När SQL Server på Azure VM väljs definieras policytypen, och menyn för säkerhetskopieringspolicy öppnas. På menyn **Säkerhetskopieringspolicy** visas de fält som krävs för en ny säkerhetskopieringspolicy för SQL Server.

3. I **Policynamn** anger du ett namn för den nya policyn.

4. En fullständig säkerhetskopia är obligatorisk. Du kan inte stänga av alternativet **Fullständig säkerhetskopia**. Klicka på **Fullständig säkerhetskopia** för att visa och redigera policyn. Även om du inte ändrar säkerhetskopieringspolicyn bör du granska policyinformationen.

    ![Nya fält för säkerhetskopieringspolicy](./media/backup-azure-sql-database/full-backup-policy.png)

    På menyn **Policy för fullständig säkerhetskopia** går du till **Säkerhetskopieringsfrekvens** och väljer **Dagligen** eller **Varje vecka**. För **Dagligen** väljer du den timme och den tidszon då säkerhetskopieringsjobbet börjar. Du kan inte skapa differentiella säkerhetskopior för dagliga fullständiga säkerhetskopior.

   ![Inställningen för dagligt intervall](./media/backup-azure-sql-database/daily-interval.png)

    För **Varje vecka** väljer du den veckodag, timme och tidszon då säkerhetskopieringsjobbet börjar.

   ![Inställningen för veckovist intervall](./media/backup-azure-sql-database/weekly-interval.png)

5. Som standard är alla alternativ för **Kvarhållningsintervall** markerade: dagligen, varje vecka, varje månad samt varje år. Avmarkera eventuella oönskade begränsningar för kvarhållningsintervall. Ange de intervall som ska användas. På menyn **Policy för fullständig säkerhetskopia** väljer du **OK** för att acceptera inställningarna.

   ![Inställningar för kvarhållningsintervall](./media/backup-azure-sql-database/retention-range-interval.png)

    Återställningspunkter taggas för kvarhållning baserat på deras kvarhållningsintervall. Om du till exempel väljer en daglig fullständig säkerhetskopia utlöses endast en fullständig säkerhetskopia varje dag. Säkerhetskopieringen för en viss dag taggas och behålls baserat på det veckovisa kvarhållningsintervallet och inställningen för veckovis kvarhållning. De månatliga och årliga kvarhållningsintervallen fungerar på liknande sätt.

6. Om du vill lägga till en policy för differentiell säkerhetskopia väljer du **Differentiell säkerhetskopia**. Menyn **Policy för differentiell säkerhetskopia** öppnas.

   ![Öppna menyn för policy för differentiell säkerhetskopia](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    På menyn **Policy för differentiell säkerhetskopia** väljer du **Aktivera** för att öppna kontrollerna för frekvens och kvarhållning. Du kan endast utlösa en differentiell säkerhetskopia per dag.

    > [!Important]
    > Differentiella säkerhetskopior kan behållas i upp till 180 dagar. Om du behöver längre kvarhållning måste du använda fullständiga säkerhetskopior.
    >

   ![Redigera policyn för differentiell säkerhetskopia](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Välj **OK** för att spara policyn och återgå till huvudmenyn **Säkerhetskopieringspolicy**.

7. Om du vill lägga till en policy för loggsäkerhetskopia väljer du **Loggsäkerhetskopia**. Menyn **Loggsäkerhetskopia** öppnas.

    På menyn **Loggsäkerhetskopia** väljer du **Aktivera** och konfigurerar sedan kontrollerna för frekvens och kvarhållning. Loggsäkerhetskopior kan skapas så ofta som var 15:e minut och kan behållas i upp till 35 dagar. Välj **OK** för att spara policyn och återgå till huvudmenyn **Säkerhetskopieringspolicy**.

   ![Redigera policyn för loggsäkerhetskopia](./media/backup-azure-sql-database/log-backup-policy-editor.png)

8. På menyn **Säkerhetskopieringspolicy** väljer du om du vill aktivera **komprimering av SQL-säkerhetskopiering**. Komprimering är inaktiverat som standard.

    På serverdelen använder Azure Backup SQL-specifik säkerhetskopieringskomprimering.

9. När du har slutfört redigeringarna i säkerhetskopieringspolicyn väljer du **OK**.

   ![Acceptera den nya säkerhetskopieringspolicyn](./media/backup-azure-sql-database/backup-policy-click-ok.png)

## <a name="restore-a-sql-database"></a>Återställa en SQL-databas
Azure Backup innehåller funktioner för att återställa enskilda databaser till ett visst datum eller en viss tid (ned till sekunden) med hjälp av säkerhetskopieringar av transaktionsloggen. Azure Backup fastställer automatiskt den lämpliga fullständiga differential och den kedja med säkerhetskopior som krävs för att återställa dina data baserat på dina återställningstider.

Du kan även välja en fullständig eller differentiell säkerhetskopia för att återställa till en specifik återställningspunkt i stället för en viss tid.

### <a name="pre-requisite-before-triggering-a-restore"></a>Förutsättningar för att utlösa en återställning

1. Du kan återställa databasen till en instans av en SQL Server i samma Azure-region. Målservern måste vara registrerad på samma Recovery Services-valv som källan.  
2. Om du vill återställa en TDE-krypterad databas till en annan SQL Server ska du först återställa certifikatet till målservern genom att följa de steg som beskrivs [här](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
3. Innan du utlöser en återställning av ”huvuddatabasen” startar du SQL Server-instansen i enanvändarläge med startalternativet `-m AzureWorkloadBackup`. Argumentet för alternativet `-m` är namnet på klienten. Endast den här klienten tillåts att öppna anslutningen. För alla systemdatabaser (model, master, msdb) ska du stoppa SQL Agent-tjänsten innan du utlöser återställningen. Stäng alla program som kan försöka stjäla en anslutning till någon av dessa databaser.

### <a name="steps-to-restore-a-database"></a>Steg för att återställa en databas:

1. Öppna det Recovery Services-valv som har registrerats med den virtuella SQL-datorn.

2. På instrumentpanelen för **Recovery Services-valv** går du till **Användning** och väljer **Säkerhetskopieringsobjekt** för att öppna menyn **Säkerhetskopieringsobjekt**.

    ![Öppna menyn Säkerhetskopieringsobjekt](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. På menyn **Säkerhetskopieringsobjekt** går du till **Typ av säkerhetskopieringshantering** och väljer **SQL på Azure VM**.

    ![Välja SQL på Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    På menyn **Säkerhetskopieringsobjekt** visas en lista över SQL-databaser.

4. I listan över SQL-databaser väljer du den databas som ska återställas.

    ![Välja den databas som ska återställas](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    När du väljer databasen öppnas dess meny. Menyn visar information om säkerhetskopiering för databasen, däribland:

    * De äldsta och nyaste återställningspunkterna.
    * Status för loggsäkerhetskopia för de senaste 24 timmarna (för databaser i fullständiga och massloggade återställningsmodeller, om de har konfigurerats för säkerhetskopior av transaktionslogg).

5. På menyn för den valda databasen väljer du **Återställ databas**. Menyn **Återställ** öppnas.

    ![Välj Återställ databas](./media/backup-azure-sql-database/restore-db-button.png)

    När menyn **Återställ** öppnas så öppnas även menyn **Återställ konfiguration**. Menyn **Återställ konfiguration** är det första steget för att konfigurera återställningen. Använd den här menyn för att välja var data ska återställas. Alternativen är:
    - **Alternativ plats**: Återställ databasen till en annan plats och behåll den ursprungliga källdatabasen.
    - **Overwrite DB** (Skriv över databas): Återställ data till samma SQL Server-instans som den ursprungliga källan. Det här alternativet gör så att den ursprungliga databasen skrivs över.

    > [!Important]
    > Om den valda databasen tillhör en AlwaysOn-tillgänglighetsgrupp tillåter inte SQL Server att databasen skrivs över. I det här fallet är endast alternativet **Alternativ plats** aktiverat.
    >

    ![Återställ konfigurationsmeny](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Återställa till en alternativ plats

Den här proceduren beskriver hur du återställer data till en alternativ plats. Om du vill skriva över databasen under återställningen fortsätter du till avsnittet om att [återställa och skriva över databasen](backup-azure-sql-database.md#restore-and-overwrite-the-database). I det här skedet är Recovery Services-valvet öppet, och menyn **Återställ konfiguration** är synlig. Om du inte är i det här skedet börjar du med att [återställa en SQL-databas](backup-azure-sql-database.md#restore-a-sql-database).

> [!NOTE]
> Du kan återställa databasen till en instans av en SQL Server i samma Azure-region. Målservern måste vara registrerad på Recovery Services-valvet.
>

På menyn **Återställ konfiguration** visar listrutan **Server** endast de SQL Server-instanser som är registrerade med Recovery Services-valvet. Om den server som du letar efter inte finns i listan kan du hitta servern med hjälp av [Identifiera SQL Server-databaser](backup-azure-sql-database.md#discover-sql-server-databases). Under identifieringsprocessen registreras nya servrar på Recovery Services-valvet.<br>
För att återställa en SQL-databas behöver du följande behörigheter:

* Behörighet som **säkerhetskopieringsoperatör** för det Recovery Services-**valv** som du utför återställningen i.
* Åtkomst som **deltagare (skrivning)** till **den virtuella SQL-källdatorn** (den virtuella datorn som säkerhetskopieras och som du försöker återställa från).
* Åtkomst som **deltagare (skrivning)** till den virtuella SQL-måldatorn (den virtuella dator som du återställer till; vilket i fallet med OLR (Original Location Recovery), är samma virtuella dator som den virtuella källdatorn).

Så här återställer du till en alternativ plats:

1. På menyn **Återställ konfiguration**:

    * Under **Where to Restore** (Var återställning ska ske) väljer du **Alternativ plats**.
    * Öppna listrutan **Server** och väljer SQL Server-instansen för att återställa databasen.
    * Öppna listrutan **Instans** och välj en SQL Server-instans.
    * I rutan **Restored DB Name** (Namn på återställd databas) anger du namnet på måldatabasen.
    * Enligt vad som är tillämpligt väljer du **Overwrite if the DB with the same name already exists on selected SQL instance** (Åsidosätt om en databas med samma namn redan finns i den valda SQL-instansen).
    * Välj **OK** för att slutföra konfigurationen av målet och fortsätt med att välja en återställningspunkt.

    ![Ange värden för menyn Återställ konfiguration](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. På menyn **Select restore point** (Välj återställningspunkt) väljer du **Logs (Point in Time)** (Loggar (tidpunkt)) eller **Full & Differential** (Fullständig och differentiell) som återställningspunkt. Om du vill återställa till en specifik tidpunktslogg fortsätter du med det här steget. Om du vill återställa en fullständig och differentiell återställningspunkt fortsätter du till steg 3.

    ![Välja meny för återställningspunkt](./media/backup-azure-sql-database/recovery-point-menu.png)

    Tidpunktsåterställning är endast tillgängligt för loggsäkerhetskopior för databaser med en fullständig och bulkloggad återställningsmodell. Så här återställer du till en specifik tidpunkt:

    1. Välj **Logs (Point in Time)** (Loggar (tidpunkt)) som återställningstyp.

        ![Välja typ för återställningspunkt](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Under **Restore Date/Time** (Återställ datum/tid) väljer du minikalendern för att öppna **Kalendern**. På **Kalendern** har datumen i fetstil återställningspunkter och det aktuella datumet markerade. Välj ett datum med återställningspunkter. Datum utan återställningspunkter kan inte väljas.

        ![Öppna kalendern](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        När du har valt ett datum visar tidslinjegrafen tillgängliga återställningspunkter i ett kontinuerligt intervall.

    3. Använd tidslinjegrafen eller dialogrutan **Tid** om du vill ange en viss tid för återställningspunkten. Välj **OK** för att slutföra steget med återställningspunkten.

       ![Öppna kalendern](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        Menyn **Select restore point** (Välj återställningspunkt) menyn stängs, och menyn **Avancerad konfiguration** öppnas.

       ![Menyn Avancerad konfiguration](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. På menyn **Avancerad konfiguration**:

        * Om du vill behålla databasen utan drift efter återställningen anger du **Restore with NORECOVERY** (Återställ med NORECOVERY) till **Aktiverat**.
        * Om du vill ändra återställningsplats på målservern anger du en ny sökväg i kolumnen **Mål**.
        * Välj **OK** för godkänna inställningarna. Stäng menyn **Avancerad konfiguration**.

    5. På menyn **Återställ** väljer du **Återställ** för att starta återställningsjobbet. Spåra förloppet för återställningen i **meddelandefältet** eller välj **Återställningsjobb** på databasmenyn.

       ![Förlopp för återställningsjobb](./media/backup-azure-sql-database/restore-job-notification.png)

3. På menyn **Select restore point** (Välj återställningspunkt) väljer du **Logs (Point in Time)** (Loggar (tidpunkt)) eller **Full & Differential** (Fullständig och differentiell) som återställningspunkt. Om du vill återställa en tidpunktslogg går du tillbaka till steg 2. Det här steget återställer en specifik fullständig eller differentiell återställningspunkt. Du kan se alla fullständiga och differentiella återställningspunkter för de senaste 30 dagarna. Om du vill se återställningspunkter som är äldre än 30 dagar väljer du **Filtrera** för att öppna menyn **Filter restore points** (Filtrera återställningspunkter). För en differentiell återställningspunkt återställer Azure Backup först lämplig fullständig återställningspunkt och tillämpar sedan den valda differentiella återställningspunkten.

    ![Välja meny för återställningspunkt](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. På menyn **Select restore point** (Välj återställningspunkt) väljer du **Full & Differential** (Fullständig och differentiell).

       ![Välja fullständig och differentiell](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        På menyn visas listan över tillgängliga återställningspunkter.

    2. Välj en återställningspunkt i listan och välj **OK** för att slutföra återställningspunktsproceduren.

        ![Välja en fullständig återställningspunkt](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        Menyn **Restore Point** (Återställningspunkt) stängs, och menyn **Avancerad konfiguration** öppnas.

        ![Menyn Avancerad konfiguration](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. På menyn **Avancerad konfiguration**:

        * Om du vill behålla databasen utan drift efter återställningen anger du **Restore with NORECOVERY** (Återställ med NORECOVERY) till **Aktiverat**. **Restore with NORECOVERY** (Återställ med NORECOVERY) är inaktiverat som standard.
        * Om du vill ändra återställningsplats på målservern anger du en ny sökväg i kolumnen **Mål**.
        * Välj **OK** för godkänna inställningarna. Stäng menyn **Avancerad konfiguration**.

    4. På menyn **Återställ** väljer du **Återställ** för att starta återställningsjobbet. Spåra förloppet för återställningen i **meddelandefältet** eller välj **Återställningsjobb** på databasmenyn.

       ![Förlopp för återställningsjobb](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Återställa och skriva över databasen

Den här proceduren beskriver hur du återställer data och skriver över en databas. Om du vill återställa till en alternativ plats fortsätter du till [Återställa till en alternativ plats](backup-azure-sql-database.md#restore-to-an-alternate-location). I det här skedet är Recovery Services-valvet öppet, och menyn **Återställ konfiguration** är synlig (se följande bild). Om du inte är i det här skedet börjar du med att [återställa en SQL-databas](backup-azure-sql-database.md#restore-a-sql-database).

![Återställ konfigurationsmeny](./media/backup-azure-sql-database/restore-overwrite-db.png)

På menyn **Återställ konfiguration** visar listrutan **Server** endast de SQL Server-instanser som är registrerade med Recovery Services-valvet. Om den server som du letar efter inte finns i listan kan du hitta servern med hjälp av [Identifiera SQL Server-databaser](backup-azure-sql-database.md#discover-sql-server-databases). Under identifieringsprocessen registreras nya servrar på Recovery Services-valvet.

1. På menyn **Återställ konfiguration** väljer du **Overwrite DB** (Skriv över databas) och sedan **OK** för att slutföra konfigurationen av målet.

   ![Välja Overwrite DB (Skriv över databas)](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    Inställningarna **Server**, **Instans** och **Restored DB Name** (Namn på återställd databas) är inte nödvändiga.

2. På menyn **Select restore point** (Välj återställningspunkt) väljer du **Logs (Point in Time)** (Loggar (tidpunkt)) eller **Full & Differential** (Fullständig och differentiell) som återställningspunkt. Om du vill återställa till en specifik tidpunktslogg fortsätter du med det här steget. Om du vill återställa en fullständig och differentiell återställningspunkt fortsätter du till steg 3.

    ![välja meny för återställningspunkt](./media/backup-azure-sql-database/recovery-point-menu.png)

    Tidpunktsåterställning är endast tillgängligt för loggsäkerhetskopior för databaser med en fullständig och bulkloggad återställningsmodell. Så här återställer du till en specifik sekund:

    1. Välj **Logs (Point in Time)** (Loggar (tidpunkt)) som återställningspunkt.

        ![Välja typ för återställningspunkt](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Under **Restore Date/Time** (Återställ datum/tid) väljer du minikalendern för att öppna **Kalendern**. På **Kalendern** har datumen i fetstil återställningspunkter och det aktuella datumet markerade. Välj ett datum med återställningspunkter. Datum utan återställningspunkter kan inte väljas.

        ![Öppna kalendern](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        När du har valt ett datum visar tidslinjegrafen tillgängliga återställningspunkter.

    3. Använd tidslinjegrafen eller dialogrutan **Tid** om du vill ange en viss tid för återställningspunkten. Välj **OK** för att slutföra steget med återställningspunkten.

       ![Öppna kalendern](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        Menyn **Select restore point** (Välj återställningspunkt) menyn stängs, och menyn **Avancerad konfiguration** öppnas.

       ![Menyn Avancerad konfiguration](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. På menyn **Avancerad konfiguration**:

        * Om du vill behålla databasen utan drift efter återställningen anger du **Restore with NORECOVERY** (Återställ med NORECOVERY) till **Aktiverat**.
        * Om du vill ändra återställningsplats på målservern anger du en ny sökväg i kolumnen **Mål**.
        * Välj **OK** för godkänna inställningarna. Stäng menyn **Avancerad konfiguration**.

    5. På menyn **Återställ** väljer du **Återställ** för att starta återställningsjobbet. Spåra förloppet för återställningen i **meddelandefältet** eller välj **Återställningsjobb** på databasmenyn.

       ![Förlopp för återställningsjobb](./media/backup-azure-sql-database/restore-job-notification.png)

3. På menyn **Select restore point** (Välj återställningspunkt) väljer du **Logs (Point in Time)** (Loggar (tidpunkt)) eller **Full & Differential** (Fullständig och differentiell) som återställningspunkt. Om du vill återställa en tidpunktslogg går du tillbaka till steg 2. Det här steget återställer en specifik fullständig eller differentiell återställningspunkt. Du kan se alla fullständiga och differentiella återställningspunkter för de senaste 30 dagarna. Om du vill se återställningspunkter som är äldre än 30 dagar väljer du **Filtrera** för att öppna menyn **Filter restore points** (Filtrera återställningspunkter). För en differentiell återställningspunkt återställer Azure Backup först lämplig fullständig återställningspunkt och tillämpar sedan den valda differentiella återställningspunkten.

    ![Välja meny för återställningspunkt](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. På menyn **Select restore point** (Välj återställningspunkt) väljer du **Full & Differential** (Fullständig och differentiell).

       ![Välja fullständig och differentiell](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        På menyn visas listan över tillgängliga återställningspunkter.

    2. Välj en återställningspunkt i listan och välj **OK** för att slutföra återställningspunktsproceduren.

        ![Välja en fullständig återställningspunkt](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        Menyn **Restore Point** (Återställningspunkt) stängs, och menyn **Avancerad konfiguration** öppnas.

        ![Menyn Avancerad konfiguration](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. På menyn **Avancerad konfiguration**:

        * Om du vill behålla databasen utan drift efter återställningen anger du **Restore with NORECOVERY** (Återställ med NORECOVERY) till **Aktiverat**. **Restore with NORECOVERY** (Återställ med NORECOVERY) är inaktiverat som standard.
        * Om du vill ändra återställningsplats på målservern anger du en ny sökväg i kolumnen **Mål**.
        * Välj **OK** för godkänna inställningarna. Stäng menyn **Avancerad konfiguration**.

    4. På menyn **Återställ** väljer du **Återställ** för att starta återställningsjobbet. Spåra förloppet för återställningen i **meddelandefältet** eller genom att välja **Återställningsjobb** på databasmenyn.

       ![Förlopp för återställningsjobb](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Hantera Azure Backup-åtgärder för SQL på virtuella Azure-datorer

Det här avsnittet innehåller information om de olika Azure Backup-hanteringsåtgärder som är tillgängliga för SQL på virtuella Azure-datorer. Följande avancerade åtgärder finns:

* Övervaka jobb
* Säkerhetskopieringsaviseringar
* Avbryta skyddet av en SQL-databas
* Återuppta skyddet för en SQL-databas
* Utlösa ett ad hoc-säkerhetskopieringsjobb
* Avregistrera en server som kör SQL Server

### <a name="monitor-backup-jobs"></a>Övervaka säkerhetskopieringsjobb
Azure Backup är en lösning i företagsklass som tillhandahåller avancerade säkerhetskopieringsaviseringar och aviseringar om eventuella fel. (Se avsnittet om att [visa säkerhetskopieringsaviseringar](backup-azure-sql-database.md#view-backup-alerts).) För att övervaka specifika jobb använder du något av följande alternativ baserat på dina krav.

#### <a name="use-the-azure-portal-for-adhoc-operations"></a>Använda Azure-portalen för ad hoc-åtgärder
Azure Backup visar alla manuellt utlösta jobb, eller ad hoc-jobb, i portalen för **säkerhetskopieringsjobb**. De jobb som är tillgängliga i portalen för **säkerhetskopieringsjobb** omfattar:
- Alla åtgärder för konfiguration av säkerhetskopiering.
- Manuellt utlösta säkerhetskopieringsåtgärder.
- Återställningsåtgärder.
- Åtgärder för registrering och identifiering av databaser.
- Åtgärder för att avbryta säkerhetskopiering.

![Portalen för säkerhetskopieringsjobb](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Alla schemalagda säkerhetskopieringsjobb, inklusive fullständiga och differentiella säkerhetskopior samt loggsäkerhetskopior, visas inte i portalen för **säkerhetskopieringsjobb**. Använd SQL Server Management Studio för att övervaka schemalagda säkerhetskopieringsjobb, enligt beskrivningen i nästa avsnitt.
>

#### <a name="use-sql-server-management-studio-for-backup-jobs"></a>Använda SQL Server Management Studio för säkerhetskopieringsjobb
Azure Backup använder SQL-specifika API:er för alla säkerhetskopieringsåtgärder. Använd specifika API:er för att hämta all jobbinformation från [SQL backupset-tabellen](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) i msdb-databasen.

Följande exempel är en fråga som hämtar alla säkerhetskopieringsjobb för en databas med namnet **DB1**. Anpassa frågan för avancerad övervakning.

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  

```

### <a name="view-backup-alerts"></a>Visa säkerhetskopieringsaviseringar

Eftersom loggsäkerhetskopior sker var 15:e minut kan övervakning av säkerhetskopieringsjobb ibland vara tidsödande. Azure Backup är till hjälp i de här fallen. E-postaviseringar utlöses för alla säkerhetskopieringsfel. Aviseringar konsolideras på databasnivå efter felkod. En e-postavisering skickas endast för det första säkerhetskopieringsfelet för en databas. Logga in på Azure-portalen för att övervaka alla fel för en databas.

Så här övervakar du säkerhetskopieringsaviseringar:

1. Logga in på din Azure-prenumeration på [Azure-portalen](https://portal.azure.com).

2. Öppna det Recovery Services-valv som har registrerats med den virtuella SQL-datorn.

3. På instrumentpanelen för **Recovery Services-valvet** väljer du **Aviseringar och händelser**.

   ![Välja aviseringar och händelser](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. På menyn **Aviseringar och händelser** väljer du **Säkerhetskopieringsaviseringar** för att visa listan över aviseringar.

   ![Välja säkerhetskopieringsaviseringar](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-for-a-sql-server-database"></a>Stoppa skydd för en SQL-serverdatabas

När du stoppar skydd för en SQL Server-databas frågar Azure Backup om återställningspunkterna ska behållas. Det finns två sätt att stoppa skyddet för en SQL-databas:

* Stoppa alla framtida säkerhetskopieringsjobb och ta bort alla återställningspunkter.
* Stoppa alla framtida säkerhetskopieringsjobb, men lämna kvar återställningspunkterna.

Om du väljer att stoppa säkerhetskopiering och behålla data rensas återställningspunkterna enligt säkerhetskopieringspolicyn. Du debiteras avgiften för SQL-skyddad instans plus den lagring som använts tills alla återställningspunkter har rensats. Mer information om prissättning för Azure Backup för SQL finns på [sidan med prisinformation för Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

När du stoppar säkerhetskopiering och behåller data upphör återställningspunkterna att gälla enligt bevarandeprincipen, men Azure Backup kommer alltid att ha den senaste återställningspunkten tills du uttryckligen tar bort säkerhetskopierade data. På samma sätt du om du tar bort en datakälla utan att stoppa säkerhetskopieringen börjar nya säkerhetskopior att misslyckas och gamla återställningspunkter upphör att gälla enligt bevarandeprincipen, men den senaste återställningspunkten behålls alltid tills du stoppar säkerhetskopieringen och tar bort data.

Så här stoppar du skydd för en databas:

1. Öppna det Recovery Services-valv som har registrerats med den virtuella SQL-datorn.

2. På instrumentpanelen för **Recovery Services-valv** går du till **Användning** och väljer **Säkerhetskopieringsobjekt** för att öppna menyn **Säkerhetskopieringsobjekt**.

    ![Öppna menyn Säkerhetskopieringsobjekt](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. På menyn **Säkerhetskopieringsobjekt** går du till **Typ av säkerhetskopieringshantering** och väljer **SQL på Azure VM**.

    ![Välja SQL på Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    På menyn **Säkerhetskopieringsobjekt** visas en lista över SQL-databaser.

4. I listan över SQL-databaser väljer du den databas som skydd ska stoppas för.

    ![Välja den databas som skydd ska stoppas för](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    När du väljer databasen öppnas dess meny.

5. På menyn för den valda databasen väljer du **Avbryt säkerhetskopiering**.

    ![Välja Avbryt säkerhetskopiering](./media/backup-azure-sql-database/stop-db-button.png)

    Menyn **Avbryt säkerhetskopiering** öppnas.

6. På menyn **Avbryt säkerhetskopiering** väljer du **Retain Backup Data** (Behåll säkerhetskopieringsdata) eller **Delete Backup Data** (Ta bort säkerhetskopieringsdata). Du kan välja att ange ett skäl till varför du avbryter skyddet samt en kommentar.

    ![Menyn Avbryt säkerhetskopiering](./media/backup-azure-sql-database/stop-backup-button.png)

7. Välj **Avbryt säkerhetskopiering**för att stoppa skyddet för databasen.

  Observera att alternativet **Avbryt säkerhetskopiering** inte fungerar för en databas i en instans med [automatiskt skydd](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm). Det enda sättet att sluta skydda den här databasen är att inaktivera [automatiskt skydd](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) i instansen för tillfället och sedan välja alternativet **Avbryt säkerhetskopiering** i **Säkerhetskopieringsobjekt** för den databasen.<br>
  När du har inaktiverat automatiskt skydd kan du **Avbryt säkerhetskopiering** för databasen under **Säkerhetskopieringsobjekt**. Automatiskt skydd kan nu aktiveras för instansen igen.


### <a name="resume-protection-for-a-sql-database"></a>Återuppta skyddet för en SQL-databas

Om du valde alternativet **Behåll säkerhetskopieringsdata** när skyddet för SQL-databasen stoppades kan du återuppta skyddet. Om säkerhetskopieringsdata inte behölls går det inte att återuppta skyddet.

1. Om du vill återuppta skyddet för SQL-databasen öppnar du säkerhetskopieringsobjektet och väljer **Resume backup** (Återuppta säkerhetskopiering).

    ![Välj Resume backup (Återuppta säkerhetskopiering) för att återuppta databasskyddet](./media/backup-azure-sql-database/resume-backup-button.png)

   Menyn **Säkerhetskopieringspolicy** öppnas.

2. På menyn **Säkerhetskopieringspolicy** väljer du en policy och sedan **Spara**.

### <a name="trigger-an-adhoc-backup"></a>Utlösa en ad hoc-säkerhetskopia

Utlös ad hoc-säkerhetskopior efter behov. Det finns fyra typer av ad hoc-säkerhetskopior:

* Fullständig säkerhetskopia
* Fullständig säkerhetskopia med endast kopiering
* Differentiell säkerhetskopia
* Loggsäkerhetskopia

Mer information om varje typ finns i avsnittet om [typer av SQL-säkerhetskopior](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

### <a name="unregister-a-sql-server-instance"></a>Avregistrera en SQL-serverinstans

Så här avregistrerar du en SQL Server-instans efter att du har tagit bort skyddet men innan du tar bort valvet:

1. Öppna det Recovery Services-valv som har registrerats med den virtuella SQL-datorn.

2. På instrumentpanelen för **Recovery Services-valv** går du till **Hantera** och väljer **Infrastruktur för säkerhetskopiering**.  

   ![Välja infrastruktur för säkerhetskopiering](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. Under **Hanteringsservrar** väljer du **Skyddade servrar**.

   ![Välja skyddade servrar](./media/backup-azure-sql-database/protected-servers.png)

    Menyn **Skyddade servrar** öppnas.

4. På menyn **Skyddade servrar** väljer du den server som du vill avregistrera. Om du vill ta bort valvet måste du avregistrera alla servrar.

5. På menyn **Skyddade servrar** högerklickar du på den skyddade servern och väljer **Ta bort**.

   ![Välja Ta bort](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

Följande avsnitt innehåller ytterligare information om säkerhetskopiering av SQL-databas.

### <a name="can-i-throttle-the-speed-of-the-sql-server-backup-policy"></a>Kan jag begränsa hastigheten för säkerhetskopieringspolicyn i SQL Server?

Ja. Du kan begränsa den hastighet med vilken säkerhetskopieringspolicy körs för att minimera påverkan på en SQL Server-instans.
Så här ändrar du inställningen:
1. I SQL Server-instansen går du till *C:\Program Files\Azure Workload Backup\bin-mappen* och skapar filen **ExtensionSettingsOverrides.json**.
2. I filen **ExtensionSettingsOverrides.json** ändrar du inställningen **DefaultBackupTasksThreshold** till ett lägre värde (till exempel 5) <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Spara ändringarna. Stäng filen.
4. I SQL Server-instansen öppnar du **Aktivitetshanteraren**. Starta om tjänsten **AzureWLBackupCoordinatorSvc**.

### <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Kan jag köra en fullständig säkerhetskopia från en sekundär replik?
Nej. Den här funktionen stöds inte.

### <a name="do-successful-backup-jobs-create-alerts"></a>Skapar lyckade säkerhetskopieringsjobb aviseringar?

Nej. Lyckade säkerhetskopieringsjobb genererar inte aviseringar. Aviseringar skickas bara för säkerhetskopieringsjobb som misslyckas.

### <a name="can-i-see-details-for-scheduled-backup-jobs-in-the-jobs-menu"></a>Kan jag visa detaljer för schemalagda säkerhetskopieringsjobb på jobbmenyn?

Nej. Menyn **Säkerhetskopieringsjobb** visar information om ad hoc-jobb men inte om schemalagda säkerhetskopieringsjobb. Om vissa schemalagda säkerhetskopieringsjobb misslyckas finns information i aviseringarna för misslyckade jobb. För att övervaka alla schemalagda säkerhetskopieringsjobb och ad hoc-säkerhetskopieringsjobb använder du [SQL Server Management Studio](backup-azure-sql-database.md#use-sql-server-management-studio-for-backup-jobs).

### <a name="when-i-select-a-sql-server-instance-are-future-databases-automatically-added"></a>Läggs framtida databaser till automatiskt när jag väljer en SQL Server-instans?

Nej. Om du väljer alternativet för servernivå när du konfigurerar skydd för en SQL Server-instans läggs alla databaser till. Om du lägger till databaser i en SQL Server-instans när du har konfigurerat skydd måste du manuellt lägga till nya databaser för att skydda dem. Databaserna inkluderas inte automatiskt i det konfigurerade skyddet.

### <a name="if-i-change-the-recovery-model-how-do-i-restart-protection"></a>Hur startar jag om skyddet om jag ändrar återställningsmodellen?

Utlös en fullständig säkerhetskopia. Loggsäkerhetskopior börjar som förväntat.

### <a name="can-i-protect-sql-always-on-availability-groups-where-the-primary-replica-is-on-premises"></a>Kan jag skydda SQL AlwaysOn-tillgänglighetsgrupper med lokala primära repliker?

Nej. Azure Backup skyddar SQL-servrar som körs i Azure. Om en tillgänglighetsgrupp (AG, Availability Group) fördelas mellan Azure och lokala datorer kan AG endast skyddas om den primära repliken körs i Azure. Azure Backup skyddar dessutom endast de noder som körs i samma Azure-region som Recovery Services-valvet.

### <a name="can-i-protect-sql-always-on-availability-groups-which-are-spread-across-azure-regions"></a>Kan jag skydda SQL AlwaysOn-tillgänglighetsgrupper som är fördelade mellan Azure-regioner?

Azure Backup Recovery Services-valv kan identifiera och skydda alla noder i samma region som Recovery Services-valvet. Om du har en SQL AlwaysOn-tillgänglighetsgrupp som täcker flera Azure-regioner kan du behöva konfigurera säkerhetskopiering från den region där den primära noden finns. Azure Backup kommer att kunna identifiera och skydda alla databaser i tillgänglighetsgruppen enligt inställningen för säkerhetskopiering. Om inställningen för säkerhetskopiering inte uppfylls misslyckas säkerhetskopieringarna, och du får felaviseringen.

### <a name="while-i-want-to-protect-most-of-the-databases-in-an-instance-i-would-like-to-exclude-a-few-is-it-possible-to-still-use-the-auto-protection-feature"></a>Jag vill skydda de flesta databaser i en instans, men jag vill exkludera några av dem. Går det ändå att använda funktionen för automatiskt skydd?

Nej, [automatiskt skydd](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) gäller för hela instansen. Du kan inte skydda bara vissa databaser en instans med automatiskt skydd.

### <a name="can-i-have-different-policies-for-different-databases-in-an-auto-protected-instance"></a>Kan jag ha olika policyer för olika databaser i en instans med automatiskt skydd?

Om du redan har några skyddade databaser i en instans fortsätter de att skyddas med sina respektive principer även när du slår **PÅ** alternativet för [automatiskt skydd](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm). Alla oskyddade databaser samt de som du vill lägga till i framtiden får dock bara en enda policy som du definierar under **Konfigurera säkerhetskopiering** när databaserna har valts. Till skillnad från andra skyddade databaser kan du inte ens ändra policyn för en databas i en instans med automatiskt skydd.
Det enda sättet att åstadkomma det är att för tillfället inaktivera automatiskt skydd i instansen och sedan ändra policyn för den databasen. Du kan då återaktivera automatiskt skydd för den här instansen.

### <a name="if-i-delete-a-database-from-an-auto-protected-instance-will-the-backups-for-that-database-also-stop"></a>Stoppas säkerhetskopior för en databas om jag tar bort den från en instans med automatiskt skydd?

Nej. Om en databas släpps från en instans med automatiskt skydd ger fortfarande försök att säkerhetskopiera den databasen. Det här innebär att den borttagna databasen börjar visas som felaktig under **Säkerhetskopieringsobjekt** och fortfarande behandlas som skyddad.

Det enda sättet att sluta skydda den här databasen är att inaktivera [automatiskt skydd](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) i instansen för tillfället och sedan välja alternativet **Avbryt säkerhetskopiering** i **Säkerhetskopieringsobjekt** för den databasen. Du kan då återaktivera automatiskt skydd för den här instansen.

###  <a name="why-cant-i-see-the-newly-added-database-to-an-auto-protected-instance-under-the-protected-items"></a>Varför kan jag inte se den databas som nyligen lagts till i en instans med automatiskt skydd under de skyddade objekten?

Du ser kanske inte en databas som nyligen lagts till i en instans med [automatiskt skydd](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) på en gång. Det beror på att identifieringen vanligtvis körs var 8:e timme. Användaren kan dock köra en manuell identifiering med hjälp alternativen för **Recover DBs** (Återställ databaser) för att identifiera och skydda nya databaser omedelbart såsom bilden nedan visar:

  ![Visa nyligen tillagd databas](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>Nästa steg

Om du vill läsa mer om Azure Backup kan du titta på Azure PowerShell-exemplet för att säkerhetskopiera krypterade virtuella datorer.

> [!div class="nextstepaction"]
> [Säkerhetskopiera en krypterad virtuell dator](./scripts/backup-powershell-sample-backup-encrypted-vm.md)

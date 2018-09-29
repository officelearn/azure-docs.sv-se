---
title: Säkerhetskopiera SQL Server-databaser till Azure | Microsoft Docs
description: Den här självstudien beskrivs hur du säkerhetskopierar SQL Server till Azure. Den här artikeln beskriver också återställning av SQL Server.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: aab0ac2dfba47741eaf5a75ef46d9ca5f8873d50
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434253"
---
# <a name="back-up-sql-server-databases-to-azure"></a>Säkerhetskopiera SQL Server-databaser till Azure

SQL Server-databaser är kritiska arbetsbelastningar som kräver ett lågt mål för återställningspunkt (RPO) och långsiktig kvarhållning. Azure Backup är en lösning för säkerhetskopiering SQL Server som kräver ingen infrastruktur: ingen komplexa säkerhetskopieringsserver, ingen hanteringsagent och ingen lagring av säkerhetskopior för att hantera. Azure Backup tillhandahåller centraliserad hantering av dina säkerhetskopior på alla servrar som kör SQL Server eller med olika arbetsbelastningar.

I den här artikeln lär du dig:

> [!div class="checklist"]
> * Förutsättningar för säkerhetskopiering av en SQL Server-instans till Azure.
> * Så här skapar och använder ett Recovery Services-valv.
> * Så här konfigurerar du SQL Server-databasen säkerhetskopieras.
> * Så här anger du en princip för säkerhetskopiering (eller kvarhållning) för återställningspunkterna.
> * Hur du återställer databasen.

Innan du börjar procedurerna i den här artikeln bör du ha en SQL Server-instans som körs i Azure. [Använda SQL Marketplace virtuella datorer och skapa snabbt en SQL Server-instans](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Offentliga begränsningar i förhandsversionen

Följande är kända begränsningar för den offentliga förhandsversionen:

- SQL virtuell dator (VM) kräver en Internetanslutning för att komma åt Azure offentliga IP-adresser. Mer information finns i [nätverksanslutningen](backup-azure-sql-database.md#establish-network-connectivity).
- Skydda upp till 2 000 SQL-databaser i ett Recovery Services-valv. Ytterligare SQL-databaser ska lagras i separata Recovery Services-valvet.
- [Säkerhetskopior av distribuerade Tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) har begränsningar.
- SQL Server alltid på Redundansklusterinstanser (FCIs) stöds inte.
- Använd Azure-portalen för att konfigurera Azure Backup för att skydda SQL Server-databaser. Azure PowerShell, Azure CLI och REST-API: er stöds inte för närvarande.

Se [vanliga frågor och svar](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#faq) för mer information om support/inte scenarier som stöds.

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

Det här avsnittet beskrivs Azure Backup stöd för operativsystem och versioner av SQL Server. SQL Marketplace Azure virtuella datorer och inte finns på Marketplace-datorer (där SQL Server manuellt installeras) stöds.

### <a name="supported-operating-systems"></a>Operativsystem som stöds

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Linux stöds inte för närvarande.

### <a name="supported-sql-server-versions-and-editions"></a>SQL Server-versioner och utgåvor som stöds

- SQL Server 2012 Enterprise, Standard, webb, Developer, Express
- SQL Server 2014 Enterprise, Standard, webb, Developer, Express
- SQL Server 2016 Enterprise, Standard, webb, Developer, Express
- SQL Server 2017 Enterprise, Standard, webb, Developer, Express

## <a name="prerequisites"></a>Förutsättningar

Innan du säkerhetskopierar SQL Server-databasen kan du kontrollera följande villkor:

- Identifiera eller [skapar ett Recovery Services-valv](backup-azure-sql-database.md#create-a-recovery-services-vault) i samma region eller språk som den virtuella datorn som är värd för SQL Server-instansen.
- [Kontrollera behörigheterna för den virtuella datorn](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) som behövs för att säkerhetskopiera SQL-databaser.
- Kontrollera att den [SQL-dator är ansluten till nätverket](backup-azure-sql-database.md#establish-network-connectivity).

> [!NOTE]
> Du kan ha endast en lösning för säkerhetskopiering åt gången för att säkerhetskopiera SQL Server-databaser. Inaktivera alla andra SQL-säkerhetskopior innan du använder den här funktionen; i annat fall kommer säkerhetskopieringarna påverka och misslyckas. Du kan aktivera Azure Backup tillsammans med SQL-säkerhetskopiering utan någon konflikt för IaaS VM.
>

Om dessa villkor finns i din miljö, fortsätter du till [Konfigurera säkerhetskopiering för SQL Server-databaser](backup-azure-sql-database.md#configure-backup-for-sql-server-databases). Om några av krav inte finns, Fortsätt att läsa.


## <a name="establish-network-connectivity"></a>Upprätta nätverksanslutning

SQL-dator ha anslutning till Azure offentliga IP-adresser för alla åtgärder. SQL VM-åtgärder (till exempel databasidentifiering, säkerhetskopieringar, schemalägga säkerhetskopieringar, återställa återställningspunkter och så vidare) misslyckas utan anslutning till offentliga IP-adresser. Använd något av följande alternativ för att tillhandahålla ett tydligt sätt för säkerhetskopieringstrafik:

- Lista över tillåtna Azure datacenter IP-intervall: godkänna Azure datacenter IP-adressintervall, använda den [Download Center-sidan för information om IP-intervall och instruktioner](https://www.microsoft.com/download/details.aspx?id=41653). 
- Distribuera en HTTP-proxyserver kan dirigera trafik: när du säkerhetskopierar en SQL-databas på en virtuell dator tillägget på den virtuella datorn använder HTTPS-API: er för att skicka kommandon för hantering av Azure Backup och data till Azure Storage. Säkerhetskopieringstillägget använder också Azure Active Directory (Azure AD) för autentisering. Dirigera säkerhetskopieringstillägget trafik för dessa tre tjänster via HTTP-proxy. Tillägget är den enda komponenten som är konfigurerad för åtkomst till det offentliga internet.

Rätt balans mellan alternativen är hanterbarhet, detaljerad kontroll och kostnad.

> [!NOTE]
> Tjänsttaggar för Azure Backup ska vara tillgänglig vid allmän tillgänglighet.
>

| Alternativ | Fördelar | Nackdelar |
| ------ | ---------- | ------------- |
| Whitelist IP-intervall | Ingen extra kostnad. <br/> För åtkomst till öppna i en NSG måste använda den **Set-AzureNetworkSecurityRule** cmdlet. | Komplicerat att hantera eftersom de berörda IP-intervallen ändras över tid. <br/>Ger åtkomst till hela Azure, inte bara Azure Storage.|
| Använda en HTTP-proxy   | Detaljerad kontroll i proxyn över lagringen URL: er tillåts. <br/>Enskild punkt för internet-åtkomst till virtuella datorer. <br/> Inte kan komma att ändras för Azure-IP-adress. | Ytterligare kostnader för att köra en virtuell dator med proxy-programvara. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Ange behörigheter för icke - SQL virtuella datorer på Marketplace

Om du vill säkerhetskopiera en virtuell dator, Azure Backup kräver den **AzureBackupWindowsWorkload** tillägget installeras. Om du använder Azure Marketplace-datorer kan fortsätta att [identifiera SQL Server-databaser](backup-azure-sql-database.md#discover-sql-server-databases). Om den virtuella datorn som är värd för dina SQL-databaser inte är skapade från Azure Marketplace, Slutför följande procedur för att installera tillägget och ange lämpliga behörigheter. Förutom den **AzureBackupWindowsWorkload** tillägg, Azure Backup kräver SQL sysadmin-behörighet för att skydda SQL-databaser. För att identifiera databaser på den virtuella datorn, Azure Backup skapar kontot **NT Service\AzureWLBackupPluginSvc**. För Azure Backup för att identifiera SQL-databaser, den **NT Service\AzureWLBackupPluginSvc** kontot måste ha SQL och SQL sysadmin-behörighet. Följande procedur beskriver hur du anger dessa behörigheter.

Konfigurera behörigheter:

1. I den [Azure-portalen](https://portal.azure.com), öppna Recovery Services-valvet som används för att skydda SQL-databaser.

2. På den **Recovery Services-valv** instrumentpanelen, väljer **Backup**. Den **säkerhetskopieringsmål** menyn öppnas.

   ![Välj säkerhetskopia för att öppna menyn säkerhetskopieringsmål](./media/backup-azure-sql-database/open-backup-menu.png)

3. På den **säkerhetskopieringsmål** menyn och ange **var körs din arbetsbelastning** standardinställning: **Azure**.

4. Expandera den **vad vill du säkerhetskopiera** nedrullningsbara listrutan och välj **SQL Server i Azure VM**.

    ![Välj SQL Server i virtuell Azure-dator för säkerhetskopiering](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Den **säkerhetskopieringsmål** menyn visas två steg: **identifiera databaser i virtuella datorer** och **Konfigurera säkerhetskopiering**. Den **identifiera databaser i virtuella datorer** steg starta en sökning efter Azure-datorer.

    ![Gå igenom stegen för två säkerhetskopieringsmål](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. Under **identifiera databaser i virtuella datorer**väljer **Starta identifiering** att söka efter oskyddade virtuella datorer i prenumerationen. Det kan ta en stund att söka igenom alla de virtuella datorerna. Söktiden beror på hur många över oskyddade virtuella datorer i prenumerationen.

    ![Säkerhetskopiering väntar vid sökning efter databaser i virtuella datorer](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    När en virtuell dator i oskyddade identifieras så visas den i listan. Oskyddade virtuella datorer visas efter deras VM-namn och resursgrupp grupp. Det är möjligt för flera virtuella datorer har samma namn. Men virtuella datorer med samma namn som hör till olika resursgrupper. Om en virtuell dator i förväntade inte visas kan du se om den virtuella datorn redan skyddas i ett valv.

6. I listan över virtuella datorer, väljer du den virtuella datorn med SQL-databas om du vill säkerhetskopiera och välj sedan **identifiera databaser**. 

    Identifieringsprocessen installerar den **AzureBackupWindowsWorkload** tillägget på den virtuella datorn. Tillägget kan kommunicera med den virtuella datorn så att du kan säkerhetskopiera SQL-databaser med Azure Backup-tjänsten. När tillägget installeras Azure Backup skapar det virtuella Windows-tjänstkontot **NT Service\AzureWLBackupPluginSvc** på den virtuella datorn. Det virtuella tjänstkontot kräver SQL sysadmin-behörighet. När virtuella konto installeras, om du får felet `UserErrorSQLNoSysadminMembership`, se [åtgärda SQL sysadmin-behörighet](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

    Den **meddelanden** området visar förloppet för identifieringen för databasen. Det kan ta en stund innan jobbet är klart. Jobbtiden beror på hur många databaser som finns på den virtuella datorn. När de valda databaserna identifieras, visas ett meddelande.

    ![Meddelande för distribution](./media/backup-azure-sql-database/notifications-db-discovered.png)

När du kopplar databasen med Recovery Services-valvet, nästa steg är att [konfigurera säkerhetskopieringsjobbet](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

### <a name="fix-sql-sysadmin-permissions"></a>Åtgärda SQL sysadmin-behörighet

Under installationen, om du får felet `UserErrorSQLNoSysadminMembership`, använda ett konto med SQL Server sysadmin-behörighet för att logga in till SQL Server Management Studio (SSMS). Om du inte behöver särskilda behörigheter, ska Windows-autentisering fungera.

1. Öppna mappen Security/inloggningar på SQL-servern.

    ![Öppna mappen Security/inloggningar för att se konton](./media/backup-azure-sql-database/security-login-list.png)

2. Högerklicka på mappen inloggningar och välj **ny inloggning**. I den **inloggning – ny** dialogrutan **Search**.

    ![Välj i inloggning - ny dialogrutan Sök](./media/backup-azure-sql-database/new-login-search.png)

3. De virtuella Windows-tjänstkontot **NT Service\AzureWLBackupPluginSvc** skapades under registrering av virtuell dator och SQL-identifieringsfas. Ange kontonamnet som visas i den **ange objektnamn att välja** rutan. Välj **Kontrollera namn** att matcha namnet. 

    ![Välj Kontrollera namn för att matcha namnet okänd tjänst](./media/backup-azure-sql-database/check-name.png)

4. Välj **OK** att stänga dialogrutan.

5. I den **serverroller** , ser du till den **sysadmin** roll har valts. Välj **OK** att stänga dialogrutan.

    ![Kontrollera att serverrollen sysadmin har valts](./media/backup-azure-sql-database/sysadmin-server-role.png)

    Behörigheterna som krävs ska nu finnas.

6. Även om du har åtgärdat felet behörigheter måste du koppla databasen till Recovery Services-valvet. I Azure-portalen i den **skyddade servrar** högerklickar du på den server som är ett felaktigt tillstånd och välj **identifiera databaser**.

    ![Kontrollera att servern har rätt behörighet](./media/backup-azure-sql-database/check-erroneous-server.png)

    Den **meddelanden** området visar förloppet för identifieringen för databasen. Det kan ta en stund innan jobbet är klart. Jobbtiden beror på hur många databaser som finns på den virtuella datorn. När de valda databaserna hittas, visas ett meddelande.

    ![Meddelande för distribution](./media/backup-azure-sql-database/notifications-db-discovered.png)

När du kopplar databasen med Recovery Services-valvet, nästa steg är att [konfigurera säkerhetskopieringsjobbet](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Identifiera SQL Server-databaser

Azure Backup identifierar alla databaser på en SQL Server-instans. Du kan skydda databaserna enligt dina behov för säkerhetskopiering. Använd följande procedur för att identifiera den virtuella datorn som är värd för SQL-databaser. När du har identifierat den virtuella datorn installerar Azure Backup ett enkelt tillägg för att identifiera SQL Server-databaser.

1. Logga in på din prenumeration i den [Azure-portalen](https://portal.azure.com/).

2. På menyn till vänster väljer **alla tjänster**.

    ![Välj alla tjänster](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. I den **alla tjänster** dialogrutan anger **återställningstjänster**. När du skriver filtrerar listan över resurser i dina indata. Välj **Recovery Services-valv** i listan.

    ![Ange och välj Recovery Services-valv](./media/backup-azure-sql-database/all-services.png) <br/>

    Listan över Recovery Services-valv i prenumerationen visas. 

4. I listan över Recovery Services-valv, väljer du valvet för att använda för att skydda din SQL-databaser.

5. På den **Recovery Services-valv** instrumentpanelen, väljer **Backup**. Den **säkerhetskopieringsmål** menyn öppnas.

   ![Välj säkerhetskopia för att öppna menyn säkerhetskopieringsmål](./media/backup-azure-sql-database/open-backup-menu.png)

6. På den **säkerhetskopieringsmål** menyn och ange **var körs din arbetsbelastning** standardinställning: **Azure**.

7. Expandera den **vad vill du säkerhetskopiera** nedrullningsbara listrutan och välj **SQL Server i Azure VM**.

    ![Välj SQL Server i virtuell Azure-dator för säkerhetskopiering](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Den **säkerhetskopieringsmål** menyn visas två steg: **identifiera databaser i virtuella datorer** och **Konfigurera säkerhetskopiering**.
    
    ![Gå igenom stegen för två säkerhetskopieringsmål](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. Under **identifiera databaser i virtuella datorer**väljer **Starta identifiering** att söka efter oskyddade virtuella datorer i prenumerationen. Det kan ta en stund att söka igenom alla virtuella datorer. Söktiden beror på hur många över oskyddade virtuella datorer i prenumerationen.

    ![Säkerhetskopiering väntar vid sökning efter databaser i virtuella datorer](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    När en virtuell dator i oskyddade identifieras så visas den i listan. Flera virtuella datorer kan ha samma namn. Men virtuella datorer med samma namn som hör till olika resursgrupper. Oskyddade virtuella datorer visas efter deras VM-namn och resursgrupp grupp. Om en virtuell dator i förväntade inte visas kan du se om den virtuella datorn redan skyddas i ett valv.

9. I listan över virtuella datorer, väljer du den virtuella datorn med SQL-databas om du vill säkerhetskopiera och välj sedan **identifiera databaser**.

    Azure Backup identifierar alla SQL-databaser på den virtuella datorn. Information om vad som händer under fasen för identifiering av databasen finns i [Background operations](backup-azure-sql-database.md#background-operations). När SQL-databaser har identifierats, är du redo att [konfigurera säkerhetskopieringsjobbet](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

### <a name="background-operations"></a>Bakgrundsåtgärder

När du använder den **identifiera databaser** verktyg, Azure Backup utförs följande åtgärder i bakgrunden:

- Registrera den virtuella datorn med Recovery Services-valv för säkerhetskopiering av arbetsbelastning. Alla databaser på den registrerade virtuella datorn kan säkerhetskopieras till endast den här Recovery Services-valv. 

- Installera den **AzureBackupWindowsWorkload** tillägget på den virtuella datorn. Säkerhetskopiera från en SQL-databas är en agentlös lösning. Tillägget har installerats på den virtuella datorn och ingen agent installerad på SQL-databasen.

- Skapa kontot **NT Service\AzureWLBackupPluginSvc** på den virtuella datorn. Alla åtgärder för säkerhetskopiering och återställning använder kontot. **NT Service\AzureWLBackupPluginSvc** måste SQL sysadmin-behörighet. Alla SQL Marketplace virtuella datorer som medföljer den **SqlIaaSExtension** installerad. Den **AzureBackupWindowsWorkload** tillägget använder den **SQLIaaSExtension** att automatiskt få behörigheterna som krävs. Om den virtuella datorn inte har den **SqlIaaSExtension** installerat, identifiera DB åtgärden misslyckas med felmeddelandet `UserErrorSQLNoSysAdminMembership`. Om du vill lägga till sysadmin-behörighet för säkerhetskopiering, följer du anvisningarna i [konfigurera Azure Backup behörigheter för icke - SQL virtuella datorer på Marketplace](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms).

    ![Välj den virtuella datorn och databasen](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-databases"></a>Konfigurera säkerhetskopiering för SQL Server-databaser

Azure Backup tillhandahåller hanteringstjänster för att skydda SQL Server-databaser och hantera säkerhetskopieringsjobb. Hanterings- och övervakningsfunktioner beror på Recovery Services-valvet. 

> [!NOTE]
> Du kan ha endast en lösning för säkerhetskopiering åt gången för att säkerhetskopiera SQL Server-databaser. Inaktivera alla andra SQL-säkerhetskopior innan du använder den här funktionen; i annat fall kommer säkerhetskopieringarna påverka och misslyckas. Du kan aktivera Azure Backup tillsammans med SQL-säkerhetskopiering utan någon konflikt för IaaS VM.
>

Konfigurera skydd för en SQL-databas:

1. Öppna Recovery Services-valvet som har registrerats hos SQL-dator.

2. På den **Recovery Services-valv** instrumentpanelen, väljer **Backup**. Den **säkerhetskopieringsmål** menyn öppnas.

   ![Välj säkerhetskopia för att öppna menyn säkerhetskopieringsmål](./media/backup-azure-sql-database/open-backup-menu.png)

3. På den **säkerhetskopieringsmål** menyn och ange **var körs din arbetsbelastning** standardinställning: **Azure**.

4. Expandera den **vad vill du säkerhetskopiera** nedrullningsbara listrutan och välj **SQL Server i Azure VM**.

    ![Välj SQL Server i virtuell Azure-dator för säkerhetskopiering](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Den **säkerhetskopieringsmål** menyn visas två steg: **identifiera databaser i virtuella datorer** och **Konfigurera säkerhetskopiering**.
    
    Om du har slutfört stegen i den här artikeln i ordning, har du hittat oskyddade virtuella datorer och det här valvet har registrerats med en virtuell dator. Nu är du redo att konfigurera skydd för SQL-databaser.
    
5. På den **säkerhetskopieringsmål** menyn och välj **Konfigurera säkerhetskopiering**.

    ![Välj Konfigurera säkerhetskopiering](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Azure Backup-tjänsten visar alla SQL Server-instanser med fristående databaser och SQL Server Always On-Tillgänglighetsgrupper. Om du vill visa de fristående databaserna i SQL Server-instansen, väljer du på ikonen till vänster om namnet på instansen. Följande bilder visar exempel på en fristående instans och en Always On-tillgänglighetsgrupp.

    > [!NOTE]
    > Säkerhetskopieringsinställningen för SQL är användas för en SQL Server Always On i tillgänglighetsgruppen. Men på grund av en SQL-plattformsbegränsning fullständiga och differentiella säkerhetskopieringar måste göras från den primära noden. Log tillbaka sker upp enligt din inställning för säkerhetskopiering. På grund av den här begränsningen måste den primära noden alltid registreras för Tillgänglighetsgrupper.
    >

    ![Lista över databaser i SQL-instansen](./media/backup-azure-sql-database/discovered-databases.png)

    Välj ikonen till vänster om Always On availability-gruppen att visa listan över databaser.

    ![Lista över databaser i Always On availability-gruppen](./media/backup-azure-sql-database/discovered-database-availability-group.png)

6. Välj alla databaser att skydda och välj sedan i listan över databaser, **OK**.

    ![Välj flera databaser för att skydda](./media/backup-azure-sql-database/select-multiple-database-protection.png)

    Välj upp till 50 databaser i taget. Skydda fler än 50 databaser, se flera pass. När du skyddar de första 50 databaserna kan du upprepa det här steget om du vill skydda en uppsättning databaser.

    > [!Note] 
    > För att optimera säkerhetskopierade belastning, delar Azure Backup upp stora säkerhetskopieringsjobb i flera batchar. Det maximala antalet databaser i en säkerhetskopiering är 50.
    >
    >

7. Att skapa eller välj en säkerhetskopieringsprincip på den **säkerhetskopiering** menyn och välj **säkerhetskopieringspolicy**. Den **säkerhetskopieringspolicy** menyn öppnas.

    ![Välj princip för säkerhetskopiering](./media/backup-azure-sql-database/select-backup-policy.png)

8. I den **Välj säkerhetskopieringspolicy** nedrullningsbara listrutan väljer du en princip för säkerhetskopiering och välj sedan **OK**. Information om hur du skapar en princip för säkerhetskopiering finns i [definierar en säkerhetskopieringspolicy](backup-azure-sql-database.md#define-a-backup-policy).

   > [!NOTE]
   > Du kan inte redigera principer för säkerhetskopiering för förhandsversionen. Om du vill att en annan princip än vad som finns i listan, måste du skapa principen. Information om hur du skapar en ny säkerhetskopieringsprincip finns i avsnittet [definierar en säkerhetskopieringspolicy](backup-azure-sql-database.md#define-a-backup-policy).

    ![Välj en princip för säkerhetskopiering i listan](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    På den **säkerhetskopieringspolicy** menyn i den **Välj säkerhetskopieringspolicy** nedrullningsbara listrutan kan du: 
    - Välj principen: **HourlyLogBackup**.
    - Välj en befintlig säkerhetskopieringspolicy som har skapats för SQL.
    - [Definiera en ny princip](backup-azure-sql-database.md#define-a-backup-policy) baserat på din RPO och kvarhållning. 

    > [!Note]
    > Azure Backup stöder långsiktig kvarhållning som baseras på farfar-far-son säkerhetskopiering schemat. Schemat optimerar användningen av lagringsutrymme för backend-medan möte efterlevnad behöver.
    >

9. När du har valt en säkerhetskopieringsprincip på den **säkerhetskopieringsmenyn**väljer **Aktivera säkerhetskopiering**.

    ![Aktivera den valda säkerhetskopieringsprincipen](./media/backup-azure-sql-database/enable-backup-button.png)

    Spåra konfigurationsförloppet i den **meddelanden** området i portalen.

    ![Meddelandefält](./media/backup-azure-sql-database/notifications-area.png)


### <a name="define-a-backup-policy"></a>definierar en säkerhetskopieringspolicy

En princip för säkerhetskopiering definierar en matris över när säkerhetskopior tas och hur länge de är kvar. Använda Azure Backup för att schemalägga tre typer av säkerhetskopiering för SQL-databaser:

* Fullständig säkerhetskopiering: en fullständig säkerhetskopia säkerhetskopierar hela databasen. En fullständig säkerhetskopia innehåller alla data i en specifik databas eller en uppsättning filgrupper eller tillräckligt många loggar att återställa dessa data och filer. Du kan endast utlösa en fullständig säkerhetskopiering per dag. Du kan välja att ta en fullständig säkerhetskopiering med dagliga och veckovisa intervall. 
* Differentiell säkerhetskopiering: en differentiell säkerhetskopiering baseras på den senaste, föregående fullständig säkerhetskopieringen. En differentiell säkerhetskopiering fångar endast de data som ändrats sedan den fullständiga säkerhetskopian. Du kan endast utlösa en differentiell säkerhetskopiering per dag. Du kan inte konfigurera en fullständig säkerhetskopia och en differentiell säkerhetskopiering på samma dag.
* Säkerhetskopiering av transaktionsloggen: en loggsäkerhetskopiering gör det möjligt för point-in-time-återställning upp till en specifik sekund. Du kan högst, konfigurera transaktionell loggsäkerhetskopior var 15: e minut.

Principens Skapad Recovery Services-valvet nivå. Flera valv kan använda samma säkerhetskopieringsprincip, men du måste använda principen för säkerhetskopiering för varje valv. När du skapar en princip för säkerhetskopiering, används den dagliga fullständiga säkerhetskopian som standard. Du kan lägga till en differentiell säkerhetskopiering, men endast om du konfigurerar fullständiga säkerhetskopieringar ska göras varje vecka. Följande procedur beskriver hur du skapar en princip för säkerhetskopiering för en SQL Server-instans i Azure-datorer. 

> [!NOTE]
> I förhandsversionen kan redigera du inte en princip för säkerhetskopiering. I stället måste du skapa en ny princip med önskad information.  
 
Skapa en princip för säkerhetskopiering:

1. I Recovery Services-valvet som skyddar SQL-databasen, klickar du på **Säkerhetskopieringsprinciper**, och klicka sedan på **Lägg till**. 

   ![Öppna dialogrutan Skapa ny princip för säkerhetskopiering](./media/backup-azure-sql-database/new-policy-workflow.png)

   Den **Lägg till** meny.

2. I den **Lägg till** -menyn klickar du på **SQL Server i Azure VM**.

   ![Välj en Principtyp av för den nya principen för säkerhetskopiering](./media/backup-azure-sql-database/policy-type-details.png)

   Att välja SQL Server i Azure VM definierar principtypen och öppnar menyn säkerhetskopieringspolicy. Den **säkerhetskopieringspolicy** menyn visar fälten som krävs för en ny säkerhetskopieringsprincip för SQL Server.

3. I **principnamn**, ange ett namn för den nya principen.

4. En fullständig säkerhetskopia är obligatoriska. Du kan inte inaktivera den **fullständig säkerhetskopiering** alternativet. Klicka på **fullständig säkerhetskopiering** att visa och redigera principen. Även om du inte ändrar säkerhetskopieringspolicyn, bör du granska information om principen.

    ![den nya principen för säkerhetskopiering fält](./media/backup-azure-sql-database/full-backup-policy.png)

    I den **princip för fullständig säkerhetskopiering** menyn för **säkerhetskopieringsfrekvens**, Välj **dagliga** eller **veckovisa**. För **dagliga**, väljer en timme och tidszonen när jobbet börjar. Du kan inte skapa differentiella säkerhetskopieringar för dagliga fullständiga säkerhetskopieringar.

   ![inställningen för varje dag](./media/backup-azure-sql-database/daily-interval.png)

    För **veckovisa**, välja dagen i veckan, timme och tidszon när jobbet börjar.

   ![inställningen för varje vecka](./media/backup-azure-sql-database/weekly-interval.png)

5. Som standard alla **Kvarhållningsintervall** alternativen är markerade: dagliga, veckovisa, månatliga och årliga. Avmarkera eventuella oönskade kvarhållning intervallet gränser. Ange intervall för att använda. I den **princip för fullständig säkerhetskopiering** menyn och välj **OK** att acceptera inställningarna.

   ![Kvarhållningsinställningar intervallet intervall](./media/backup-azure-sql-database/retention-range-interval.png)

    Återställningspunkter är taggade för kvarhållning av säkerhetskopior baserat på deras Kvarhållningsintervall. Exempel: Om du väljer en daglig fullständig säkerhetskopiering utlöses endast en fullständig säkerhetskopiering varje dag. Säkerhetskopiering baserat för en viss dag är märkta och bevaras på veckovisa kvarhållningsintervallet och din veckovisa kvarhållningsinställning. Månatliga och årliga Kvarhållningsintervall fungerar på liknande sätt.

6. Om du vill lägga till en princip för differentiell säkerhetskopiering, Välj **differentiell säkerhetskopiering**. Den **princip för differentiell säkerhetskopiering** menyn öppnas. 

   ![Öppna menyn princip för differentiell säkerhetskopiering](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    På den **princip för differentiell säkerhetskopiering** menyn och välj **aktivera** att öppna kontrollerna frekvens och kvarhållning. Du kan endast utlösa en differentiell säkerhetskopiering per dag.
    
    > [!Important] 
    > Differentiella säkerhetskopieringar kan behållas i upp till 180 dagar. Om du behöver längre kvarhållning, måste du använda fullständiga säkerhetskopieringar.
    >

   ![Redigera princip för differentiell säkerhetskopiering](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Välj **OK** att spara principen och återgå till huvudsidan **säkerhetskopieringspolicy** menyn.

7. Om du vill lägga till en princip för säkerhetskopiering av transaktionella log, Välj **Loggsäkerhetskopior**. Den **Loggsäkerhetskopiering** menyn öppnas.

    I den **Loggsäkerhetskopiering** menyn och välj **aktivera**, och Ställ in frekvensen och kvarhållning kontroller. Loggsäkerhetskopior kan ske så ofta som var 15: e minut och kan behållas i upp till 35 dagar. Välj **OK** att spara principen och återgå till huvudsidan **säkerhetskopieringspolicy** menyn.

   ![Redigera log-principen för säkerhetskopiering](./media/backup-azure-sql-database/log-backup-policy-editor.png)

8. På den **säkerhetskopieringspolicy** menyn, Välj om du vill aktivera **komprimering av SQL-säkerhetskopiering**. Komprimering är inaktiverad som standard.

    På backend-servern använder Azure Backup SQL native säkerhetskopieringskomprimering.

9. När du har slutfört redigeringar i säkerhetskopieringsprincipen väljer **OK**. 

   ![Acceptera den nya principen för säkerhetskopiering](./media/backup-azure-sql-database/backup-policy-click-ok.png)

## <a name="restore-a-sql-database"></a>Återställa en SQL-databas
Azure Backup innehåller funktioner för att återställa enskilda databaser till ett visst datum eller tid (till andra) med hjälp av säkerhetskopieringar av transaktionsloggen. Azure Backup anger automatiskt lämpliga fullständig differentiella och kedja av säkerhetskopior som krävs för att återställa dina data baserat på din återställningstiderna.

Du kan också välja en fullständig eller Differentiell säkerhetskopia att återställa till en specifik återställningspunkt i stället för en viss tid.

### <a name="pre-requisite-before-triggering-a-restore"></a>Krav innan du utlöser en återställning

1. Du kan återställa databasen till en instans av en SQL-Server i samma Azure-region. Målservern måste vara registrerad på samma Recovery Services-valv som källa.  
2. Om du vill återställa en krypterad TDE-databas till en annan SQL Server, först Återställ certifikatet till målservern genom att följa stegen som beskrivs [här](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
3. Innan du utlöser en återställning av ”master”-databasen, starta SQL Server-instansen i enanvändarläge med startalternativ `-m AzureWorkloadBackup`. Argumentet för den `-m` alternativet är namnet på klienten. Endast den här klienten tillåts att öppna anslutningen. Stoppa tjänsten SQL Agent för alla systemdatabaser (modell, master, msdb) innan du utlöser återställningen. Stäng alla program som kan försöka stjäla en anslutning till någon av dessa databaser.

### <a name="steps-to-restore-a-database"></a>Steg för att återställa en databas:

1. Öppna Recovery Services-valvet som har registrerats hos SQL-dator.

2. På den **Recovery Services-valv** instrumentpanelen under **användning**väljer **Säkerhetskopieringsobjekt** att öppna den **Säkerhetskopieringsobjekt** menyn.

    ![Öppna menyn Säkerhetskopieringsobjekt](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. På den **Säkerhetskopieringsobjekt** menyn under **typ av Säkerhetskopieringshantering**väljer **SQL i Azure VM**. 

    ![Välj SQL i Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    Den **Säkerhetskopieringsobjekt** menyn visar en lista över SQL-databaser. 

4. I listan över SQL-databaser, väljer du databasen som ska återställas.

    ![Välj databas för att återställa](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    När du väljer databasen, öppnar menyn. Menyn visar säkerhetskopiering information för databasen, inklusive:

    * Den äldsta och de senaste återställningspunkter.
    * Logga Säkerhetskopieringsstatus för de senaste 24 timmarna (för databaser i fullständiga och massloggade återställningsmodellen, om konfigurerad för transaktionell loggsäkerhetskopior).

5. Välj på menyn för den valda databasen **återställa DB**. Den **återställa** menyn öppnas.

    ![Välj Återställ databasen](./media/backup-azure-sql-database/restore-db-button.png)

    När den **återställa** menyn öppnas den **återställa konfigurationen** menyn öppnas också. Den **återställa konfigurationen** menyn är det första steget för att konfigurera återställningen. Använd den här menyn för att välja var du vill återställa data. Alternativen är:
    - **Alternativ plats**: återställa databasen till en annan plats och behålla den ursprungliga källdatabasen.
    - **Åsidosätt databas**: återställa data till samma SQL Server-instans som den ursprungliga källan. Effekten av det här alternativet är att skriva över den ursprungliga databasen.

    > [!Important]
    > Om den valda databasen tillhör en Always On-tillgänglighetsgrupp, tillåter inte SQL Server att databasen kan skrivas över. I det här fallet bara den **alternativ plats** är aktiverat.
    >

    ![Återställ konfigurationen menyn](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Återställ till en alternativ plats

Den här proceduren beskriver hur du återställer data till en annan plats. Om du vill skriva över databasen under återställningen fortsätter att [återställa och skriva över databasen](backup-azure-sql-database.md#restore-and-overwrite-the-database). I det här skedet Recovery Services-valvet är öppen och **återställa konfigurationen** menyn är synlig. Om du inte är i det här skedet, börjar du med [återställer en SQL-databas](backup-azure-sql-database.md#restore-a-sql-database).

> [!NOTE]
> Du kan återställa databasen till en instans av en SQL-Server i samma Azure-region. Målservern måste vara registrerad för Recovery Services-valvet. 
>

På den **återställa konfigurationen** menyn den **Server** nedrullningsbara listrutan visas endast de SQL Server-instanser som har registrerats med Recovery Services-valvet. Om den server som du vill inte finns i listan, se [identifiera SQL Server-databaser](backup-azure-sql-database.md#discover-sql-server-databases) att hitta servern. Under lagringsidentifieringen kan är nya servrar registrerade på Recovery Services-valvet.

1. I den **återställa konfigurationen** menyn:

    * Under **var du vill återställa**väljer **alternativ plats**.
    * Öppna den **Server** listrutan och väljer SQL Server-instansen för att återställa databasen.
    * Öppna den **instans** listrutan och väljer en SQL Server-instans.
    * I den **återställt databasnamn** anger du namnet på måldatabasen.
    * Beroende, Välj **Åsidosätt om databasen med samma namn finns redan på den valda SQL-instansen**.
    * Välj **OK** att slutföra konfigurationen av målservern och fortsätt med att välja en återställningspunkt.

    ![Ange värden för menyn Återställ konfiguration](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. På den **: Välj återställningspunkt** menyn, Välj **loggar (tidpunkt)** eller **fullständig och differentiell** som återställningspunkt. Om du vill återställa till en specifik point-in-time-logg, fortsätter du med det här steget. Om du vill återställa en återställningspunkt med fullständiga och differentiella, fortsätter du till steg 3.

    ![Välj Återställ point-menyn](./media/backup-azure-sql-database/recovery-point-menu.png)

    Point-in-time-återställning finns tillgängligt endast för säkerhetskopieringar för databaser med en fullständiga och massloggade återställningsmodellen. Att återställa till en viss tidpunkt:

    1. Välj **loggar (tidpunkt)** som typ av återställning.

        ![Välj typ av återställning](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Under **återställa datum/tid**, Välj mini kalender för att öppna den **kalender**. På den **kalender**, datumen i fetstil har återställningspunkter och det aktuella datumet är markerat. Välj ett datum med återställningspunkter. Datum utan återställningspunkter kan inte väljas.

        ![Öppna kalendern](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        När du har valt ett datum visar i tidslinjen diagrammet tillgängliga återställningspunkter intill varandra.

    3. Använd tidslinje graph eller **tid** om du vill ange en viss tidpunkt för återställningspunkten. Välj **OK** att slutföra återställningspunkt steget.
    
       ![Öppna kalendern](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        Den **: Välj återställningspunkt** menyn har stängts och **Advanced Configuration** menyn öppnas.

       ![menyn för avancerad konfiguration](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. På den **Advanced Configuration** menyn:

        * Om du vill behålla databasen inte kan användas efter återställningen, ange **Återställ med NORECOVERY** till **aktiverad**.
        * Om du vill ändra plats för databasfilåterställning på målservern, ange en ny sökväg i den **Target** kolumn.
        * Välj **OK** godkänna inställningarna. Stäng den **Advanced Configuration** menyn.

    5. På den **återställa** menyn och välj **återställa** att starta återställningsjobbet. Spåra förloppet för återställning i den **meddelanden** området eller välj **återställningsjobb** på databas-menyn.

       ![Förlopp för återställningsjobb](./media/backup-azure-sql-database/restore-job-notification.png)

3. På den **: Välj återställningspunkt** menyn, Välj **loggar (tidpunkt)** eller **fullständig och differentiell** som återställningspunkt. Gå tillbaka till steg 2 om du vill återställa en point-in-time-logg. Det här steget återställs en specifik fullständig eller differentiell återställningspunkt. Du kan se alla fullständiga och differentiella återställningspunkterna för de senaste 30 dagarna. Om du vill se återställningspunkter som är äldre än 30 dagar, **Filter** att öppna den **Filter återställningspunkter** menyn. Azure Backup för en differentiell återställningspunkt först att återställa den lämpliga fullständig återställningspunkten och tillämpar sedan den valda differentiella återställningspunkten.

    ![Välj Återställ point-menyn](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. I den **: Välj återställningspunkt** menyn, Välj **fullständig och differentiell**.

       ![Välj fullständig och differentiell](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        På menyn visas listan över tillgängliga återställningspunkter.

    2. Välj en återställningspunkt i listan och välj **OK** Slutför återställningspunkt proceduren. 

        ![Välj en fullständig återställningspunkt](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        Den **återställningspunkt** menyn har stängts och **Advanced Configuration** menyn öppnas.

        ![Avancerad konfiguration meny](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. På den **Advanced Configuration** menyn:

        * Om du vill behålla databasen inte kan användas efter återställningen, ange **Återställ med NORECOVERY** till **aktiverad**. **Återställ med NORECOVERY** är inaktiverad som standard.
        * Om du vill ändra plats för databasfilåterställning på målservern, ange en ny sökväg i den **Target** kolumn.
        * Välj **OK** godkänna inställningarna. Stäng den **Advanced Configuration** menyn.

    4. På den **återställa** menyn och välj **återställa** att starta återställningsjobbet. Spåra förloppet för återställning i den **meddelanden** området eller välj **återställningsjobb** på databas-menyn.

       ![Förlopp för återställningsjobb](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Återställa och skriva över databasen

Den här proceduren vägleder dig genom återställning av data och skriver över en databas. Om du vill återställa till en annan plats, även i fortsättningen [återställa till en alternativ plats](backup-azure-sql-database.md#restore-to-an-alternate-location). I det här skedet Recovery Services-valvet är öppen och **återställa konfigurationen** menyn är synliga (se följande bild). Om du inte är i det här skedet, börjar du med [återställer en SQL-databas](backup-azure-sql-database.md#restore-a-sql-database).

![Återställ konfigurationen menyn](./media/backup-azure-sql-database/restore-overwrite-db.png)

På den **återställa konfigurationen** menyn den **Server** nedrullningsbara listrutan visas endast de SQL Server-instanser som har registrerats med Recovery Services-valvet. Om den server som du vill inte finns i listan, se [identifiera SQL Server-databaser](backup-azure-sql-database.md#discover-sql-server-databases) att hitta servern. Under lagringsidentifieringen kan är nya servrar registrerade på Recovery Services-valvet.

1. I den **återställa konfigurationen** menyn och välj **skriva över DB**, och välj sedan **OK** att slutföra konfigurationen av målservern. 

   ![Välj Åsidosätt databas](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    Den **Server**, **instans**, och **återställt databasnamn** inställningarna är inte nödvändigt.

2. På den **: Välj återställningspunkt** menyn, Välj **loggar (tidpunkt)** eller **fullständig och differentiell** som återställningspunkt. Om du vill återställa till en specifik point-in-time-logg, fortsätter du med det här steget. Om du vill återställa en återställningspunkt med fullständiga och differentiella, fortsätter du till steg 3.

    ![Välj Återställ point-menyn](./media/backup-azure-sql-database/recovery-point-menu.png)

    Point-in-time-återställning finns tillgängligt endast för säkerhetskopieringar för databaser med en fullständiga och massloggade återställningsmodellen. Så här återställer till en specifik andra:

    1. Välj **loggar (tidpunkt)** som återställningspunkt.

        ![Välj typ av återställning](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Under **återställa datum/tid**, Välj mini kalender för att öppna den **kalender**. På den **kalender**, datumen i fetstil har återställningspunkter och det aktuella datumet är markerat. Välj ett datum med återställningspunkter. Datum utan återställningspunkter kan inte väljas.

        ![Öppna kalendern](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        När du har valt ett datum visas i tidslinjen diagrammet tillgängliga återställningspunkter.

    3. Använd tidslinje graph eller **tid** om du vill ange en viss tidpunkt för återställningspunkten. Välj **OK** att slutföra återställningspunkt steget.
    
       ![Öppna kalendern](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        Den **: Välj återställningspunkt** menyn har stängts och **Advanced Configuration** menyn öppnas.

       ![menyn för avancerad konfiguration](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. På den **Advanced Configuration** menyn:

        * Om du vill behålla databasen inte kan användas efter återställningen, ange **Återställ med NORECOVERY** till **aktiverad**.
        * Om du vill ändra plats för databasfilåterställning på målservern, ange en ny sökväg i den **Target** kolumn.
        * Välj **OK** godkänna inställningarna. Stäng den **Advanced Configuration** menyn.

    5. På den **återställa** menyn och välj **återställa** att starta återställningsjobbet. Spåra förloppet för återställning i den **meddelanden** området eller välj **återställningsjobb** på databas-menyn.

       ![Förlopp för återställningsjobb](./media/backup-azure-sql-database/restore-job-notification.png)

3. På den **: Välj återställningspunkt** menyn, Välj **loggar (tidpunkt)** eller **fullständig och differentiell** som återställningspunkt. Gå tillbaka till steg 2 om du vill återställa en point-in-time-logg. Det här steget återställs en specifik fullständig eller differentiell återställningspunkt. Du kan se alla fullständiga och differentiella återställningspunkterna för de senaste 30 dagarna. Om du vill se återställningspunkter som är äldre än 30 dagar, **Filter** att öppna den **Filter återställningspunkter** menyn. Azure Backup för en differentiell återställningspunkt först att återställa den lämpliga fullständig återställningspunkten och tillämpar sedan den valda differentiella återställningspunkten.

    ![Välj Återställ point-menyn](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. I den **: Välj återställningspunkt** menyn, Välj **fullständig och differentiell**.

       ![Välj fullständig och differentiell](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        På menyn visas listan över tillgängliga återställningspunkter.

    2. Välj en återställningspunkt i listan och välj **OK** Slutför återställningspunkt proceduren. 

        ![Välj en fullständig återställningspunkt](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        Den **återställningspunkt** menyn har stängts och **Advanced Configuration** menyn öppnas.

        ![Avancerad konfiguration meny](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. På den **Advanced Configuration** menyn:

        * Om du vill behålla databasen inte kan användas efter återställningen, ange **Återställ med NORECOVERY** till **aktiverad**. **Återställ med NORECOVERY** är inaktiverad som standard.
        * Om du vill ändra plats för databasfilåterställning på målservern, ange en ny sökväg i den **Target** kolumn.
        * Välj **OK** godkänna inställningarna. Stäng den **Advanced Configuration** menyn.

    4. På den **återställa** menyn och välj **återställa** att starta återställningsjobbet. Spåra förloppet för återställning i den **meddelanden** området eller genom att välja **återställningsjobb** på databas-menyn.

       ![Förlopp för återställningsjobb](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Hantera Azure Backup-åtgärder för SQL på Azure Virtual Machines

Det här avsnittet innehåller information om de olika Azure Backup-hanteringsåtgärder som är tillgängliga för SQL på Azure virtual machines. Följande avancerade åtgärder finns:

* Övervaka jobb
* Säkerhetskopieringsaviseringar
* Stoppa skydd på en SQL-databas
* Återuppta skyddet av en SQL-databas
* Utlös en ad hoc-säkerhetskopiering
* Avregistrera en server som kör SQL Server

### <a name="monitor-backup-jobs"></a>Övervaka säkerhetskopieringsjobb
Azure Backup är en lösning för klassen som tillhandahåller avancerade aviseringar om säkerhetskopiering och meddelanden om eventuella fel. (Se [Visa aviseringar om säkerhetskopiering](backup-azure-sql-database.md#view-backup-alerts).) För att övervaka specifika jobb, använder du någon av följande alternativ baserat på dina krav.

#### <a name="use-the-azure-portal-for-adhoc-operations"></a>Använda Azure-portalen för ad hoc-åtgärder
Azure Backup visar alla manuellt utlösta eller ad hoc,-jobb i den **säkerhetskopieringsjobb** portal. De jobb som är tillgängliga i den **säkerhetskopieringsjobb** portal omfattar:
- Alla konfigurera säkerhetskopieringsåtgärder.
- Utlöses manuellt säkerhetskopieringsåtgärder.
- Återställning.
- Registrering och identifiera databasåtgärder.
- Stoppa säkerhetskopiering. 

![Säkerhetskopieringsjobb portal](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Alla schemalagda säkerhetskopieringsjobb, inklusive fullständig, differentiell och säkerhetskopiering av loggen, visas inte i den **säkerhetskopieringsjobb** portal. Använda SQL Server Management Studio för att övervaka schemalagda säkerhetskopieringsjobb, enligt beskrivningen i nästa avsnitt.
>

#### <a name="use-sql-server-management-studio-for-backup-jobs"></a>Använd SQL Server Management Studio för säkerhetskopieringsjobb
Azure Backup använder SQL interna API: er för alla säkerhetskopieringsåtgärder. Använda interna API: er för att hämta alla jobbinformation från den [SQL-tabell för säkerhetskopian](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) i msdb-databasen.

I följande exempel är en fråga som hämtar alla säkerhetskopieringsjobb för en databas med namnet **DB1**. Anpassa frågan för avancerad övervakning.

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  
 
```

### <a name="view-backup-alerts"></a>Visa aviseringar om säkerhetskopiering

Eftersom loggsäkerhetskopior sker varje kvart, ibland kan övervaka säkerhetskopieringsjobb kan vara tråkigt. Azure Backup innehåller hjälp i det här fallet. E-postaviseringar initieras för alla Säkerhetskopieringsfel. Aviseringar konsolideras på databasnivå med hjälp av felkoder. En e-postavisering skickas endast för det första säkerhetskopieringen har misslyckandet för en databas. Logga in på Azure-portalen att övervaka alla fel för en databas. 

Övervaka aviseringar om säkerhetskopiering:

1. Logga in på Azure-prenumerationen i den [Azure-portalen](https://portal.azure.com).

2. Öppna Recovery Services-valvet som har registrerats hos SQL-dator.

3. På den **Recovery Services-valv** instrumentpanelen, väljer **aviseringar och händelser**. 

   ![Välj aviseringar och händelser](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. På den **aviseringar och händelser** menyn och välj **säkerhetskopiering aviseringar** att visa listan över aviseringar.

   ![Välj Säkerhetskopieringsaviseringar](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-for-a-sql-server-database"></a>Sluta skydda en SQL Server-databas

När du slutar skydda en SQL Server-databas, Azure Backup-begäranden om du vill behålla återställningspunkterna. Det finns två sätt att sluta skydda en SQL-databas:

* Stoppa alla framtida säkerhetskopieringsjobb och ta bort alla återställningspunkter.
* Stoppa alla framtida säkerhetskopieringsjobb, men lämna kvar återställningspunkterna.

Det finns en kostnad för att lämna kvar återställningspunkterna. Återställningspunkter för SQL debiteras för den skyddade SQL-instansen priser kostnad, plus förbrukad lagring. Läs mer om Azure Backup priser för SQL, den [sidan med prissättning för Azure Backup](https://azure.microsoft.com/pricing/details/backup/). 

Sluta skydda en databas:

1. Öppna Recovery Services-valvet som har registrerats hos SQL-dator.

2. På den **Recovery Services-valv** instrumentpanelen under **användning**väljer **Säkerhetskopieringsobjekt** att öppna den **Säkerhetskopieringsobjekt** menyn.

    ![Öppna menyn Säkerhetskopieringsobjekt](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. På den **Säkerhetskopieringsobjekt** menyn under **typ av Säkerhetskopieringshantering**väljer **SQL i Azure VM**. 

    ![Välj SQL i Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    Den **Säkerhetskopieringsobjekt** menyn visar en lista över SQL-databaser. 

4. I listan över SQL-databaser, väljer du databasen för att stoppa skyddet.

    ![Den databas du sluta skydda](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    När du väljer databasen, öppnar menyn.

5. Välj på menyn för den valda databasen **stoppa säkerhetskopiering**. 

    ![Välj Avbryt säkerhetskopiering](./media/backup-azure-sql-database/stop-db-button.png)

    Den **stoppa säkerhetskopiering** menyn öppnas.

6. På den **stoppa säkerhetskopiering** menyn välja att **Behåll säkerhetskopieringsdata** eller **ta bort säkerhetskopieringsdata**. Ange ett skäl för att stoppa skyddet och en kommentar som ett alternativ.

    ![Stoppa Backup-menyn](./media/backup-azure-sql-database/stop-backup-button.png)

7. Välj **stoppa säkerhetskopiering** upphöra med skyddet på databasen. 

### <a name="resume-protection-for-a-sql-database"></a>Återuppta skyddet av en SQL-databas

Om den **Behåll säkerhetskopieringsdata** alternativet valdes när skydd för SQL-databasen har stoppats, du kan återuppta skyddet. Om du säkerhetskopierade data inte behålls kan inte protection återuppta. 

1. Om du vill återuppta skyddet av SQL-databasen, öppnar du säkerhetskopieringsobjektet och väljer **återuppta säkerhetskopiering**.

    ![Välj återuppta säkerhetskopiering till återuppta databasskyddet](./media/backup-azure-sql-database/resume-backup-button.png)

   Den **säkerhetskopieringspolicy** menyn öppnas.

2. På den **säkerhetskopieringspolicy** menyn, Välj en princip och välj sedan **spara**.

### <a name="trigger-an-adhoc-backup"></a>Utlös en ad hoc-säkerhetskopiering

Aktivera ad hoc-säkerhetskopiering efter behov. Det finns fyra typer av ad hoc-säkerhetskopiering:

* Fullständig säkerhetskopiering
* Fullständig säkerhetskopiering med endast kopiering
* Differentiell säkerhetskopia
* Loggsäkerhetskopia

Mer information om varje typ finns [typer av SQL-säkerhetskopior](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

### <a name="unregister-a-sql-server-instance"></a>Avregistrera en SQL Server-instans

Avregistrera en SQL Server-instansen när du ta bort skyddet, men innan du tar bort valvet:

1. Öppna Recovery Services-valvet som har registrerats hos SQL-dator.

2. På den **Recovery Services-valv** instrumentpanelen under **hantera**väljer **infrastruktur för säkerhetskopiering**.  

   ![Välj en infrastruktur för säkerhetskopiering](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. Under **hanteringsservrar**väljer **skyddade servrar**.

   ![Välj skyddade servrar](./media/backup-azure-sql-database/protected-servers.png)

    Den **skyddade servrar** menyn öppnas. 

4. På den **skyddade servrar** menyn väljer du servern du avregistrera. Om du vill ta bort valvet måste du avregistrera alla servrar.

5. På den **skyddade servrar** menyn, högerklicka på den skyddade servern och välj **ta bort**. 

   ![Välj Ta bort](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

Följande avsnitt innehåller ytterligare information om säkerhetskopiering för SQL-databas.

### <a name="can-i-throttle-the-speed-of-the-sql-server-backup-policy"></a>Kan jag begränsa hastigheten på SQL Server-principen för säkerhetskopiering?

Ja. Du kan begränsa den hastighet som principen för säkerhetskopiering körs för att minimera effekten på en SQL Server-instans.

Ändra inställningen:

1. Öppna på SQL Server-instansen i mappen c:\Program\Microsoft Files\Azure arbetsbelastning Backup\bin den **TaskThrottlerSettings.json** fil.

2. I filen TaskThrottlerSettings.json ändrar den **DefaultBackupTasksThreshold** till ett lägre värde (till exempel 5).

3. Spara ändringarna. Stäng filen.

4. Öppna på SQL Server-instansen **Aktivitetshanteraren**. Starta om den **tjänsten för Azure Backup-arbetsbelastning Coordinator**.

### <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Kan jag köra en fullständig säkerhetskopiering från en sekundär replik?

Nej. Den här funktionen stöds inte.

### <a name="do-successful-backup-jobs-create-alerts"></a>Skapar lyckad säkerhetskopieringsjobb aviseringar?

Nej. Lyckad säkerhetskopieringsjobb generera inte aviseringar. Aviseringar skickas bara för säkerhetskopieringsjobb som misslyckas.

### <a name="can-i-see-details-for-scheduled-backup-jobs-in-the-jobs-menu"></a>Kan jag se information om schemalagda säkerhetskopieringsjobb på menyn jobb?

Nej. Den **säkerhetskopieringsjobb** menyn visar information om ad hoc, men inte schemalagda säkerhetskopieringsjobb. Om alla schemalagda säkerhetskopieringsjobb misslyckas, finns information i aviseringar för misslyckade jobbet. För att övervaka alla schemalagda och adhoc-säkerhetskopieringsjobb, använda [SQL Server Management Studio](backup-azure-sql-database.md#use-sql-server-management-studio-for-backup-jobs).

### <a name="when-i-select-a-sql-server-instance-are-future-databases-automatically-added"></a>När jag väljer en SQL Server-instansen är framtida databaser som läggs till automatiskt?

Nej. När du konfigurerar skydd för en SQL Server-instans, om du väljer alternativet för server, läggs alla databaser. Om du lägger till databaser till en SQL Server-instansen när du konfigurerar skydd måste du lägga till nya databaser för att skydda dem manuellt. Databaserna är inte inkluderas i det konfigurera skyddet.

### <a name="if-i-change-the-recovery-model-how-do-i-restart-protection"></a>Hur startar jag skydd om jag ändrar återställningsmodellen?

Aktivera en fullständig säkerhetskopiering. Loggsäkerhetskopior börjar som förväntat.

### <a name="can-i-protect-sql-always-on-availability-groups-where-the-primary-replica-is-on-premises"></a>Jag kan skydda SQL Always On-Tillgänglighetsgrupper där den primära repliken är lokalt

Nej. Azure Backup skyddar SQL-servrar som körs i Azure. Om tillgänglighet grupp (AG) fördelas mellan Azure och lokala datorer, kan AG skyddas endast om den primära repliken körs i Azure. Azure Backup skyddar dessutom endast de noder som körs i samma Azure-region som Recovery Services-valvet.

### <a name="can-i-protect-sql-always-on-availability-groups-which-are-spread-across-azure-regions"></a>Jag kan skydda SQL Always On-Tillgänglighetsgrupper som är fördelade på Azure-regioner
Azure Backup Recovery Services-valv kan identifiera och skydda alla noder i samma region som Recovery Services-valvet. Om du har en SQL alltid på tillgänglighetsgrupp över flera Azure-regioner kan behöva du konfigurera säkerhetskopiering från den region som har den primära noden. Azure Backup kommer att kunna identifiera och skydda alla databaser i tillgänglighetsgruppen enligt inställning för säkerhetskopiering. Om inställningen för säkerhetskopiering inte är uppfyllt, misslyckas säkerhetskopieringarna och du får fel aviseringen.


## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Backup i Azure PowerShell-exemplet för att säkerhetskopiera krypterade virtuella datorer.

> [!div class="nextstepaction"]
> [Säkerhetskopiera en krypterad virtuell dator](./scripts/backup-powershell-sample-backup-encrypted-vm.md)

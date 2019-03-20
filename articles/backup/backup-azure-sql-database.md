---
title: Säkerhetskopiera SQL Server-databaser till Azure | Microsoft Docs
description: I den här självstudien beskrivs hur du säkerhetskopierar SQL Server till Azure. Den här artikeln beskriver även återställning av SQL Server.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: b08d4e55deac4c9dd6a11fc9a278bdcfaa739627
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224319"
---
# <a name="back-up-sql-server-databases-on-azure-vms"></a>Säkerhetskopiera SQL Server-databaser på virtuella Azure-datorer 

SQL Server-databaser är kritiska arbetsbelastningar som kräver ett lågt mål för återställningspunkt (RPO) och långsiktig kvarhållning. Du kan säkerhetskopiera SQL Server-databaser som körs på virtuella Azure-datorer med hjälp av [Azure Backup](backup-overview.md). 

Den här självstudien visar hur du säkerhetskopierar en SQL Server-databas som körs på en virtuell Azure-dator till ett Azure Backup Recovery Services-valv. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Kontrollera förutsättningarna för säkerhetskopiering av en SQL Server-instans.
> * Skapa och konfigurera ett valv.
> * Identifiera databaser och konfigurerar säkerhetskopieringar.
> * Konfigurera automatiskt skydd för databaser.

> [!NOTE]
> Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion.

## <a name="before-you-start"></a>Innan du börjar


- Se till att du har en SQL Server-instans som körs i Azure. Du kan snabbt [skapa en SQL Server-instans](../sql-database/sql-database-get-started-portal.md) på Azure Marketplace.
- Granska begränsningarna för offentlig förhandsversion.
- Granska scenariostöd.
- [Granska vanliga frågor](faq-backup-sql-server.md) om det här scenariot.

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

Den här förhandsversionen har ett antal begränsningar.

- Den virtuella datorn som kör SQL Server kräver en Internetanslutning för att komma åt Azure offentliga IP-adresser. 
- Du kan säkerhetskopiera 2 000 SQL Server-databaser i ett valv. Om du har fler kan du skapa ett till valv. 
- Säkerhetskopior av [distribuerade tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) fungerar inte helt och hållet.
- AlwaysOn-redundansklusterinstanser för SQL Server (FCI:er) stöds inte för säkerhetskopiering.
- Konfigurera SQL Server-säkerhetskopiering i portalen. För närvarande kan du inte konfigurera säkerhetskopiering med Azure PowerShell, CLI eller REST-API:er.
- Åtgärder för säkerhetskopiering och återställning för FCI speglade databaser, ögonblicksbilder av databaser och databaser stöds inte.
- Kan inte skydda databaser med ett stort antal filer. Det maximala antalet filer som stöds är inte deterministisk. Det beror på hur många filer och beror också på sökvägslängden på filerna. 

Granska [vanliga frågor och svar](faq-backup-sql-server.md) om säkerhetskopiering av SQL Server-databaser.
## <a name="scenario-support"></a>Scenariostöd

**Support** | **Detaljer**
--- | ---
**Distributioner som stöds** | Azure virtuella datorer på Marketplace och icke-Marketplace (SQL Server manuellt installerade) virtuella datorer stöds.
**Geografiska områden som stöds** | Australien, sydöstra (ASE); Brasilien, södra (BRS); Kanada, centrala (CNC); Kanada, östra (CE); USA, centrala (CUS); Asien, östra (EA); Australien, östra (AE); USA, östra (EUS); USA, östra 2 (EUS2); Indien, centrala (INC); Indien, södra (INS); Japan, östra (JPE); Japan, västra (JPW); Korea, centrala (KRC); Korea, södra (KRS); USA, norra centrala (NCUS); Europa, norra (NE); USA, södra centrala (SCUS); Asien, sydöstra (SEA) Storbritannien, södra (UKS); Storbritannien, västra (UKW); USA, västra centrala (WCUS); Europa, västra (WE); USA, västra (WUS); USA, västra 2 (WUS 2)
**Operativsystem som stöds** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux stöds inte för närvarande.
**SQL Server-versioner som stöds** | SQL Server 2017, SQL Server 2016, SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.

## <a name="prerequisites"></a>Förutsättningar

Kontrollera följande villkor innan du säkerhetskopierar SQL Server-databasen:

- Identifiera eller [skapar ett Recovery Services-valv](backup-azure-sql-database.md#create-a-recovery-services-vault) i samma region eller språk som den virtuella datorn som är värd för SQL Server-instansen.
- [Kontrollera behörigheter för virtuella datorer](#fix-sql-sysadmin-permissions) som behövs för att säkerhetskopiera SQL-databaser.
- Kontrollera att den virtuella datorn har [nätverksanslutningar](backup-azure-sql-database.md#establish-network-connectivity).
- Kontrollera att SQL Server-databaser namnges i enlighet med den [riktlinjerna för namngivning för Azure Backup](backup-azure-sql-database.md).
- Kontrollera att du inte har några andra lösningar för säkerhetskopiering aktiverade för databasen. Inaktivera alla andra SQL Server-säkerhetskopieringar innan du installerar det här scenariot. Du kan aktivera Azure Backup för en Azure-dator, tillsammans med Azure Backup för SQL Server-databas som körs på den virtuella datorn, utan någon konflikt.

### <a name="establish-network-connectivity"></a>Etablera nätverksanslutning

Den virtuella SQL Server-datorn behöver ha anslutning till offentliga Azure-IP-adresser för alla åtgärder. VM-åtgärder (databasen identifiering, säkerhetskopieringar, schemalägga säkerhetskopieringar, återställa återställningspunkter) misslyckas utan anslutning till offentliga IP-adresser. Upprätta en anslutning med något av följande alternativ:

- **Tillåt IP-intervallen för Azure-datacenter**: Tillåt [IP-intervallen](https://www.microsoft.com/download/details.aspx?id=41653) i nedladdningen. Åtkomst till i en nätverkssäkerhetsgrupp (NSG), använder den `Set-AzureNetworkSecurityRule` cmdlet.
- **Distribuera en HTTP-proxyserver för att dirigera trafik**: När du säkerhetskopierar en SQL Server-databas på en Azure virtuell dator använder HTTPS-API: er i tillägget på den virtuella datorn att skicka kommandon för hantering av till Azure Backup och för att skicka data till Azure Storage. Säkerhetskopieringstillägget använder också Azure Active Directory (Azure AD) för autentisering. Dirigera trafiken för säkerhetskopieringstillägget för dessa tre tjänster via HTTP-proxyn. Tillägget är den enda komponenten som är konfigurerad för åtkomst till det offentliga internet.

Varje alternativ har fördelar och nackdelar.

**Alternativ** | **Fördelar** | **Nackdelar**
--- | --- | ---
Tillåta IP-intervall | Inga ytterligare kostnader. | Komplicerat att hantera eftersom IP-adressintervallen ändras över tid. <br/><br/> Ger åtkomst till hela Azure, inte bara till Azure Storage.
Använda en HTTP-proxy   | Detaljerad kontroll i proxyn över lagrings-URL:er tillåts. <br/><br/> Enskild punkt för Internetåtkomst till virtuella datorer. <br/><br/> Inte föremål för Azure-IP-adressändringar. | Ytterligare kostnader för att köra en virtuell dator med proxyprogramvaran. 

### <a name="set-vm-permissions"></a>Ange VM-behörigheter

Azure Backup gör flera saker när du konfigurerar säkerhetskopiering för en SQL Server-databas:

- Azure Backup lägger till den **AzureBackupWindowsWorkload** tillägget.
- Azure Backup skapas kontot **NT SERVICE\AzureWLBackupPluginSvc** att identifiera databaser på den virtuella datorn. Det här kontot används för säkerhetskopiering och återställning och kräver SQL sysadmin-behörighet.
- Azure Backup använder den **NT AUTHORITY\SYSTEM** konto för databasidentifiering av och förfrågan. Det här kontot måste vara en offentlig inloggning på SQL Server.

Om du inte skapade SQL Server-dator från Azure Marketplace, du kan få en **UserErrorSQLNoSysadminMembership** fel. Om det här felet uppstår [följer du dessa instruktioner](#fix-sql-sysadmin-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Kontrollera riktlinjerna för namngivning av databaser för Azure Backup

Undvik följande för databasnamn:

* Avslutande/inledande blanksteg
* Avslutande utropstecken (!)
* Avslutande hakparentes (])

Vi har alias för Azure-tabell som inte stöds tecken, men vi rekommenderar att du undviker dem. [Läs mer](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Identifiera SQL Server-databaser

Identifiera databaser som körs på den virtuella datorn. 

1. I den [Azure-portalen](https://portal.azure.com), öppna Recovery Services-valvet som används för att säkerhetskopiera databasen.

1. På instrumentpanelen för **Recovery Services-valvet** väljer du **Säkerhetskopiering**. 

   ![Välj Säkerhetskopiering för att öppna menyn Säkerhetskopieringsmål](./media/backup-azure-sql-database/open-backup-menu.png)

1. I **Säkerhetskopieringsmål** anger du **Var körs din arbetsbelastning** till **Azure** (standardinställningen).

1. I **Vad vill du säkerhetskopiera** väljer du **SQL Server på Azure VM**.

    ![Välj SQL Server på Azure VM för säkerhetskopieringen](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)


1. Under **Säkerhetskopieringsmål** > **Identifiera databaser på virtuella datorer** väljer du **Start Discovery** (Starta identifiering) för att söka efter oskyddade virtuella datorer i prenumerationen. Det kan ta ett tag beroende på antalet oskyddade virtuella datorer i prenumerationen.

    ![Säkerhetskopieringen väntar under sökningen efter databaser på virtuella datorer](./media/backup-azure-sql-database/discovering-sql-databases.png)

1. I listan över virtuella datorer väljer du den virtuella datorn som kör SQL Server-databasen > **identifiera databaser**.

1. Spåra databasidentifiering i området **Meddelanden**. Det kan ta en stund innan jobbet är slutfört, beroende på hur många databaser som finns på den virtuella datorn. När de valda databaserna identifieras visas ett meddelande.

    ![Meddelande som anger att distributionen lyckades](./media/backup-azure-sql-database/notifications-db-discovered.png)

    - Oskyddade virtuella datorer bör visas i listan efter identifiering, sorterade efter namn och resursgrupp.
    - Om en virtuell dator inte visas som förväntat kontrollerar du om den redan har säkerhetskopierats i ett valv.
    - Flera virtuella datorer kan ha samma namn, men de kommer tillhör olika resursgrupper. 

1. Välj den virtuella datorn som kör SQL Server-databasen > **identifiera databaser**. 
1. Azure Backup identifierar alla SQL Server-databaser på den virtuella datorn. Följande aktivitet sker i bakgrunden under identifieringen:

    - Azure Backup registreras den virtuella datorn med valvet för säkerhetskopiering av arbetsbelastning. Alla databaser på den registrerade virtuella datorn kan säkerhetskopieras bara för det här valvet.
    - Azure Backup installerar tillägget **AzureBackupWindowsWorkload** på den virtuella datorn. Det installeras inte någon agent på SQL-databasen.
    - Azure Backup skapar tjänstkontot **NT Service\AzureWLBackupPluginSvc** på den virtuella datorn.
      - Alla åtgärder för säkerhetskopiering och återställning använder tjänstkontot.
      - **NT Service\AzureWLBackupPluginSvc** behöver SQL-sysadmin-behörigheter. Alla SQL Server-datorer som har skapats i Azure Marketplace som medföljer den **SqlIaaSExtension** installerad. Tillägget **AzureBackupWindowsWorkload** använder **SQLIaaSExtension** för att automatiskt hämta de behörigheter som krävs.
    - Om du inte har skapat den virtuella datorn från Marketplace, den virtuella datorn inte har den **SqlIaaSExtension** installerat, och Identifieringsåtgärden misslyckas med den **UserErrorSQLNoSysAdminMembership** felmeddelande. Följ instruktionerna i [#fix-sql-sysadmin-permissions] för att åtgärda det här problemet.

        ![Välj den virtuella datorn och databasen](./media/backup-azure-sql-database/registration-errors.png)



## <a name="configure-a-backup-policy"></a>Konfigurera en säkerhetskopieringspolicy 

För att optimera säkerhetskopieringsbelastningar anger Azure Backup ett maximalt antal databaser i ett säkerhetskopieringsjobb till 50.

- Om du vill skydda fler än 50 databaser konfigurerar du flera säkerhetskopieringar.
- Alternativt kan du aktivera automatiskt skydd. Automatiskt skydd skyddar befintliga databaser i en går och skyddar automatiskt nya databaser som läggs till instansen av tillgänglighetsgruppen.


Konfigurera säkerhetskopiering på följande sätt:

1. På valvets instrumentpanel väljer du **Säkerhetskopiering**. 

   ![Välj Säkerhetskopiering för att öppna menyn Säkerhetskopieringsmål](./media/backup-azure-sql-database/open-backup-menu.png)

1. I **säkerhetskopieringsmål**anger **var körs din arbetsbelastning?** till **Azure**.

1. I **vad vill du säkerhetskopiera?** väljer **SQL Server i Azure VM**.

    ![Välj SQL Server på Azure VM för säkerhetskopieringen](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    
1. I **säkerhetskopieringsmål**väljer **Konfigurera säkerhetskopiering**.

    ![Välj Konfigurera säkerhetskopiering](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    ![Visa alla SQL Server-instanser med fristående databaser](./media/backup-azure-sql-database/list-of-sql-databases.png)

1. Välj alla databaser som du vill skydda > **OK**.

    ![Skydda databasen](./media/backup-azure-sql-database/select-database-to-protect.png)

    - Alla SQL Server-instanser visas (fristående och tillgänglighetsgrupper).
    - Välj nedåtpilen till vänster om instansgrupp för namn eller tillgänglighet att filtrera.

1. I den **Backup** menyn och välj **säkerhetskopieringspolicy**. 

    ![Välja säkerhetskopieringspolicy](./media/backup-azure-sql-database/select-backup-policy.png)

1. I **Välj säkerhetskopieringspolicy**, Välj en princip och välj sedan **OK**.

    - Välja standardpolicy: **HourlyLogBackup**.
    - Välj en befintlig säkerhetskopieringspolicy som tidigare har skapats för SQL.
    - [Definiera en ny policy](backup-azure-sql-database.md#configure-a-backup-policy) baserat på ditt RPO och kvarhållningsintervall.
    - Under förhandsversionen kan du inte redigera en befintlig säkerhetskopieringspolicy.
    
1. I den **säkerhetskopiering** menyn och välj **Aktivera säkerhetskopiering**.

    ![Aktivera den valda säkerhetskopieringspolicyn](./media/backup-azure-sql-database/enable-backup-button.png)

1. Spåra konfigurationsförloppet i **meddelandefältet** på portalen.

    ![Meddelandefältet](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy

En princip för säkerhetskopiering avgör när säkerhetskopior tas och hur länge de är kvar. 

- En policy skapas på valvnivå.
- Flera valv kan använda samma säkerhetskopieringspolicy, men du måste tillämpa säkerhetskopieringspolicyn på varje valv.
- När du skapar en säkerhetskopieringspolicy används en daglig fullständig säkerhetskopia som standard.
- Du kan lägga till en differentiell säkerhetskopia, men endast om du konfigurerar så att fullständiga säkerhetskopior utförs varje vecka.
- [Lär dig mer om](backup-architecture.md#sql-server-backup-types) olika typer av säkerhetskopieringspolicyer.

Följ dessa steg om du vill skapa en princip för säkerhetskopiering:

1. I valvet, väljer **Säkerhetskopieringsprinciper** > **Lägg till**.
1. I den **Lägg till** menyn och välj **SQL Server i Azure VM**. Detta gör så att policytypen definieras.

   ![Välj en policytyp för den nya säkerhetskopieringspolicyn](./media/backup-azure-sql-database/policy-type-details.png)

1. I **Policynamn** anger du ett namn för den nya policyn. 
1. I **princip för fullständig säkerhetskopiering**väljer en **säkerhetskopieringsfrekvens**. Välj **dagliga** eller **veckovisa**.

    - För **dagliga**, väljer en timme och tidszonen för säkerhetskopiering ska börja.
    - Du måste köra en fullständig säkerhetskopia. Du kan inte inaktivera den **fullständig säkerhetskopiering** alternativet.
    - Välj **fullständig säkerhetskopiering** att visa principen. 
    - Du kan inte skapa differentiella säkerhetskopior för dagliga fullständiga säkerhetskopior.
    - För **veckovisa**, välja dagen i veckan, timme och tidszonen för att säkerhetskopieringen påbörjas.

     ![Nya fält för säkerhetskopieringspolicy](./media/backup-azure-sql-database/full-backup-policy.png)  

1. I **Kvarhållningsintervall**, alla alternativ som är markerade som standard. Avmarkera eventuella gränser för oönskade kvarhållning intervall som du inte vill använda och ange de intervall som du vill använda. 

    - Återställningspunkter taggas för kvarhållning baserat på deras kvarhållningsintervall. Om du till exempel väljer en daglig fullständig säkerhetskopia utlöses endast en fullständig säkerhetskopia varje dag.
    - Säkerhetskopieringen för en viss dag taggas och behålls baserat på det veckovisa kvarhållningsintervallet och inställningen för veckovis kvarhållning.
    - De månatliga och årliga kvarhållningsintervallen fungerar på liknande sätt.

   ![Inställningar för kvarhållningsintervall](./media/backup-azure-sql-database/retention-range-interval.png)

    

1. På menyn **Policy för fullständig säkerhetskopia** väljer du **OK** för att acceptera inställningarna.
1. Om du vill lägga till en policy för differentiell säkerhetskopia väljer du **Differentiell säkerhetskopia**. 

   ![Inställningar för kvarhållningsintervall](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Öppna menyn för policy för differentiell säkerhetskopia](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

1. I **Policy för differentiell säkerhetskopia** väljer du **Aktivera** för att öppna kontrollerna för frekvens och kvarhållning. 

    - Du kan endast utlösa en differentiell säkerhetskopia per dag.
    - Differentiella säkerhetskopior kan behållas i upp till 180 dagar. Om du behöver längre kvarhållning måste du använda fullständiga säkerhetskopior.

1. Välj **OK** för att spara policyn och återgå till huvudmenyn **Säkerhetskopieringspolicy**.

1. Om du vill lägga till en policy för loggsäkerhetskopia väljer du **Loggsäkerhetskopia**.
1. I **Loggsäkerhetskopia** väljer du **Aktivera** och anger kontrollerna för frekvens och kvarhållning. Loggsäkerhetskopior kan ske så ofta som var 15: e minut och kan behållas i upp till 35 dagar.
1. Välj **OK** för att spara policyn och återgå till huvudmenyn **Säkerhetskopieringspolicy**.

    ![Redigera policyn för loggsäkerhetskopia](./media/backup-azure-sql-database/log-backup-policy-editor.png)

1. I den **säkerhetskopieringspolicy** menyn, Välj om du vill aktivera **komprimering av SQL-säkerhetskopiering**.
    - Komprimering är inaktiverat som standard.
    - På serverdelen använder Azure Backup SQL-specifik säkerhetskopieringskomprimering.

1. När du har slutfört redigeringarna i säkerhetskopieringspolicyn väljer du **OK**.



## <a name="enable-auto-protection"></a>Aktivera automatiskt skydd  

Aktivera automatiskt skydd för automatisk säkerhetskopiering av alla befintliga databaser och databaser som har lagts till i framtiden, till en fristående SQL Server-instans eller till en SQL Server Always On-tillgänglighetsgrupp. 

- När du aktiverar automatiskt skydd och väljer en princip, fortsätter de befintliga skyddade databaserna att använda den tidigare principen.
- Det finns ingen gräns för antalet databaser som du kan välja för automatiskt skydd.

Följ dessa steg om du vill aktivera automatiskt skydd:

1. I **Items to backup** (Objekt som ska säkerhetskopieras) väljer du den instans som du vill aktivera automatiskt skydd för.
1. Välj listrutan under **Autoprotect** (Automatiskt skydd) och välj **På**. Välj sedan **OK**.

    ![Aktivera automatiskt skydd i AlwaysOn-tillgänglighetsgruppen](./media/backup-azure-sql-database/enable-auto-protection.png)

1. Säkerhetskopiering konfigureras för alla databaser tillsammans och kan spåras i **Säkerhetskopieringsjobb**.


Om du vill inaktivera det automatiska skyddet markerar du namnet under **Konfigurera säkerhetskopiering**, och välj **inaktivera Autoprotect** för instansen. Alla databaser fortsätter att säkerhetskopiera. Dock skyddas framtida databaser inte automatiskt.

![Inaktivera automatiskt skydd i den instansen](./media/backup-azure-sql-database/disable-auto-protection.png)


## <a name="fix-sql-sysadmin-permissions"></a>Åtgärda SQL-sysadmin-behörigheter

Om du vill åtgärda behörigheterna grund av en **UserErrorSQLNoSysadminMembership** fel, Följ dessa steg:

1. Använd ett konto med SQL Server-sysadmin-behörighet för att logga in till SQL Server Management Studio (SSMS). Såvida du inte behöver specialbehörigheter bör Windows-autentisering fungera.
1. Öppna på SQL Server-instansen i **Security/inloggningar** mapp.

    ![Öppna mappen Security/inloggningar för att se konton](./media/backup-azure-sql-database/security-login-list.png)

1. Högerklicka på mappen **Logins** och välj **Ny inloggning**. I **Login - New** (Inloggning – ny) väljer du **Sök**.

    ![I dialogrutan Inloggning – ny väljer du Sök](./media/backup-azure-sql-database/new-login-search.png)

1. Kontot för virtuell Windows-tjänst, **NT SERVICE\AzureWLBackupPluginSvc**, skapades under registreringen av den virtuell datorn och SQL-identifieringsfasen. Ange kontonamnet såsom det visas i **Ange ett objektnamn du vill markera**. Välj **Kontrollera namn** för att matcha namnet. Välj **OK**.

    ![Välj Kontrollera namn för att matcha det okända tjänstnamnet](./media/backup-azure-sql-database/check-name.png)


1. I **Serverroller** kontrollerar du att serverrollen **sysadmin** har valts. Välj **OK**. De behörigheter som krävs bör nu finnas.

    ![Kontrollera att serverrollen sysadmin har valts](./media/backup-azure-sql-database/sysadmin-server-role.png)

1. Koppla databasen till Recovery Services-valvet. I Azure-portalen går du till listan **Skyddade servrar**, högerklickar på den server som är i ett felaktigt tillstånd > **Identifiera databaser på nytt**.

    ![Kontrollera att servern har rätt behörigheter](./media/backup-azure-sql-database/check-erroneous-server.png)

1. Kontrollera förloppet i området **Meddelanden**. När de valda databaserna hittas visas ett meddelande.

    ![Meddelande som anger att distributionen lyckades](./media/backup-azure-sql-database/notifications-db-discovered.png)

Du kan också aktivera [automatiskt skydd](backup-azure-sql-database.md#enable-auto-protection) på hela instans eller AlwaysOn-tillgänglighetsgrupp. Aktivera det genom att välja den **på** alternativet i motsvarande listrutan i den **AUTOPROTECT** kolumn. Den [automatiskt skydd](backup-azure-sql-database.md#enable-auto-protection) funktionen gör det möjligt för skydd på alla befintliga databaser. Det skyddar också automatiskt nya databaser som ska läggas till den instansen eller tillgänglighetsgruppen i framtiden.  

   ![Aktivera automatiskt skydd i AlwaysOn-tillgänglighetsgruppen](./media/backup-azure-sql-database/enable-auto-protection.png)

## <a name="next-steps"></a>Nästa steg

- [Lär dig om att](restore-sql-database-azure-vm.md) återställa säkerhetskopierade SQL Server-databaser.
- [Lär dig om att](manage-monitor-sql-database-backup.md) hantera säkerhetskopierade SQL Server-databaser.


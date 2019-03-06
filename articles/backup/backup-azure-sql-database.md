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
ms.openlocfilehash: 61219fc4e1fc329708a7e58ee6a293e4e25cca31
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887819"
---
# <a name="back-up-sql-server-databases-on-azure-vms"></a>Säkerhetskopiera SQL Server-databaser på virtuella Azure-datorer 

SQL Server-databaser är kritiska arbetsbelastningar som kräver ett lågt mål för återställningspunkt (RPO) och långsiktig kvarhållning. Du kan säkerhetskopiera SQL Server-databaser som körs på virtuella Azure-datorer med hjälp av [Azure Backup](backup-overview.md). 

Den här artikeln visar hur du säkerhetskopierar en SQL Server-databas som körs på en Azure virtuell dator till ett Azure Backup Recovery Services-valv. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Kontrollera förutsättningarna för säkerhetskopiering av en SQL Server-instans.
> * Skapa och konfigurera ett valv.
> * Identifiera databaser och konfigurera säkerhetskopieringar.
> * Konfigurera automatiskt skydd för databaser.

> [!NOTE]
> Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion.

## <a name="before-you-start"></a>Innan du börjar

Innan du börjar bör du kontrollera följande:

1. Se till att du har en SQL Server-instans som körs i Azure. Du kan [snabbt skapa en SQL Server-instans](../sql-database/sql-database-get-started-portal.md) på Marketplace.
2. Granska begränsningarna för den offentliga förhandsversionen nedan.
3. Granska scenariostöd.
4. [Granska vanliga frågor](faq-backup-sql-server.md) om det här scenariot.


## <a name="preview-limitations"></a>Begränsningar för förhandsversion

Den här förhandsversionen har ett antal begränsningar.

- Den virtuella dator som kör SQL Server kräver en Internetanslutning för åtkomst till offentliga Azure-IP-adresser. 
- Du kan säkerhetskopiera upp till 2 000 SQL Server-databaser i ett valv. Om du har fler kan du skapa ett till valv. 
- Säkerhetskopior av [distribuerade tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) fungerar inte helt och hållet.
- AlwaysOn-redundansklusterinstanser för SQL Server (FCI:er) stöds inte för säkerhetskopiering.
- Säkerhetskopieringar av SQL Server ska konfigureras i portalen. För närvarande kan du inte konfigurera säkerhetskopiering med Azure PowerShell, CLI eller REST-API:er.
- Åtgärder för säkerhetskopiering och återställning för speglade FCI-databaser, ögonblicksbilder av databaser samt databaser stöds inte.
- Databaser med ett stort antal filer kan inte skyddas. Det maximala antal filer som stöds är inte deterministiskt. Det beror inte bara på antalet filer, utan även på filernas sökvägslängd. 

Granska [vanliga frågor och svar](faq-backup-sql-server.md) om säkerhetskopiering av SQL Server-databaser.
## <a name="scenario-support"></a>Scenariostöd

**Support** | **Detaljer**
--- | ---
**Distributioner som stöds** | Virtuella SQL Marketplace Azure-datorer och virtuella icke-Marketplace-datorer (manuellt installerat SQL Server) stöds.
**Geografiska områden som stöds** | Australien, sydöstra (ASE); Brasilien, södra (BRS); Kanada, centrala (CNC); Kanada, östra (CE); USA, centrala (CUS); Asien, östra (EA); Australien, östra (AE); USA, östra (EUS); USA, östra 2 (EUS2); Indien, centrala (INC); Indien, södra (INS); Japan, östra (JPE); Japan, västra (JPW); Korea, centrala (KRC); Korea, södra (KRS); USA, norra centrala (NCUS); Europa, norra (NE); USA, södra centrala (SCUS); Asien, sydöstra (SEA) Storbritannien, södra (UKS); Storbritannien, västra (UKW); USA, västra centrala (WCUS); Europa, västra (WE); USA, västra (WUS); USA, västra 2 (WUS 2)
**Operativsystem som stöds** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux stöds inte för närvarande.
**SQL Server-versioner som stöds** | SQL Server 2017, SQL Server 2016, SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.


## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera följande villkor innan du säkerhetskopierar SQL Server-databasen:

1. Identifiera eller [skapa](backup-azure-sql-database.md#create-a-recovery-services-vault) ett Recovery Services-valv i samma region eller språkinställning som den virtuella dator som är värd för SQL Server-instansen.
2. [Kontrollera de behörigheter för virtuella datorer](#fix-sql-sysadmin-permissions) som behövs för att säkerhetskopiera SQL-databaserna.
3. Kontrollera att den virtuella datorn har [nätverksanslutning](backup-azure-sql-database.md#establish-network-connectivity).
4. Kontrollera att SQL Server-databaserna namnges enligt [riktlinjerna för namngivning](backup-azure-sql-database.md) för Azure Backup.
5. Kontrollera att du inte har några andra lösningar för säkerhetskopiering aktiverade för databasen. Inaktivera alla andra SQL Server-säkerhetskopieringar innan du installerar det här scenariot. Du kan aktivera Azure Backup för en virtuell Azure-dator tillsammans med Azure Backup för en SQL Server-databas som körs på den virtuella datorn utan konflikter.


### <a name="establish-network-connectivity"></a>Etablera nätverksanslutning

Den virtuella SQL Server-datorn behöver ha anslutning till offentliga Azure-IP-adresser för alla åtgärder. VM-åtgärder (databasidentifiering, konfiguration av säkerhetskopieringar, schemaläggning av säkerhetskopieringar, återställning av återställningspunkter osv.) misslyckas utan anslutning till de offentliga IP-adresserna. Upprätta en anslutning med något av följande alternativ:

- **Tillåt IP-intervallen för Azure-datacenter**: Tillåt [IP-intervallen](https://www.microsoft.com/download/details.aspx?id=41653) i nedladdningen. För åtkomst i en nätverkssäkerhetsgrupp (NSG) använder du cmdleten **Set-AzureNetworkSecurityRule**.
- **Distribuera en HTTP-proxyserver för att dirigera trafik**: När du säkerhetskopierar en SQL Server-databas på en virtuell Azure-dator använder säkerhetskopieringstillägget på den virtuella datorn HTTPS-API:er för att skicka hanteringskommandon till Azure Backup och data till Azure Storage. Säkerhetskopieringstillägget använder också Azure Active Directory (Azure AD) för autentisering. Dirigera trafiken för säkerhetskopieringstillägget för dessa tre tjänster via HTTP-proxyn. Tillägget är den enda komponenten som är konfigurerad för åtkomst till det offentliga Internet.

Varje alternativ har fördelar och nackdelar

**Alternativ** | **Fördelar** | **Nackdelar**
--- | --- | ---
Tillåta IP-intervall | Inga ytterligare kostnader. | Komplicerat att hantera eftersom IP-adressintervallen ändras över tid. <br/><br/> Ger åtkomst till hela Azure, inte bara Azure Storage.
Använda en HTTP-proxy   | Detaljerad kontroll i proxyn över lagrings-URL:er tillåts. <br/><br/> Enskild punkt för Internetåtkomst till virtuella datorer. <br/><br/> Inte föremål för Azure-IP-adressändringar. | Ytterligare kostnader för att köra en virtuell dator med proxyprogramvaran. 

### <a name="set-vm-permissions"></a>Ange VM-behörigheter

Azure Backup gör ett antal saker när du konfigurerar säkerhetskopiering för en SQL Server-databas:

- Lägger till tillägget **AzureBackupWindowsWorkload**.
- För att identifiera databaser på den virtuella datorn skapar Azure Backup kontot **NT SERVICE\AzureWLBackupPluginSvc**. Det här kontot används för säkerhetskopiering och återställning och kräver SQL-sysadmin-behörighet.
- Azure Backup utnyttjar kontot **NT AUTHORITY\SYSTEM** för databasidentifiering/-förfrågan. Därför behöver det här kontot vara en offentlig inloggning på SQL.

Om du inte skapade SQL Server-datorn från Azure Marketplace kan det hända att du får felet **UserErrorSQLNoSysadminMembership**. Om det inträffar [följer du de här instruktionerna](#fix-sql-sysadmin-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Kontrollera riktlinjerna för namngivning av databaser för Azure Backup

Undvik följande för databasnamn:

  * Avslutande/inledande blanksteg
  * Avslutande ”!”
  * Avslutande hakparentes ”]”

Vi har alias för tecken som inte stöds i Azure-tabellen, men vi rekommenderar att du undviker dem. [Läs mer](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Identifiera SQL Server-databaser

Identifiera databaser som körs på den virtuella datorn. 
1. I [Azure-portalen](https://portal.azure.com) öppnar du det Recovery Services-valv som du använder för att säkerhetskopiera databasen.

2. På instrumentpanelen för **Recovery Services-valvet** väljer du **Säkerhetskopiering**. 

   ![Välj Säkerhetskopiering för att öppna menyn Säkerhetskopieringsmål](./media/backup-azure-sql-database/open-backup-menu.png)

3. I **Säkerhetskopieringsmål** anger du **Var körs din arbetsbelastning** till **Azure** (standardinställningen).

4. I **Vad vill du säkerhetskopiera** väljer du **SQL Server på Azure VM**.

    ![Välj SQL Server på Azure VM för säkerhetskopieringen](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)


5. Under **Säkerhetskopieringsmål** > **Identifiera databaser på virtuella datorer** väljer du **Start Discovery** (Starta identifiering) för att söka efter oskyddade virtuella datorer i prenumerationen. Det kan ta ett tag beroende på antalet oskyddade virtuella datorer i prenumerationen.

    ![Säkerhetskopieringen väntar under sökningen efter databaser på virtuella datorer](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. I listan över virtuella datorer väljer du den virtuella dator som kör SQL Server-databasen > **Identifiera databaser**.

7. Spåra databasidentifiering i området **Meddelanden**. Det kan ta en stund innan jobbet är klart, beroende på hur många databaser som finns på den virtuella datorn. När de valda databaserna identifieras visas ett meddelande.

    ![Meddelande som anger att distributionen lyckades](./media/backup-azure-sql-database/notifications-db-discovered.png)

    - Oskyddade virtuella datorer bör visas i listan efter identifiering, sorterade efter namn och resursgrupp.
    - Om en virtuell dator inte visas som förväntat kontrollerar du om den redan har säkerhetskopierats i ett valv.
    - Flera virtuella datorer kan ha samma namn, men de tillhör olika resursgrupper. 

9. Välj den virtuella dator som kör SQL Server-databasen > **Identifiera databaser**. 
10. Azure Backup identifierar alla SQL Server-databaser på den virtuella datorn. Under identifieringen sker följande i bakgrunden:

    - Azure Backup registrerar den virtuella datorn med valvet för säkerhetskopiering av arbetsbelastning. Alla databaser på den registrerade virtuella datorn kan endast säkerhetskopieras till det här valvet.
    - Azure Backup installerar tillägget **AzureBackupWindowsWorkload** på den virtuella datorn. Det installeras inte någon agent på SQL-databasen.
    - Azure Backup skapar tjänstkontot **NT Service\AzureWLBackupPluginSvc** på den virtuella datorn.
      - Alla åtgärder för säkerhetskopiering och återställning använder tjänstkontot.
      - **NT Service\AzureWLBackupPluginSvc** behöver SQL-sysadmin-behörigheter. Alla virtuella SQL Server-datorer som skapas i Azure Marketplace har **SqlIaaSExtension** installerat. Tillägget **AzureBackupWindowsWorkload** använder **SQLIaaSExtension** för att automatiskt hämta de behörigheter som krävs.
    - Om du inte skapade den virtuella datorn från Marketplace har den inte **SqlIaaSExtension** installerat, och identifieringsåtgärden misslyckas med felmeddelandet **UserErrorSQLNoSysAdminMembership**. Följ instruktionerna i [#fix-sql-sysadmin-permissions] för att åtgärda det här problemet.

        ![Välj den virtuella datorn och databasen](./media/backup-azure-sql-database/registration-errors.png)



## <a name="configure-a-backup-policy"></a>Konfigurera en säkerhetskopieringspolicy 

För att optimera säkerhetskopieringsbelastningar anger Azure Backup ett maximalt antal databaser i ett säkerhetskopieringsjobb till 50.

- Om du vill skydda fler än 50 databaser konfigurerar du flera säkerhetskopieringar.
- Alternativt kan du aktivera automatiskt skydd. Automatiskt skydd skyddar befintliga databaser i ett svep och skyddar automatiskt nya databaser som läggs till i instansen av tillgänglighetsgruppen.


Konfigurera säkerhetskopiering på följande sätt:

1. På valvets instrumentpanel väljer du **Säkerhetskopiering**. 

   ![Välj Säkerhetskopiering för att öppna menyn Säkerhetskopieringsmål](./media/backup-azure-sql-database/open-backup-menu.png)

3. På menyn **Säkerhetskopieringsmål** anger du **Var körs din arbetsbelastning** till **Azure**.

4. I **Vad vill du säkerhetskopiera** väljer du **SQL Server på Azure VM**.

    ![Välj SQL Server på Azure VM för säkerhetskopieringen](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    
5. I **Säkerhetskopieringsmål** väljer du **Konfigurera säkerhetskopiering**.

    ![Välj Konfigurera säkerhetskopiering](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    ![Visa alla SQL Server-instanser med fristående databaser](./media/backup-azure-sql-database/list-of-sql-databases.png)

6. Välj alla de databaser som du vill skydda > **OK**.

    ![Skydda databasen](./media/backup-azure-sql-database/select-database-to-protect.png)

    - Alla SQL Server-instanser visas (fristående och tillgänglighetsgrupper).
    - Välj nedåtpilen till vänster om det instansnamn/den tillgänglighetsgrupp som ska filtreras.

7. På menyn **Säkerhetskopiering** väljer du **Säkerhetskopieringspolicy**. 

    ![Välja säkerhetskopieringspolicy](./media/backup-azure-sql-database/select-backup-policy.png)

8. I **Välj säkerhetskopieringspolicy** väljer du en policy och klickar på **OK**.

    - Välja standardpolicy: **HourlyLogBackup**.
    - Välj en befintlig säkerhetskopieringspolicy som har skapats för SQL.
    - [Definiera en ny policy](backup-azure-sql-database.md#configure-a-backup-policy) baserat på ditt RPO och kvarhållningsintervall.
    - Under förhandsversionen kan du inte redigera en befintlig säkerhetskopieringspolicy.
    
9. På **säkerhetskopieringsmenyn** väljer du **Aktivera säkerhetskopiering**.

    ![Aktivera den valda säkerhetskopieringspolicyn](./media/backup-azure-sql-database/enable-backup-button.png)

10. Spåra konfigurationsförloppet i **meddelandefältet** på portalen.

    ![Meddelandefältet](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy

En säkerhetskopieringspolicy definierar när säkerhetskopior skapas och hur länge de behålls. 

- En policy skapas på valvnivå.
- Flera valv kan använda samma säkerhetskopieringspolicy, men du måste tillämpa säkerhetskopieringspolicyn på varje valv.
- När du skapar en säkerhetskopieringspolicy används en daglig fullständig säkerhetskopia som standard.
- Du kan lägga till en differentiell säkerhetskopia, men endast om du konfigurerar så att fullständiga säkerhetskopior utförs varje vecka.
- [Lär dig mer om](backup-architecture.md#sql-server-backup-types) olika typer av säkerhetskopieringspolicyer.


Så här skapar du en säkerhetskopieringspolicy:

1. I valvet klickar du på **Säkerhetskopieringspolicyer** > **Lägg till**.
2. På menyn **Lägg till** klickar du på **SQL Server på Azure VM**. Detta gör så att policytypen definieras.

   ![Välj en policytyp för den nya säkerhetskopieringspolicyn](./media/backup-azure-sql-database/policy-type-details.png)

3. I **Policynamn** anger du ett namn för den nya policyn. 
4. I **Policy för fullständig säkerhetskopia** väljer du en **Säkerhetskopieringsfrekvens** och väljer **Dagligen** eller **Varje vecka**.

    - För **Dagligen** väljer du den timme och den tidszon då säkerhetskopieringsjobbet börjar.
    - Du måste köra en fullständig säkerhetskopia. Du kan inte stänga av alternativet **Fullständig säkerhetskopia**.
    - Klicka på **Fullständig säkerhetskopia** för att visa policyn. 
    - Du kan inte skapa differentiella säkerhetskopior för dagliga fullständiga säkerhetskopior.
    - För **Varje vecka** väljer du den veckodag, timme och tidszon då säkerhetskopieringsjobbet börjar.

    ![Nya fält för säkerhetskopieringspolicy](./media/backup-azure-sql-database/full-backup-policy.png)  

5. För **Kvarhållningsintervall** är alla alternativ markerade som standard. Avmarkera eventuella gränser för kvarhållningsintervall som du inte vill använda, och ange intervall som ska användas. 

    - Återställningspunkter taggas för kvarhållning baserat på deras kvarhållningsintervall. Om du till exempel väljer en daglig fullständig säkerhetskopia utlöses endast en fullständig säkerhetskopia varje dag.
    - Säkerhetskopieringen för en viss dag taggas och behålls baserat på det veckovisa kvarhållningsintervallet och inställningen för veckovis kvarhållning.
    - De månatliga och årliga kvarhållningsintervallen fungerar på liknande sätt.

   ![Inställningar för kvarhållningsintervall](./media/backup-azure-sql-database/retention-range-interval.png)

    

6. På menyn **Policy för fullständig säkerhetskopia** väljer du **OK** för att acceptera inställningarna.
7. Om du vill lägga till en policy för differentiell säkerhetskopia väljer du **Differentiell säkerhetskopia**. 

   ![Inställningar för kvarhållningsintervall](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Öppna menyn för policy för differentiell säkerhetskopia](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. I **Policy för differentiell säkerhetskopia** väljer du **Aktivera** för att öppna kontrollerna för frekvens och kvarhållning. 

    - Du kan endast utlösa en differentiell säkerhetskopia per dag.
    - Differentiella säkerhetskopior kan behållas i upp till 180 dagar. Om du behöver längre kvarhållning måste du använda fullständiga säkerhetskopior.

9. Välj **OK** för att spara policyn och återgå till huvudmenyn **Säkerhetskopieringspolicy**.

10. Om du vill lägga till en policy för loggsäkerhetskopia väljer du **Loggsäkerhetskopia**.
11. I **Loggsäkerhetskopia** väljer du **Aktivera** och anger kontrollerna för frekvens och kvarhållning. Loggsäkerhetskopior kan skapas så ofta som var 15:e minut och kan behållas i upp till 35 dagar.
12. Välj **OK** för att spara policyn och återgå till huvudmenyn **Säkerhetskopieringspolicy**.

   ![Redigera policyn för loggsäkerhetskopia](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. På menyn **Säkerhetskopieringspolicy** väljer du om du vill aktivera **komprimering av SQL-säkerhetskopiering**.
    - Komprimering är inaktiverat som standard.
    - På serverdelen använder Azure Backup SQL-specifik säkerhetskopieringskomprimering.

14. När du har slutfört redigeringarna i säkerhetskopieringspolicyn väljer du **OK**.



## <a name="enable-auto-protection"></a>Aktivera automatiskt skydd  

Aktivera automatiskt skydd för att automatiskt skydda alla befintliga databaser samt databaser som läggs till i framtiden till en fristående SQL Server-instans eller en SQL Server Always On-tillgänglighetsgrupp. 

- När du aktiverar automatiskt skydd och väljer en princip fortsätter de befintliga skyddade databaserna att använda den föregående policyn.
- Det finns ingen gräns för det antal databaser som du kan välja för automatiskt skydd på en gång.

Aktivera automatiskt skydd på följande sätt:

1. I **Items to backup** (Objekt som ska säkerhetskopieras) väljer du den instans som du vill aktivera automatiskt skydd för.
2. Välj listrutan under **Autoprotect** (Automatiskt skydd) och välj **På**. Klicka sedan på **OK**.

    ![Aktivera automatiskt skydd i AlwaysOn-tillgänglighetsgruppen](./media/backup-azure-sql-database/enable-auto-protection.png)

3. Säkerhetskopiering konfigureras för alla databaser tillsammans och kan spåras i **Säkerhetskopieringsjobb**.


Om du behöver inaktivera automatiskt skydd klickar du på instansnamnet under **Konfigurera säkerhetskopiering** och väljer **Inaktivera automatiskt skydd** för instansen. Alla databaser fortsätter att säkerhetskopiera. Men framtida databaser skyddas inte automatiskt.

![Inaktivera automatiskt skydd i den instansen](./media/backup-azure-sql-database/disable-auto-protection.png)


## <a name="fix-sql-sysadmin-permissions"></a>Åtgärda SQL-sysadmin-behörigheter

Om du behöver åtgärda behörigheter grund av felet **UserErrorSQLNoSysadminMembership** gör du följande:

1. Använd ett konto med SQL Server-sysadmin-behörighet för att logga in till SQL Server Management Studio (SSMS). Såvida du inte behöver specialbehörigheter bör Windows-autentisering fungera.
2. På SQL Server öppnar du mappen **Security/Logins** (Säkerhet/Inloggningar).

    ![Öppna mappen Security/inloggningar för att se konton](./media/backup-azure-sql-database/security-login-list.png)

3. Högerklicka på mappen **Logins** och välj **Ny inloggning**. I **Login - New** (Inloggning – ny) väljer du **Sök**.

    ![I dialogrutan Inloggning – ny väljer du Sök](./media/backup-azure-sql-database/new-login-search.png)

3. Kontot för virtuell Windows-tjänst, **NT SERVICE\AzureWLBackupPluginSvc**, skapades under registreringen av den virtuell datorn och SQL-identifieringsfasen. Ange kontonamnet såsom det visas i **Ange ett objektnamn du vill markera**. Välj **Kontrollera namn** för att matcha namnet. Klicka på **OK**.

    ![Välj Kontrollera namn för att matcha det okända tjänstnamnet](./media/backup-azure-sql-database/check-name.png)


4. I **Serverroller** kontrollerar du att serverrollen **sysadmin** har valts. Klicka på **OK**. De behörigheter som krävs bör nu finnas.

    ![Kontrollera att serverrollen sysadmin har valts](./media/backup-azure-sql-database/sysadmin-server-role.png)

5. Nu associerar du databasen med Recovery Services-valvet. I Azure-portalen går du till listan **Skyddade servrar**, högerklickar på den server som är i ett felaktigt tillstånd > **Identifiera databaser på nytt**.

    ![Kontrollera att servern har rätt behörigheter](./media/backup-azure-sql-database/check-erroneous-server.png)

6. Kontrollera förloppet i området **Meddelanden**. När de valda databaserna hittas visas ett meddelande.

    ![Meddelande som anger att distributionen lyckades](./media/backup-azure-sql-database/notifications-db-discovered.png)

Alternativt kan du aktivera [automatiskt skydd](backup-azure-sql-database.md#enable-auto-protection) på hela instansen eller AlwaysOn-tillgänglighetsgruppen genom att välja alternativet **PÅ** i motsvarande listruta i kolumnen **AUTOPROTECT** (Automatiskt skydd). Funktionen [automatiskt skydd](backup-azure-sql-database.md#enable-auto-protection) aktiverar inte bara skydd på alla befintliga databaser i ett svep, utan skyddar även automatiskt alla nya nya databaser som läggs till i den instansen eller tillgänglighetsgruppen i framtiden.  

   ![Aktivera automatiskt skydd i AlwaysOn-tillgänglighetsgruppen](./media/backup-azure-sql-database/enable-auto-protection.png)

## <a name="next-steps"></a>Nästa steg

- [Lär dig om att](restore-sql-database-azure-vm.md) återställa säkerhetskopierade SQL Server-databaser.
- [Lär dig om att](manage-monitor-sql-database-backup.md) hantera säkerhetskopierade SQL Server-databaser.


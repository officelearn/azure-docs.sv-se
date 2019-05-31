---
title: Säkerhetskopiera SQL Server-databaser i virtuella Azure-datorer | Microsoft Docs
description: Lär dig hur du säkerhetskopierar SQL Server-databaser i virtuella Azure-datorer
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: sachdevaswati
ms.openlocfilehash: 0307dc5c83782119f6c10279563b8b9f0a999d28
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236887"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Säkerhetskopiera SQL Server-databaser i virtuella Azure-datorer

SQL Server-databaser är kritiska arbetsbelastningar som kräver ett mål för låg återställningspunkt (RPO) och långsiktig kvarhållning. Du kan säkerhetskopiera SQL Server-databaser som körs på virtuella Azure-datorer (VM) med hjälp av [Azure Backup](backup-overview.md).

Den här artikeln visar hur du säkerhetskopierar en SQL Server-databas som körs på en virtuell Azure-dator till ett Azure Backup Recovery Services-valv.

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Skapa och konfigurera ett valv.
> * Identifiera databaser och konfigurerar säkerhetskopieringar.
> * Konfigurera automatiskt skydd för databaser.


## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du säkerhetskopierar en SQL Server-databas kan du kontrollera följande kriterier:

1. Identifiera eller skapa en [Recovery Services-valv](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) i samma region eller språk som den virtuella datorn som är värd för SQL Server-instansen.
2. Kontrollera den [VM behörighet](backup-azure-sql-database.md#fix-sql-sysadmin-permissions) att säkerhetskopiera SQL-databaser.
3. Kontrollera att den virtuella datorn har [nätverksanslutningar](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
4. Se till att SQL Server-databaserna följer den [databasen riktlinjerna för namngivning för Azure Backup](#database-naming-guidelines-for-azure-backup).
5. Kontrollera att det inte finns några andra lösningar för säkerhetskopiering har aktiverats för databasen. Inaktivera alla andra säkerhetskopior i SQL Server innan du säkerhetskopierar databasen.

> [!NOTE]
> Du kan aktivera Azure Backup för en Azure-dator och för en SQL Server-databas som körs på den virtuella datorn utan konflikter.


### <a name="establish-network-connectivity"></a>Etablera nätverksanslutning

För alla åtgärder kräver en SQL Server VM anslutning till Azure offentliga IP-adresser. VM-åtgärder (databasidentifiering, säkerhetskopieringar, schemalägga säkerhetskopieringar, återställa återställningspunkter och så vidare) misslyckas utan anslutning till Azure offentlig IP-adresser.

Upprätta en anslutning med hjälp av något av följande alternativ:

- **Tillåt Azure-datacenter IP-adressintervall**. Det här alternativet kan [IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653) i nedladdningen. Använd cmdleten Set-AzureNetworkSecurityRule för att komma åt en nätverkssäkerhetsgrupp (NSG). Om du är säker mottagare listan endast regionspecifika IP-adresser, du måste också uppdatera listan över betrodda mottagare tjänsttaggen Azure Active Directory (Azure AD) för att aktivera autentisering.

- **Tillåt åtkomst med hjälp av NSG taggar**. Om du använder NSG: er för att begränsa anslutning kan det här alternativet lägger till en regel för din NSG som tillåter utgående åtkomst till Azure Backup med hjälp av taggen AzureBackup. Förutom den här taggen måste du också motsvarande [regler](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) för Azure AD och Azure Storage för att tillåta anslutning för autentisering och överföring. Taggen AzureBackup är endast tillgänglig på PowerShell. Skapa en regel med hjälp av AzureBackup-taggen:

    - Lägg till autentiseringsuppgifter för Azure-konto och uppdatera nationella moln<br/>
    `Add-AzureRmAccount`

    - Välj prenumerationen som NSG<br/>
    `Select-AzureRmSubscription "<Subscription Id>"`

     - Välj NSG: N<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

    - Lägg till utgående regel för Azure Backup-tjänsttaggen för att tillåta<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

  - Spara NSG: N<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`
- **Tillåta åtkomst med hjälp av Azure-brandvägg taggar**. Om du använder Azure-brandväggen, skapa en regel för program med hjälp av AzureBackup [FQDN taggen](https://docs.microsoft.com/azure/firewall/fqdn-tags). Detta tillåter utgående åtkomst till Azure Backup.
- **Distribuera en HTTP-proxyserver kan dirigera trafik**. När du säkerhetskopierar en SQL Server-databas på en Azure virtuell dator använder tillägget på den virtuella datorn HTTPS-API: er för att skicka kommandon för hantering av Azure Backup och data till Azure Storage. Säkerhetskopieringstillägget använder också Azure AD för autentisering. Dirigera trafiken för säkerhetskopieringstillägget för dessa tre tjänster via HTTP-proxyn. Tilläggen är den enda komponenten som är konfigurerad för åtkomst till det offentliga internet.

Anslutningsalternativ omfattar följande fördelar och nackdelar:

**Alternativ** | **Fördelar** | **Nackdelar**
--- | --- | ---
Tillåta IP-intervall | Utan extra kostnad | Komplex för att hantera eftersom de IP-adressintervall ändras över tid <br/><br/> Ger åtkomst till hela Azure, inte bara Azure Storage
Använd tjänsttaggar för NSG | Enklare att hantera som ändras automatiskt slås samman <br/><br/> Utan extra kostnad <br/><br/> | Kan endast användas med NSG: er <br/><br/> Ger åtkomst till hela tjänsten
Använd Azure brandväggen FQDN taggar | Enklare att hantera eftersom nödvändiga FQDN: er hanteras automatiskt | Kan endast användas med Azure-brandvägg
Använda en HTTP-proxy | Detaljerad kontroll i proxyn över lagringen URL: er tillåts <br/><br/> Enskild punkt för internet-åtkomst till virtuella datorer <br/><br/> Inte kan komma att ändras för Azure-IP-adress | Ytterligare kostnader för att köra en virtuell dator med proxyprogrammet

### <a name="set-vm-permissions"></a>Ange VM-behörigheter

När du konfigurerar en säkerhetskopia för en SQL Server-databas, gör Azure Backup följande:

- Lägger till tillägget AzureBackupWindowsWorkload.
- Skapar ett NT SERVICE\AzureWLBackupPluginSvc-konto för att identifiera databaser på den virtuella datorn. Det här kontot används för en säkerhetskopia och återställa och kräver SQL sysadmin-behörighet.
- Identifierar databaser som körs på en virtuell dator, Azure Backup använder kontot NT AUTHORITY\SYSTEM. Det här kontot måste vara en offentlig logga in på SQL.

Om du inte skapade SQL Server-VM på Azure Marketplace, kan det hända att ett UserErrorSQLNoSysadminMembership-fel. Mer information finns i avsnittet funktionen överväganden och begränsningar finns i [om SQL Server-säkerhetskopiering i Azure virtuella datorer](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

### <a name="database-naming-guidelines-for-azure-backup"></a>Databasen riktlinjerna för namngivning för Azure Backup

Undvik att använda följande element i databasnamn:

  * Avslutande och inledande blanksteg
  * Avslutande marks utropstecken (!)
  * Avslutande hakparenteser (])
  * Semikolon (;)
  * Snedstreck (/)

Alias är tillgänglig för tecken som inte stöds, men vi rekommenderar att du inte dem. Mer information finns i [Understanding the Table Service Data Model](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN) (Så här fungerar datamodellen för Table Storage).


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Identifiera SQL Server-databaser

Så här att identifiera databaser som körs på en virtuell dator:

1. I [Azure-portalen](https://portal.azure.com) öppnar du det Recovery Services-valv som du använder för att säkerhetskopiera databasen.

2. I den **Recovery Services-valv** instrumentpanelen, väljer **Backup**.

   ![Välj Säkerhetskopiering för att öppna menyn Säkerhetskopieringsmål](./media/backup-azure-sql-database/open-backup-menu.png)

3. I **säkerhetskopieringsmål**anger **var körs din arbetsbelastning?** till **Azure**.

4. I **Vad vill du säkerhetskopiera** väljer du **SQL Server på Azure VM**.

    ![Välj SQL Server på Azure VM för säkerhetskopieringen](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Under **Säkerhetskopieringsmål** > **Identifiera databaser på virtuella datorer** väljer du **Start Discovery** (Starta identifiering) för att söka efter oskyddade virtuella datorer i prenumerationen. Den här sökningen kan ta en stund, beroende på hur många oskyddade virtuella datorer i prenumerationen.

   - Oskyddade virtuella datorer bör visas i listan efter identifiering, sorterade efter namn och resursgrupp.
   - Om en virtuell dator inte visas som förväntat, kan du se om det redan har säkerhetskopierats i ett valv.
   - Flera virtuella datorer kan ha samma namn, men de kommer tillhör olika resursgrupper.

     ![Säkerhetskopieringen väntar under sökningen efter databaser på virtuella datorer](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. I listan över virtuella datorer väljer du den virtuella dator som kör SQL Server-databasen > **Identifiera databaser**.

7. Spåra databasidentifiering i **meddelanden**. Den tid som krävs för den här åtgärden beror på antalet VM-databaser. När de valda databaserna identifieras visas ett meddelande.

    ![Meddelande som anger att distributionen lyckades](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup identifierar alla SQL Server-databaser på den virtuella datorn. Vid identifiering inträffar följande element i bakgrunden:

    - Azure Backup registreras den virtuella datorn med valvet för säkerhetskopiering av arbetsbelastning. Alla databaser på den registrerade virtuella datorn kan säkerhetskopieras i valvet endast.
    - Azure Backup installerar tillägget AzureBackupWindowsWorkload på den virtuella datorn. Ingen agent är installerad på en SQL-databas.
    - Azure Backup skapas tjänstkontot NT Service\AzureWLBackupPluginSvc på den virtuella datorn.
      - Alla åtgärder för säkerhetskopiering och återställning använder tjänstkontot.
      - NT Service\AzureWLBackupPluginSvc krävs SQL sysadmin-behörighet. Alla SQL Server-datorer som skapats i Marketplace medföljer SqlIaaSExtension installerad. Tillägget AzureBackupWindowsWorkload använder SQLIaaSExtension att automatiskt få behörigheterna som krävs.
    - Om du inte har skapat den virtuella datorn från Marketplace, den virtuella datorn har inte SqlIaaSExtension installerad och Identifieringsåtgärden misslyckas med felmeddelandet UserErrorSQLNoSysAdminMembership. Om du vill åtgärda problemet genom att följa den [instruktioner](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

        ![Välj den virtuella datorn och databasen](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering  

1. I **säkerhetskopieringsmålet** > **steg 2: Konfigurera säkerhetskopiering**väljer **Konfigurera säkerhetskopiering**.

   ![Välj Konfigurera säkerhetskopiering](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. I **Välj objekt som ska säkerhetskopieras**, visas alla registrerade Tillgänglighetsgrupper och fristående SQL Server-instanser. Markera pilen till vänster om en rad för att expandera listan över oskyddade databaser i instansen eller Always On-tillgänglighetsgrupp.  

    ![Visa alla SQL Server-instanser med fristående databaser](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Välj alla databaser som du vill skydda och välj sedan **OK**.

   ![Skydda databasen](./media/backup-azure-sql-database/select-database-to-protect.png)

   För att optimera säkerhetskopieringsbelastningar anger Azure Backup ett maximalt antal databaser i ett säkerhetskopieringsjobb till 50.

     * Om du vill skydda fler än 50 databaser konfigurerar du flera säkerhetskopieringar.
     * Aktivera [ ](#enable-auto-protection) en hel instans eller AlwaysOn-tillgänglighetsgruppen. I den **AUTOPROTECT** listrutan, väljer **på**, och välj sedan **OK**.

    > [!NOTE]
    > Den [automatiskt skydd](#enable-auto-protection) funktionen inte bara aktiverar skydd på alla befintliga databaser på samma gång, men också automatiskt skyddar eventuella nya databaser som läggs till i den instansen eller tillgänglighetsgruppen.  

4. Välj **OK** att öppna **säkerhetskopieringspolicy**.

    ![Aktivera automatiskt skydd för Always On availability-gruppen](./media/backup-azure-sql-database/enable-auto-protection.png)

5. I **säkerhetskopieringspolicy**, väljer du en princip och väljer sedan **OK**.

   - Välj principen som HourlyLogBackup.
   - Välj en befintlig säkerhetskopieringspolicy som har skapats för SQL.
   - Definiera en ny princip baserat på din RPO och kvarhållning.

     ![Välja säkerhetskopieringspolicy](./media/backup-azure-sql-database/select-backup-policy.png)

6. I **säkerhetskopiering**väljer **Aktivera säkerhetskopiering**.

    ![Aktivera den valda säkerhetskopieringspolicyn](./media/backup-azure-sql-database/enable-backup-button.png)

7. Spåra konfigurationsförloppet i den **meddelanden** området i portalen.

    ![Meddelandefältet](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy

En säkerhetskopieringspolicy definierar när säkerhetskopior skapas och hur länge de behålls.

- En policy skapas på valvnivå.
- Flera valv kan använda samma säkerhetskopieringspolicy, men du måste tillämpa säkerhetskopieringspolicyn på varje valv.
- När du skapar en säkerhetskopieringspolicy används en daglig fullständig säkerhetskopia som standard.
- Du kan lägga till en differentiell säkerhetskopia, men endast om du konfigurerar så att fullständiga säkerhetskopior utförs varje vecka.
- Lär dig mer om [olika typer av principer för säkerhetskopiering](backup-architecture.md#sql-server-backup-types).

Så här skapar du en säkerhetskopieringspolicy:

1. I valvet, väljer **Säkerhetskopieringsprinciper** > **Lägg till**.
2. I **Lägg till**väljer **SQL Server i Azure VM** att definiera typen.

   ![Välj en policytyp för den nya säkerhetskopieringspolicyn](./media/backup-azure-sql-database/policy-type-details.png)

3. I **Policynamn** anger du ett namn för den nya policyn.
4. I **princip för fullständig säkerhetskopiering**väljer en **säkerhetskopieringsfrekvens**. Välj antingen **dagliga** eller **veckovisa**.

   - För **Dagligen** väljer du den timme och den tidszon då säkerhetskopieringsjobbet börjar.
   - För **Varje vecka** väljer du den veckodag, timme och tidszon då säkerhetskopieringsjobbet börjar.
   - Kör en fullständig säkerhetskopiering eftersom du inte kan inaktivera den **fullständig säkerhetskopiering** alternativet.
   - Välj **fullständig säkerhetskopiering** att visa principen.
   - Du kan inte skapa differentiella säkerhetskopior för dagliga fullständiga säkerhetskopior.

     ![Nya fält för säkerhetskopieringspolicy](./media/backup-azure-sql-database/full-backup-policy.png)  

5. I **KVARHÅLLNINGSINTERVALL**, alla alternativ som är markerade som standard. Rensa begränsar alla Kvarhållningsintervall som du inte vill och sedan ange intervall för att använda.

    - Minsta kvarhållningsperioden för alla typer av säkerhetskopiering (fullständig, differentiell och log) är sju dagar.
    - Återställningspunkter taggas för kvarhållning baserat på deras kvarhållningsintervall. Om du till exempel väljer en daglig fullständig säkerhetskopia utlöses endast en fullständig säkerhetskopia varje dag.
    - Säkerhetskopiering baserat för en viss dag är märkta och bevaras på veckovisa kvarhållningsintervallet och varje vecka kvarhållningsinställning.
    - Månatliga och årliga Kvarhållningsintervall fungerar på liknande sätt.

       ![Inställningar för kvarhållningsintervall](./media/backup-azure-sql-database/retention-range-interval.png)

6. På menyn **Policy för fullständig säkerhetskopia** väljer du **OK** för att acceptera inställningarna.
7. Om du vill lägga till en policy för differentiell säkerhetskopia väljer du **Differentiell säkerhetskopia**.

   ![Inställningar för kvarhållningsintervall](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Öppna menyn för policy för differentiell säkerhetskopia](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. I **Policy för differentiell säkerhetskopia** väljer du **Aktivera** för att öppna kontrollerna för frekvens och kvarhållning.

    - Du kan utlösa endast en differentiell säkerhetskopiering per dag.
    - Differentiella säkerhetskopior kan behållas i upp till 180 dagar. Använda fullständiga säkerhetskopieringar för längre kvarhållning.

9. Välj **OK** för att spara policyn och återgå till huvudmenyn **Säkerhetskopieringspolicy**.

10. Om du vill lägga till en policy för loggsäkerhetskopia väljer du **Loggsäkerhetskopia**.
11. I **Loggsäkerhetskopia** väljer du **Aktivera** och anger kontrollerna för frekvens och kvarhållning. Loggsäkerhetskopior kan ske så ofta som var 15: e minut och kan behållas i upp till 35 dagar.
12. Välj **OK** för att spara policyn och återgå till huvudmenyn **Säkerhetskopieringspolicy**.

    ![Redigera policyn för loggsäkerhetskopia](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. På menyn **Säkerhetskopieringspolicy** väljer du om du vill aktivera **komprimering av SQL-säkerhetskopiering**.
    - Komprimering är inaktiverat som standard.
    - På serverdelen använder Azure Backup SQL-specifik säkerhetskopieringskomprimering.

14. När du har slutfört redigeringarna i säkerhetskopieringspolicyn väljer du **OK**.


### <a name="modify-policy"></a>Ändra princip
Ändra princip om du vill ändra frekvensen eller kvarhållande säkerhetskopieringsintervallet.

> [!NOTE]
> Ändringar i kvarhållningsperioden tillämpas retroaktivt på alla äldre återställningspunkter förutom nya.

I instrumentpanelen för valvet går du till **hantera** > **principer för säkerhetskopiering** och välj den princip som du vill redigera.

  ![Hantera principer för säkerhetskopiering](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>Aktivera automatiskt skydd  

Du kan aktivera automatiskt skydd för automatisk säkerhetskopiering av alla befintliga och framtida databaser till en fristående SQL Server-instans eller till en Always On-tillgänglighetsgrupp.

- Det finns ingen gräns för antalet databaser som du kan välja för automatiskt skydd på en gång.
- Du kan inte selektivt skydda eller utesluta databaserna från skyddet i en databasinstans när du aktiverar automatiskt skydd.
- Om din instans innehåller redan vissa skyddade databaser, de kommer fortfarande är skyddade under deras respektive principer även när du har aktiverat automatiskt skydd. Alla oskyddade databaser som läggs till senare har en enda princip som du definierar vid tidpunkten för att aktivera automatiskt skydd, visas under **Konfigurera säkerhetskopiering**. Du kan dock ändra principen som är associerad med en databas som automatiskt skydd senare.  

Aktivera automatiskt skydd:

  1. I **Items to backup** (Objekt som ska säkerhetskopieras) väljer du den instans som du vill aktivera automatiskt skydd för.
  2. Välj listrutan under **AUTOPROTECT**, Välj **på**, och välj sedan **OK**.

      ![Aktivera automatiskt skydd på tillgänglighetsgruppen](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Säkerhetskopiering konfigureras för alla databaser tillsammans och kan spåras i **Säkerhetskopieringsjobb**.

Om du vill inaktivera det automatiska skyddet markerar du namnet under **Konfigurera säkerhetskopiering**, och välj sedan **inaktivera Autoprotect** för instansen. Alla databaser fortsätter att säkerhetskopieras, men framtida databaser skyddas inte automatiskt.

![Inaktivera automatiskt skydd på den instansen](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>Nästa steg

Lär dig att:

- [Återställa säkerhetskopierade SQL Server-databaser](restore-sql-database-azure-vm.md)
- [Hantera säkerhetskopierade SQL Server-databaser](manage-monitor-sql-database-backup.md)

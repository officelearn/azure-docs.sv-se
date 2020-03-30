---
title: Säkerhetskopiera SQL Server-databaser till Azure
description: I den här artikeln beskrivs hur du säkerhetskopierar SQL Server till Azure. Den här artikeln beskriver även återställning av SQL Server.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 537257733d7693598fd8007da6ce12c28fbeb02a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408768"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Om SQL Server-säkerhetskopiering i virtuella Azure-datorer

[Azure Backup](backup-overview.md) erbjuder en strömbaserad, specialiserad lösning för att säkerhetskopiera SQL Server som körs i virtuella Azure-datorer. Den här lösningen stämmer överens med Azure Backups fördelar med säkerhetskopiering av noll infrastruktur, långsiktig lagring och central hantering. Det ger dessutom följande fördelar specifikt för SQL Server:

1. Arbetsbelastningsmedvetna säkerhetskopior som stöder alla säkerhetskopieringstyper – fullständiga, differentiella och loggande
2. 15 min RPO (återställningspunkt mål) med täta logg säkerhetskopior
3. Återhämtning i tid upp till en sekund
4. Enskilda säkerhetskopiering och återställning på databasnivå

Om du vill visa de scenarier för säkerhetskopiering och återställning som vi stöder idag läser du [supportmatrisen](sql-support-matrix.md#scenario-support).

## <a name="backup-process"></a>Säkerhetskopieringsprocessen

Den här lösningen utnyttjar SQL native API:er för att ta säkerhetskopior av dina SQL-databaser.

* När du har angett den virtuella DATORN för SQL Server som du vill skydda och fråga efter databaserna `AzureBackupWindowsWorkload` i den, installerar Azure Backup-tjänsten ett tillägg för säkerhetskopiering av arbetsbelastningen på den virtuella datorn med namntillägget.
* Denna förlängning består av en samordnare och en SQL plugin. Medan samordnaren är ansvarig för att utlösa arbetsflöden för olika åtgärder som konfigurera säkerhetskopiering, säkerhetskopiering och återställning, är plugin-programmet ansvarig för det faktiska dataflödet.
* För att kunna identifiera databaser på den här virtuella `NT SERVICE\AzureWLBackupPluginSvc`datorn skapar Azure Backup kontot . Det här kontot används för säkerhetskopiering och återställning och kräver SQL sysadmin-behörigheter. Kontot `NT SERVICE\AzureWLBackupPluginSvc` är ett [virtuellt tjänstkonto](https://docs.microsoft.com/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)och kräver därför ingen lösenordshantering. Azure Backup utnyttjar `NT AUTHORITY\SYSTEM` kontot för databasidentifiering/förfrågan, så det här kontot måste vara en offentlig inloggning på SQL. Om du inte skapade SQL Server-datorn från Azure Marketplace kan det hända att du får felet **UserErrorSQLNoSysadminMembership**. Om det inträffar [följer du de här instruktionerna](#set-vm-permissions).
* När du utlöser konfigurera skydd för de valda databaserna konfigurerar säkerhetskopieringstjänsten koordinatorn med säkerhetskopieringsscheman och annan principinformation, som tillägget cachelagrar lokalt på den virtuella datorn.
* Vid den schemalagda tiden kommunicerar koordinatorn med insticksprogrammet och den börjar strömma säkerhetskopieringsdata från SQL-servern med HJÄLP AV VDI.  
* Insticksprogrammet skickar data direkt till återhämtningstjänster valv, vilket eliminerar behovet av en mellanlagring plats. Data krypteras och lagras av Azure Backup-tjänsten i lagringskonton.
* När dataöverföringen är klar bekräftar koordinatorn att den har genomförts med säkerhetskopieringstjänsten.

  ![ARKITEKTUR för SQL Backup](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Innan du börjar

Innan du börjar, kontrollera nedan:

1. Se till att du har en SQL Server-instans som körs i Azure. Du kan [snabbt skapa en SQL Server-instans](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) på Marketplace.
2. Granska [funktionsövervägandet](sql-support-matrix.md#feature-consideration-and-limitations) och [scenariostödet](sql-support-matrix.md#scenario-support).
3. [Granska vanliga frågor](faq-backup-sql-server.md) om det här scenariot.

## <a name="set-vm-permissions"></a>Ange VM-behörigheter

  När du kör identifiering på en SQL Server gör Azure Backup följande:

* Lägger till tillägget AzureBackupWindowsWorkload.
* Skapar ett NT SERVICE\AzureWLBackupPluginSvc-konto för att identifiera databaser på den virtuella datorn. Det här kontot används för en säkerhetskopiering och återställning och kräver SQL sysadmin-behörigheter.
* Identifierar databaser som körs på en virtuell dator, Azure Backup använder NT AUTHORITY\SYSTEM-kontot. Det här kontot måste vara ett offentligt inloggningstecken på SQL.

Om du inte skapade den virtuella datorn för SQL Server på Azure Marketplace eller om du använder SQL 2008 och 2008 R2 kan du få ett **UserErrorSQLNoSysadminMembership-fel.**

För att ge behörigheter i händelse av **SQL 2008** och **2008 R2** körs på Windows 2008 R2, se [här](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

För alla andra versioner korrigeringsbehörigheter med följande steg:

  1. Använd ett konto med SQL Server-sysadmin-behörighet för att logga in till SQL Server Management Studio (SSMS). Såvida du inte behöver specialbehörigheter bör Windows-autentisering fungera.
  2. Öppna mappen **Säkerhet/Inloggningar** på SQL Server.

      ![Öppna mappen Security/inloggningar för att se konton](./media/backup-azure-sql-database/security-login-list.png)

  3. Högerklicka på mappen **Inloggningar** och välj **Ny inloggning**. I **Login - New** (Inloggning – ny) väljer du **Sök**.

      ![I dialogrutan Inloggning – ny väljer du Sök](./media/backup-azure-sql-database/new-login-search.png)

  4. Kontot för virtuell Windows-tjänst, **NT SERVICE\AzureWLBackupPluginSvc**, skapades under registreringen av den virtuell datorn och SQL-identifieringsfasen. Ange kontonamnet såsom det visas i **Ange ett objektnamn du vill markera**. Välj **Kontrollera namn** för att matcha namnet. Klicka på **OK**.

      ![Välj Kontrollera namn för att matcha det okända tjänstnamnet](./media/backup-azure-sql-database/check-name.png)

  5. I **Serverroller** kontrollerar du att serverrollen **sysadmin** har valts. Klicka på **OK**. De behörigheter som krävs bör nu finnas.

      ![Kontrollera att serverrollen sysadmin har valts](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Nu associerar du databasen med Recovery Services-valvet. I Azure-portalen går du till listan **Skyddade servrar**, högerklickar på den server som är i ett felaktigt tillstånd > **Identifiera databaser på nytt**.

      ![Kontrollera att servern har rätt behörigheter](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Kontrollera förloppet i området **Meddelanden**. När de valda databaserna hittas visas ett meddelande.

      ![Meddelande som anger att distributionen lyckades](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Om SQL Server har flera instanser av SQL Server installerat måste du lägga till sysadmin-behörighet för **NT Service\AzureWLBackupPluginSvc-konto** i alla SQL-instanser.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Ge SQL sysadmin-behörigheter för SQL 2008 och SQL 2008 R2

Lägg till **NT AUTHORITY\SYSTEM-** och **NT-tjänst\AzureWLBackupPluginSvc-inloggningar** till SQL Server-instansen:

1. Gå till SQL Server-instansen i objektutforskaren.
2. Navigera till säkerhets-> inloggningar
3. Högerklicka på inloggningar och klicka på *Ny inloggning ...*

    ![Ny inloggning med SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Gå till fliken Allmänt och ange **NT AUTHORITY\SYSTEM** som inloggningsnamn.

    ![inloggningsnamn för SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Gå till *Serverroller* och välj *offentliga* och *sysadmin-roller.*

    ![välja roller i SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Gå till *Status*. *Bevilja* behörighet att ansluta till databasmotor och logga in som *aktiverat*.

    ![Bevilja behörigheter i SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Klicka på OK.
8. Upprepa samma sekvens med steg (1-7 ovan) för att lägga till NT-tjänst\AzureWLBackupPluginSvc-inloggning till SQL Server-instansen. Om inloggningen redan finns kontrollerar du att den har sysadmin-serverrollen och under Status har den behörighet att ansluta till databasmotorn och logga in som aktiverad.
9. När du har beviljat behörighet **återupptäcker du DBs** i portalen: **->** **->** Arbetsbelastning för säkerhetskopiering av arkivsäkerhet i Azure VM:

    ![Återupptäcka DBs i Azure-portalen](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Du kan också automatisera behörigheten genom att köra följande PowerShell-kommandon i administratörsläge. Instansnamnet är som standard INSTÄLLT på MSSQLSERVER. Ändra instansnamnsargumentet i skriptet om det behövs:

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om](backup-sql-server-database-azure-vms.md) säkerhetskopiering av SQL Server-databaser.
* [Lär dig om att](restore-sql-database-azure-vm.md) återställa säkerhetskopierade SQL Server-databaser.
* [Lär dig om att](manage-monitor-sql-database-backup.md) hantera säkerhetskopierade SQL Server-databaser.

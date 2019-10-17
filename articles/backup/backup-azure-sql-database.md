---
title: Säkerhetskopiera SQL Server-databaser till Azure
description: I den här självstudien beskrivs hur du säkerhetskopierar SQL Server till Azure. Den här artikeln beskriver även återställning av SQL Server.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: dacurwin
ms.openlocfilehash: 202d608e5d994cabd3d7e2e9a0887c8aab75af31
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72437833"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Om SQL Server-säkerhetskopiering i virtuella Azure-datorer

SQL Server-databaser är kritiska arbetsbelastningar som kräver ett lågt mål för återställningspunkt (RPO) och långsiktig kvarhållning. Du kan säkerhetskopiera SQL Server databaser som körs på virtuella Azure-datorer med [Azure Backup](backup-overview.md).

## <a name="backup-process"></a>Säkerhets kopierings process

Den här lösningen utnyttjar SQL-inbyggda API: er för att säkerhetskopiera SQL-databaser.

* När du har angett SQL Server VM som du vill skydda och fråga efter databaserna i den, kommer Azure Backup-tjänsten att installera ett tillägg för säkerhets kopiering av arbets belastning på den virtuella datorn med namnet `AzureBackupWindowsWorkload`-tillägget.
* Tillägget består av en koordinator och ett SQL-plugin-program. Även om koordinatorn ansvarar för att utlösa arbets flöden för olika åtgärder som att konfigurera säkerhets kopiering, säkerhets kopiering och återställning, är plugin-programmet ansvarigt för det faktiska data flödet.
* För att kunna identifiera databaser på den här virtuella datorn skapar Azure Backup kontot `NT SERVICE\AzureWLBackupPluginSvc`. Det här kontot används för säkerhets kopiering och återställning och kräver SQL sysadmin-behörigheter. Kontot `NT SERVICE\AzureWLBackupPluginSvc` är ett [virtuellt tjänst konto](https://docs.microsoft.com/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)och kräver därför inte någon lösen ords hantering. Azure Backup använder `NT AUTHORITY\SYSTEM`-kontot för databas identifiering/-förfrågan, så det här kontot måste vara en offentlig inloggning på SQL. Om du inte skapade SQL Server-datorn från Azure Marketplace kan det hända att du får felet **UserErrorSQLNoSysadminMembership**. Om det inträffar [följer du de här instruktionerna](#set-vm-permissions).
* När du har aktiverat konfigurera skydd för de valda databaserna konfigurerar säkerhets kopierings tjänsten koordinatorn med säkerhets kopierings scheman och annan princip information, som tillägget cachelagrar lokalt på den virtuella datorn.
* Vid den schemalagda tiden kommunicerar koordinatorn med plugin-programmet och börjar strömma säkerhetskopierade data från SQL-servern med VDI.  
* Plugin-programmet skickar data direkt till Recovery Services-valvet, vilket eliminerar behovet av en mellanlagringsplats. Data krypteras och lagras av Azure Backups tjänsten i lagrings konton.
* När data överföringen är slutförd bekräftar koordinatorn incheckningen med säkerhets kopierings tjänsten.

  ![Säkerhets kopierings arkitektur för SQL](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Innan du börjar

Innan du börjar ska du kontrol lera följande:

1. Se till att du har en SQL Server-instans som körs i Azure. Du kan [snabbt skapa en SQL Server-instans](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) på Marketplace.
2. Granska stöd för [funktions överväganden](#feature-consideration-and-limitations) och [scenariot](#scenario-support).
3. [Granska vanliga frågor](faq-backup-sql-server.md) om det här scenariot.

## <a name="scenario-support"></a>Scenariostöd

**Support** | **Detaljer**
--- | ---
**Distributioner som stöds** | Virtuella SQL Marketplace Azure-datorer och virtuella icke-Marketplace-datorer (manuellt installerat SQL Server) stöds.
**Geografiska områden som stöds** | Sydöstra Australien (ASE), östra Australien (AE), Australien, centrala (AC), Australien, centrala 2 (AC) <br> Brasilien, södra (BRS)<br> Kanada, centrala (CNC), Kanada, östra (CE)<br> Asien, sydöstra (SEA), Asien, östra (EA) <br> Östra USA (EUS), östra USA 2 (EUS2), västra centrala USA (WCUS), västra USA (WUS); Västra USA 2 (WUS 2) norra centrala USA (NCUS) centrala USA (CUS) södra centrala USA (SCUS) <br> Indien, centrala (INC), Indien, södra (moduler), västra Indien <br> Japan, östra (JPE), Japan, väst (JPW) <br> Korea, centrala (KRC), Korea, södra (KRS) <br> Nord Europa (NE), Västeuropa <br> Storbritannien, södra (UKS), Storbritannien, västra (UKW) <br> US Gov, Arizona, US Gov, Virginia, US Gov, Texas, US DoD, centrala, US DoD, östra <br> Tyskland, norra Tyskland, västra centrala <br> Schweiz, norra Schweiz, västra
**Operativsystem som stöds** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux stöds inte för närvarande.
**SQL Server-versioner som stöds** | SQL Server 2017 som beskrivs [här](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 och SPS som beskrivs [här](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014 SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.
**.NET-versioner som stöds** | .NET Framework 4.5.2 och senare installerat på den virtuella datorn

### <a name="support-for-sql-server-2008-and-sql-server-2008-r2"></a>Stöd för SQL Server 2008 och SQL Server 2008 R2

Azure Backup har nyligen lanserat stöd för [EOS SQL-servrar](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-2008-eos-extend-support) – SQL Server 2008 och SQL Server 2008 R2. Lösningen är för närvarande en för hands version för EOS SQL Server och har stöd för följande konfiguration:

1. SQL Server 2008 och SQL Server 2008 R2 som körs på Windows 2008 R2 SP1
2. .NET Framework 4.5.2 och senare måste installeras på den virtuella datorn
3. Säkerhets kopiering för FCI och speglade databaser stöds inte

Användarna kommer inte att debiteras för den här funktionen förrän den är allmänt tillgänglig. Alla andra [funktions överväganden och begränsningar](#feature-consideration-and-limitations) gäller även för dessa versioner. Se till att kraven är [uppfyllda](backup-sql-server-database-azure-vms.md#prerequisites) innan du konfigurerar skydd på SQL Server 2008 och 2008 R2.

## <a name="feature-consideration-and-limitations"></a>Funktions överväganden och begränsningar

* SQL Server säkerhets kopiering kan konfigureras i Azure Portal eller **PowerShell**. Vi stöder inte CLI.
* Lösningen stöds på båda typerna av [distributioner](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) – Azure Resource Manager virtuella datorer och klassiska virtuella datorer.
* Virtuell dator som kör SQL Server kräver Internet anslutning för att få åtkomst till offentliga Azure-IP-adresser.
* SQL Server **-FCI (failover Cluster instance)** och SQL Server Always on Cluster instance stöds inte.
* Säkerhets kopierings-och återställnings åtgärder för speglings databaser och ögonblicks bilder av databasen stöds inte.
* Om du använder fler än en säkerhets kopierings lösning för att säkerhetskopiera din fristående SQL Server instans eller SQL Always on-tillgänglighetsgruppen kan det leda till säkerhets kopierings problem. avstå från att göra det.
* Att säkerhetskopiera två noder i en tillgänglighets grupp individuellt med samma eller olika lösningar, kan också leda till att säkerhets kopieringen Miss lyckas.
* Azure Backup stöder endast fullständig och endast kopiering av fullständiga säkerhets kopierings typer för **skrivskyddade** databaser
* Databaser med ett stort antal filer kan inte skyddas. Det maximala antalet filer som stöds är **~ 1000**.  
* Du kan säkerhetskopiera till **~ 2000** SQL Server databaser i ett valv. Du kan skapa flera valv om du har ett större antal databaser.
* Du kan konfigurera säkerhets kopiering för upp till **50** databaser i en go; den här begränsningen hjälper till att optimera säkerhets kopierings belastning.
* Vi har stöd för databaser upp till **2 TB** i storlek. för större storlekar kan prestanda problem uppstå.
* För att du ska kunna se hur många databaser som kan skyddas per server måste vi överväga faktorer som bandbredd, VM-storlek, säkerhets kopierings frekvens, databas storlek osv. [Ladda ned](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) resurs planeraren som ger det ungefärliga antalet databaser som du kan ha per Server baserat på de virtuella dator resurserna och säkerhets kopierings principen.
* Om det gäller tillgänglighets grupper tas säkerhets kopiorna från de olika noderna utifrån några faktorer. Säkerhets kopierings beteendet för en tillgänglighets grupp sammanfattas nedan.

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Säkerhetskopiera beteende om Always on-tillgänglighetsgrupper

Vi rekommenderar att säkerhets kopian bara konfigureras på en nod i en AG. Säkerhets kopieringen ska alltid konfigureras i samma region som den primära noden. Med andra ord behöver du alltid den primära noden för att finnas i den region där du konfigurerar säkerhets kopieringen. Om alla noder i AG finns i samma region som säkerhets kopian har kon figurer ATS för, finns det inga problem.

#### <a name="for-cross-region-ag"></a>För en AG i flera regioner

* Oberoende av säkerhets kopierings inställningen sker inga säkerhets kopieringar från noderna som inte finns i samma region där säkerhets kopian har kon figurer ATS. Detta beror på att det inte finns stöd för säkerhets kopiering mellan regioner. Om du bara har två noder och den sekundära noden finns i den andra regionen, i det här fallet fortsätter säkerhets kopieringarna från den primära noden (om inte säkerhets kopierings inställningen är sekundär).
* Om ett fel uppstår i en annan region än den där säkerhets kopian har kon figurer ATS, Miss lyckas säkerhets kopieringarna på noderna i den felande regionen.

Beroende på vilka säkerhets kopierings-och säkerhets kopierings typer (fullständig/differentiell/Logga/kopiera endast fullständig), tas säkerhets kopiorna från en viss nod (primär/sekundär).

* **Säkerhets kopierings inställning: primär**

**Typ av säkerhets kopiering** | **Node**
    --- | ---
    Fullständig | Primär
    Differentiell | Primär
    Logg |  Primär
    Fullständig kopia |  Primär

* **Inställningar för säkerhets kopiering: endast sekundär**

**Typ av säkerhets kopiering** | **Node**
--- | ---
Fullständig | Primär
Differentiell | Primär
Logg |  Sekundär
Fullständig kopia |  Sekundär

* **Säkerhets kopierings inställning: sekundär**

**Typ av säkerhets kopiering** | **Node**
--- | ---
Fullständig | Primär
Differentiell | Primär
Logg |  Sekundär
Fullständig kopia |  Sekundär

* **Ingen säkerhets kopierings inställning**

**Typ av säkerhets kopiering** | **Node**
--- | ---
Fullständig | Primär
Differentiell | Primär
Logg |  Sekundär
Fullständig kopia |  Sekundär

## <a name="set-vm-permissions"></a>Ange VM-behörigheter

  När du kör identifieringen på en SQL Server gör Azure Backup följande:

* Lägger till AzureBackupWindowsWorkload-tillägget.
* Skapar ett NT SERVICE\AzureWLBackupPluginSvc-konto för att identifiera databaser på den virtuella datorn. Det här kontot används för att säkerhetskopiera och återställa och kräver SQL sysadmin-behörigheter.
* Identifierar databaser som körs på en virtuell dator, Azure Backup använder NT instans\system-kontot. Detta konto måste vara ett offentligt inloggnings konto på SQL.

Om du inte skapade SQL Server VM på Azure Marketplace eller om du är på SQL 2008 och 2008 R2, kan du få ett **UserErrorSQLNoSysadminMembership** -fel.

Om du vill ge behörighet i händelse av **SQL 2008** och **2008 R2** som körs på Windows 2008 R2 kan du läsa [här](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

För alla andra versioner måste du åtgärda behörigheterna med följande steg:

  1. Använd ett konto med SQL Server-sysadmin-behörighet för att logga in till SQL Server Management Studio (SSMS). Såvida du inte behöver specialbehörigheter bör Windows-autentisering fungera.
  2. På SQL Server öppnar du mappen **Security/Logins** (Säkerhet/Inloggningar).

      ![Öppna mappen Security/inloggningar för att se konton](./media/backup-azure-sql-database/security-login-list.png)

  3. Högerklicka på mappen **Logins** och välj **Ny inloggning**. I **Login - New** (Inloggning – ny) väljer du **Sök**.

      ![I dialogrutan Inloggning – ny väljer du Sök](./media/backup-azure-sql-database/new-login-search.png)

  4. Kontot för virtuell Windows-tjänst, **NT SERVICE\AzureWLBackupPluginSvc**, skapades under registreringen av den virtuell datorn och SQL-identifieringsfasen. Ange kontonamnet såsom det visas i **Ange ett objektnamn du vill markera**. Välj **Kontrollera namn** för att matcha namnet. Klicka på **OK**

      ![Välj Kontrollera namn för att matcha det okända tjänstnamnet](./media/backup-azure-sql-database/check-name.png)

  5. I **Serverroller** kontrollerar du att serverrollen **sysadmin** har valts. Klicka på **OK** De behörigheter som krävs bör nu finnas.

      ![Kontrollera att serverrollen sysadmin har valts](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Nu associerar du databasen med Recovery Services-valvet. I Azure-portalen går du till listan **Skyddade servrar**, högerklickar på den server som är i ett felaktigt tillstånd > **Identifiera databaser på nytt**.

      ![Kontrollera att servern har rätt behörigheter](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Kontrollera förloppet i området **Meddelanden**. När de valda databaserna hittas visas ett meddelande.

      ![Meddelande som anger att distributionen lyckades](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Om SQL Server har flera instanser av SQL Server installerade måste du lägga till sysadmin-behörighet för **NT Service\AzureWLBackupPluginSvc** -kontot till alla SQL-instanser.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Ge SQL-sysadmin-behörigheter för SQL 2008 och SQL 2008 R2

Lägg till **NT instans\system** -och **NT Service\AzureWLBackupPluginSvc** -inloggningar till SQL Server-instansen:

1. Gå SQL Server-instansen i Object Explorer.
2. Navigera till säkerhets-> inloggningar
3. Högerklicka på inloggningarna och klicka på *ny inloggning...*

    ![Ny inloggning med SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Gå till fliken Allmänt och ange **NT instans\system** som inloggnings namn.

    ![inloggnings namn för SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Gå till *Server roller* och välj roller för *offentliga* och *sysadmin* .

    ![välja roller i SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Gå till *status*. *Ge* behörighet att ansluta till databas motorn och logga in som *aktive rad*.

    ![Bevilja behörigheter i SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Klicka på OK.
8. Upprepa samma steg (1-7 ovan) för att lägga till NT Service\AzureWLBackupPluginSvc-inloggning till SQL Server-instansen. Om inloggningen redan finns kontrollerar du att den har Server rollen sysadmin och under status har den behörighet att ansluta till databas motorn och logga in som aktive rad.
9. När du har beviljat behörighet kan du **identifiera databaser** i portalen: valv **->** säkerhets kopierings infrastruktur **->** arbets belastning i virtuell Azure-dator:

    ![Identifiera databaser i Azure Portal](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Du kan också automatisera att ge behörighet genom att köra följande PowerShell-kommandon i administratörs läge. Instans namnet är inställt på MSSQLSERVER som standard. Ändra argumentet instans namn i skriptet om det behövs:

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

* [Lär dig mer om](backup-sql-server-database-azure-vms.md) att säkerhetskopiera SQL Server-databaser.
* [Lär dig om att](restore-sql-database-azure-vm.md) återställa säkerhetskopierade SQL Server-databaser.
* [Lär dig om att](manage-monitor-sql-database-backup.md) hantera säkerhetskopierade SQL Server-databaser.

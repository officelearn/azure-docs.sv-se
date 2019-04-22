---
title: Säkerhetskopiera SQL Server-databaser till Azure | Microsoft Docs
description: I den här självstudien beskrivs hur du säkerhetskopierar SQL Server till Azure. Den här artikeln beskriver även återställning av SQL Server.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: raynew
ms.openlocfilehash: d99a3d23959cfdd9bd068fbde3a882eb1bc9b4ae
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58847304"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Om SQL Server-säkerhetskopiering i virtuella Azure-datorer

SQL Server-databaser är kritiska arbetsbelastningar som kräver ett lågt mål för återställningspunkt (RPO) och långsiktig kvarhållning. Du kan säkerhetskopiera SQL Server-databaser som körs på virtuella Azure-datorer med hjälp av [Azure Backup](backup-overview.md).

## <a name="backup-process"></a>Säkerhetskopieringsprocessen

Den här lösningen utnyttjar SQL native API: erna för att göra säkerhetskopior av dina SQL-databaser.

* När du anger den SQL Server-dator som du vill skydda och fråga efter för databaser i it, Azure Backup-tjänsten installerar ett tillägg för säkerhetskopiering av arbetsbelastning på den virtuella datorn med namnet `AzureBackupWindowsWorkload`  tillägget.
* Det här tillägget består av en koordinator och ett SQL-plugin-program. Koordinatorn är ansvarig för att utlösa arbetsflöden för olika åtgärder som att konfigurera säkerhetskopiering, säkerhetskopiering och återställning, ansvarar plugin-programmet för faktiska dataflöde.
* Om du vill kunna identifiera databaser på den här virtuella datorn skapar Azure Backup-kontot `NT SERVICE\AzureWLBackupPluginSvc`. Det här kontot används för säkerhetskopiering och återställning och kräver SQL sysadmin-behörighet. Azure Backup använder den `NT AUTHORITY\SYSTEM` konto för database identifiering/förfrågan, så det här kontot måste vara en offentlig inloggning på SQL. Om du inte har skapat SQL Server-dator från Azure Marketplace, kan ett felmeddelande **UserErrorSQLNoSysadminMembership**. Om detta inträffar [följer du dessa instruktioner](backup-azure-sql-database.md).
* När du utlösaren Konfigurera skydd på de valda databaserna, ställer säkerhetskopieringstjänsten in koordinatorn med scheman för säkerhetskopiering och andra principinformation som tillägget cachelagrar lokalt på den virtuella datorn 
* På den schemalagda tiden koordinatorn kommunicerar med plugin-programmet som startar säkerhetskopierade data från SQLServer med VDI för direktuppspelning.  
* Plugin-programmet skickar data direkt till recovery services-valv, vilket eliminerar behovet av en mellanlagringsplats. Data krypteras och lagras av Azure Backup-tjänsten i storage-konton.
* När dataöverföringen har slutförts bekräftar coordinator genomförandet med säkerhetskopieringstjänsten.

  ![Arkitektur för SQL-säkerhetskopiering](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Innan du börjar

Innan du börjar bör du kontrollera följande:

1. Se till att du har en SQL Server-instans som körs i Azure. Du kan [snabbt skapa en SQL Server-instans](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) på Marketplace.
2. Granska den [funktionen övervägande](#feature-consideration-and-limitations) och [scenariot support](#scenario-support).
3. [Granska vanliga frågor](faq-backup-sql-server.md) om det här scenariot.

## <a name="scenario-support"></a>Scenariostöd

**Support** | **Detaljer**
--- | ---
**Distributioner som stöds** | Virtuella SQL Marketplace Azure-datorer och virtuella icke-Marketplace-datorer (manuellt installerat SQL Server) stöds.
**Geografiska områden som stöds** | Australien, sydöstra (ASE), Australien, östra (AE) <br> Brasilien, södra (BRS)<br> Canada Central (CNC), Canada East (CE)<br> Asien, sydöstra (SEA), Asien, östra (EA) <br> USA, östra (EUS), östra USA 2 (EUS2), västra centrala (WCUS), USA, västra (WUS); Västra USA 2 (WUS 2) norra centrala (NCUS) USA, centrala (CUS) södra centrala (SCUS) <br> Indien, centrala (INC), Indien, södra (INS) <br> Japan, östra (JPE), Japan, västra (JPW) <br> Korea Central (KRC), Korea South (KRS) <br> Europa, norra (NE), Europa, västra <br> Storbritannien, södra (UKS), Storbritannien, västra (UKW)
**Operativsystem som stöds** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux stöds inte för närvarande.
**SQL Server-versioner som stöds** | SQL Server 2017, SQL Server 2016, SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.
**.NET-versioner som stöds** | .NET framework 4.5.2 och senare har installerats på den virtuella datorn

## <a name="feature-consideration-and-limitations"></a>Funktionen överväganden och begränsningar

- SQL Server-säkerhetskopiering kan konfigureras i Azure-portalen eller **PowerShell**. Vi stöder inte CLI.
- Virtuell dator som kör SQL Server kräver en Internetanslutning för att komma åt Azure offentliga IP-adresser.
- SQL Server **instans för Failover-kluster (FCI)** och SQL Server Always på Redundansklusterinstans stöds inte.
- Åtgärder för säkerhetskopiering och återställning för speglade databaser och databasögonblicksbilder stöds inte.
- Med fler än en säkerhetskopieringslösningar för att säkerhetskopiera din fristående SQL Server kan-instans eller SQL Always on-tillgänglighetsgrupp leda till Säkerhetskopieringsfel; avstå från detta.
- Säkerhetskopiera två noder i en tillgänglighetsgrupp individuellt med samma eller olika lösningar kan även leda till Säkerhetskopieringsfel. Azure Backup kan identifiera och skydda alla noder som finns i samma region som valvet. Om din SQL Server Always on-tillgänglighetsgrupp sträcker sig över flera Azure-regioner, Ställ in säkerhetskopian från den region som har den primära noden. Azure Backup kan identifiera och skydda alla databaser i tillgänglighetsgruppen enligt din inställning för säkerhetskopiering.  
- Azure Backup stöder endast fullständiga och fullständig säkerhetskopiering med endast kopiering typer för **skrivskyddad** databaser
- Databaser med ett stort antal filer kan inte skyddas. Det maximala antalet filer som stöds är **~ 1000**.  
- Du kan säkerhetskopiera upp till **cirka 2 000** SQL Server-databaser i ett valv. Du kan skapa flera valv du har ett större antal databaser.
- Du kan konfigurera säkerhetskopiering för upp till **50** databaser i en gå; den här begränsningen hjälper till att optimera säkerhetskopierade belastning.
- Vi har stöd för databaser upp till **2TB** i storlek; som är större än den som prestandaproblem kan komma.
- För att få en uppfattning om hur många databaser kan skyddas per server ska behöver vi beakta faktorer, till exempel bandbredd, VM-storlek, säkerhetskopieringsfrekvens, databasens storlek, osv. Vi arbetar på en planner som kan hjälpa dig att beräkna dessa nummer på du äger. Vi kommer att publicera dem inom kort.
- När det gäller Tillgänglighetsgrupper är säkerhetskopior hämtade från de olika noderna baserat på ett antal faktorer. Säkerhetskopiering beteendet för en tillgänglighetsgrupp sammanfattas nedan.

### <a name="backup-behavior-in-case-of-always-on-availability-groups"></a>Säkerhetskopiering beteende när Always on-Tillgänglighetsgrupper

Beroende på inställning för säkerhetskopiering och säkerhetskopieringar typer (fullständig/differentiell/log/endast kopiering fullständig) kan hämtas säkerhetskopior från en särskild nod (primär/sekundär).

- **Inställning för säkerhetskopiering: Primär**

**Typ av säkerhetskopiering** | **Node**
    --- | ---
    Fullständig | Primär
    Differentiell | Primär
    Logga |  Primär
    Kopiera bara fullständig |  Primär

- **Inställning för säkerhetskopiering: Endast sekundär**

**Typ av säkerhetskopiering** | **Node**
--- | ---
Fullständig | Primär
Differentiell | Primär
Logga |  Sekundär
Kopiera bara fullständig |  Sekundär

- **Inställning för säkerhetskopiering: Secondary**

**Typ av säkerhetskopiering** | **Node**
--- | ---
Fullständig | Primär
Differentiell | Primär
Logga |  Sekundär
Kopiera bara fullständig |  Sekundär

- **Ingen inställning för säkerhetskopiering**

**Typ av säkerhetskopiering** | **Node**
--- | ---
Fullständig | Primär
Differentiell | Primär
Logga |  Sekundär
Kopiera bara fullständig |  Sekundär

## <a name="fix-sql-sysadmin-permissions"></a>Åtgärda SQL-sysadmin-behörigheter

  Om du behöver åtgärda behörigheter grund av felet **UserErrorSQLNoSysadminMembership** gör du följande:

  1. Använd ett konto med SQL Server-sysadmin-behörighet för att logga in till SQL Server Management Studio (SSMS). Såvida du inte behöver specialbehörigheter bör Windows-autentisering fungera.
  2. På SQL Server öppnar du mappen **Security/Logins** (Säkerhet/Inloggningar).

      ![Öppna mappen Security/inloggningar för att se konton](./media/backup-azure-sql-database/security-login-list.png)

  3. Högerklicka på mappen **Logins** och välj **Ny inloggning**. I **Login - New** (Inloggning – ny) väljer du **Sök**.

      ![I dialogrutan Inloggning – ny väljer du Sök](./media/backup-azure-sql-database/new-login-search.png)

  4. Kontot för virtuell Windows-tjänst, **NT SERVICE\AzureWLBackupPluginSvc**, skapades under registreringen av den virtuell datorn och SQL-identifieringsfasen. Ange kontonamnet såsom det visas i **Ange ett objektnamn du vill markera**. Välj **Kontrollera namn** för att matcha namnet. Klicka på **OK**.

      ![Välj Kontrollera namn för att matcha det okända tjänstnamnet](./media/backup-azure-sql-database/check-name.png)

  5. I **Serverroller** kontrollerar du att serverrollen **sysadmin** har valts. Klicka på **OK**. De behörigheter som krävs bör nu finnas.

      ![Kontrollera att serverrollen sysadmin har valts](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Nu associerar du databasen med Recovery Services-valvet. I Azure-portalen går du till listan **Skyddade servrar**, högerklickar på den server som är i ett felaktigt tillstånd > **Identifiera databaser på nytt**.

      ![Kontrollera att servern har rätt behörigheter](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Kontrollera förloppet i området **Meddelanden**. När de valda databaserna hittas visas ett meddelande.

      ![Meddelande som anger att distributionen lyckades](./media/backup-azure-sql-database/notifications-db-discovered.png)


## <a name="next-steps"></a>Nästa steg

- [Lär dig mer om](backup-sql-server-database-azure-vms.md) säkerhetskopiering av SQL Server-databaser.
- [Lär dig om att](restore-sql-database-azure-vm.md) återställa säkerhetskopierade SQL Server-databaser.
- [Lär dig om att](manage-monitor-sql-database-backup.md) hantera säkerhetskopierade SQL Server-databaser.

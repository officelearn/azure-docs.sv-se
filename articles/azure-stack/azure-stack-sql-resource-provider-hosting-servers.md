---
title: SQL som är värd för servrar i Azure Stack | Microsoft Docs
description: Hur du lägger till SQL-instanser för etablering via SQL nätverkskort resursprovidern.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: f1b39601f0f6d254daadfafdd812549c6c95ed35
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239476"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Lägg till värdservrar för SQL-resursprovider

Du kan ha en SQL-instans på en virtuell dator (VM) i [Azure Stack](azure-stack-poc.md), eller på en virtuell dator utanför Azure Stack-miljön, så länge som SQL-resursprovider kan ansluta till instansen.

> [!NOTE]
> SQL-databaser ska skapas på SQL resource provider-servern. SQL-resursprovider ska skapas i standard-providerprenumeration medan SQL värdservrar ska skapas i en fakturerbar, användarprenumeration. Resource provider-servern bör inte användas till att värden användardatabaser.

## <a name="overview"></a>Översikt

Granska följande obligatoriska och allmänna krav innan du lägger till en SQL på värdservern.

### <a name="mandatory-requirements"></a>Obligatoriskt krav

* Aktivera SQL-autentisering på SQL Server-instansen. Eftersom SQL-resursprovider VM inte är ansluten till en domän, kan den bara ansluta till en värdservern med SQL-autentisering.
* Konfigurera IP-adresser för SQL-instanser som offentliga när installerade på Azure Stack. Resursprovidern och användare, t.ex Webbappar, kommunicera över nätverket användaren så anslutning till SQL-instansen på det här nätverket krävs.

### <a name="general-requirements"></a>Allmänna krav

* Anger den SQL-instansen för användning av resurs-providern och användaren arbetsbelastningar. Du kan inte använda en SQL-instans som används av andra konsument. Den här begränsningen gäller även för App Services.
* Konfigurera ett konto med rätt behörighetsnivåer för resursprovidern (beskrivs nedan).
* Du ansvarar för att hantera SQL-instanser och deras värdar.  Till exempel resursprovidern inte tillämpa uppdateringar, hantera säkerhetskopior eller hantera rotation av autentiseringsuppgifter.

### <a name="sql-server-virtual-machine-images"></a>SQL Server-avbildningar

SQL IaaS avbildningar av virtuella datorer är tillgängliga via Marketplace-hanteringsfunktionen. Dessa avbildningar är samma som den virtuella SQL-datorer som är tillgängliga i Azure.

Se till att du alltid hämta den senaste versionen av den **SQL IaaS-tillägget** innan du distribuerar en SQL-VM med hjälp av ett Marketplace-objekt. IaaS-tillägget och motsvarande portal förbättringar erbjuder ytterligare funktioner, till exempel automatisk uppdatering och säkerhetskopiering. Mer information om det här tillägget finns i [automatisera hanteringsuppgifter på Azure virtuella datorer med SQL Server Agent-tillägget](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

> [!NOTE]
> SQL IaaS-tillägget är _krävs_ för alla SQL på Windows-avbildningar i marketplace; inte den virtuella datorn ska distribueras om du inte gick att hämta tillägget. Den används inte med Linux-baserade SQL avbildningar av virtuella datorer.

Det finns andra alternativ för att distribuera SQL virtuella datorer, inklusive mallar i den [Azure Stack-Snabbstartsgalleriet](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Värd-servrar som installerats på en Azure Stack för flera noder måste skapas från en användarprenumeration och inte som standard providern prenumerationen. De måste skapas från användarportalen eller från en PowerShell-session med en lämplig inloggning. Alla värdservrar är fakturerbara virtuella datorer och måste ha rätt SQL-licenser. Tjänstadministratören _kan_ vara ägare till den prenumerationen.

### <a name="required-privileges"></a>Behörigheter som krävs

Du kan skapa en administrativ användare med lägre behörighet än en SQL-sysadmin. Användaren behöver bara behörigheter för följande åtgärder:

* Databas: Skapa, Alter, med inneslutning (för Always On endast), ta bort, säkerhetskopiera
* Tillgänglighetsgruppen: ALTER, ansluta, Lägg till/ta bort databasen
* Inloggning: Create, Select, Alter, Drop, Revoke
* Välj köras: \[master\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn), sys.availability_replicas (AlwaysOn), sys.databases, \[master\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[master\].\[ sys\].\[ availability_groups\] (AlwaysOn), sys.master_files

### <a name="additional-security-information"></a>Ytterligare säkerhetsinformation

Följande information innehåller ytterligare vägledning:

* Alla Azure Stack-lagring krypteras med BitLocker, så att alla SQL-instans på Azure Stack använder krypterad blob storage.
* SQL-Resursprovider har fullständigt stöd för TLS 1.2. Kontrollera att SQL Server som hanteras via SQL RP är konfigurerad för att TLS 1.2 _endast_ och den ansvariga Personen som standard som. Alla versioner av SQL Server-stöd TLS 1.2 Se [TLS 1.2-stöd för Microsoft SQL Server](https://support.microsoft.com/en-us/help/3135244/tls-1-2-support-for-microsoft-sql-server).
* Använd SQL Server Configuration Manager för att ange den **ForceEncryption** krypteras alltid alternativet för att se till att all kommunikation till SQLServer. Se [att konfigurera servern för att tvinga krypterade anslutningar](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017#ConfigureServerConnections).
* Se till att klientprogram kommunicerar också över en krypterad anslutning.
* RP: N har konfigurerats för att lita på certifikat som används av SQL Server-instanserna.

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Ange kapacitet genom att ansluta till en fristående värd för SQLServer

Du kan använda fristående (icke-hög tillgänglighet) SQL-servrar med hjälp av en utgåva av SQL Server 2014 eller SQL Server 2016. Kontrollera att du har autentiseringsuppgifterna för ett konto med behörighet som systemadministratör.

Följ dessa steg för att lägga till en fristående värd-server som redan har ställts in:

1. Logga in på Azure Stack-operator-portalen som tjänstadministratör.

2. Välj **alla tjänster** &gt; **ADMINISTRATIONSRESURSER** &gt; **som är värd för SQL-servrar**.

   ![SQL Hosting Servers](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   Under **som är värd för SQL-servrar**, kan du ansluta SQL-resursprovider till instanser av SQL Server som fungerar som serverdel för provider för nätverksresurser.

   ![SQL Adapter instrumentpanel](./media/azure-stack-sql-rp-deploy/sqlrp-hostingserver.png)

3. Klicka på **Lägg till** och ange anslutningsinformationen för SQL Server-instansen på den **lägga till en SQL Server som är värd för** bladet.

   ![Lägg till en SQL på värdservern](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Du kan också ange instansnamn och ange ett portnummer om instansen inte är tilldelad standardporten 1433.

   > [!NOTE]
   > Så länge som SQL-instansen kan användas av användare och Azure Resource Manager-administratör, kan den placeras under kontroll av resursprovidern. SQL-instansen __måste__ allokeras exklusivt till resursprovidern.

4. När du lägger till servrar, måste du tilldela dem till en befintlig SKU eller skapa en ny SKU. Under **lägga till en Server som är värd för**väljer **SKU: er**.

   * Välj en tillgänglig SKU för att använda en befintlig SKU, och välj sedan **skapa**.
   * Om du vill skapa en SKU, Välj **+ skapa nya SKU: N**. I **skapa SKU**, ange informationen som krävs och välj sedan **OK**.

     ![Skapa en SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Ge hög tillgänglighet med hjälp av SQL Always On-Tillgänglighetsgrupper

Konfigurera SQL Always On-instanser kräver ytterligare åtgärder och kräver tre virtuella datorer (eller fysiska datorer.) Den här artikeln förutsätter att du redan har en djupare förståelse för Always On-Tillgänglighetsgrupper. Mer information finns i följande artiklar:

* [Introduktion till SQL Server Always On-Tillgänglighetsgrupper på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Always On-Tillgänglighetsgrupper (SQLServer)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> SQL-adapter resursprovider _endast_ stöder SQL 2016 SP1 Enterprise eller senare instanser för Always On Tillgänglighetsgrupper. Den här konfigurationen för nätverkskort kräver nya SQL-funktioner som automatisk seeding.

### <a name="automatic-seeding"></a>Automatisk seeding

Du måste aktivera [automatisk Seeding](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) på varje tillgänglighetsgrupp för varje instans av SQL Server.

Om du vill aktivera automatisk seeding på alla instanser, redigera och kör följande SQL-kommando på den primära repliken för varje sekundär instans:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<secondary_node>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

Tillgänglighetsgruppen måste omges av hakparenteser.

Kör följande SQL-kommando på de sekundära noderna:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="configure-contained-database-authentication"></a>Konfigurera innesluten databas-autentisering

Innan du lägger till en innesluten databas till en tillgänglighetsgrupp, kontrollerar du att alternativet innesluten databas authentication server är inställd på 1 på varje server-instansen som är värd för en tillgänglighetsreplik för tillgänglighetsgruppen. Mer information finns i [innehöll Databasautentisering Serverkonfigurationsalternativet](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Med dessa kommandon anger alternativet innesluten databas authentication server för varje instans:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>Att lägga till SQL Always On som är värd för servrar

1. Logga in på Azure Stack-administrationsportalen som en tjänst.

2. Välj **Bläddra** &gt; **administrativa resurser** &gt; **SQL som är värd för servrar** &gt; **+ Lägg till**.

   Under **som är värd för SQL-servrar**, kan du ansluta SQL Server-Resursleverantör till själva instanserna av SQL Server som fungerar som serverdel för provider för nätverksresurser.

3. Fyll i formuläret med anslutningsinformationen för SQL Server-instansen. Se till att du använder FQDN-adressen för alltid-lyssnare för (och valfritt antal och instans-portnamn). Ange information för det konto som du konfigurerat med sysadmin-behörighet.

4. Markera rutan Always On Availability Group om du vill aktivera stöd för SQL Always On Availability Group-instanser.

   ![Aktivera alltid på](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Lägg till SQL Always On-instans till en SKU.

   > [!IMPORTANT]
   > Du kan inte blanda fristående servrar med Always On-instanser i samma SKU. Försök att blanda typer när du lägger till de första värdbaserade servern resulterar i ett fel.

## <a name="sku-notes"></a>SKU-information
Använd en SKU-namn som beskriver funktionerna i servrar i SKU: N, till exempel kapacitet och prestanda. Namnet fungerar som en hjälp för att hjälpa användare att distribuera sina databaser till lämplig SKU: N. Du kan till exempel använda SKU-namn för att skilja Tjänsterbjudanden med följande egenskaper:
  
* hög kapacitet
* höga prestanda
* hög tillgänglighet

Som bästa praxis, bör värdservrar i en SKU ha samma resurs- och egenskaper.

SKU: er kan inte tilldelas till specifika användare eller grupper.

SKU: er kan ta upp till en timme att bli synliga i portalen. Användare kan inte skapa en databas tills SKU: N är färdigt.

Om du vill redigera en SKU, gå till **alla tjänster** > **SQL Adapter** > **SKU: er**. Välj SKU för att ändra, gör nödvändiga ändringar och klicka på **spara** att spara ändringarna. Om du vill ta bort en SKU som inte längre behövs, gå till **alla tjänster** > **SQL Adapter** > **SKU: er**. Högerklicka på SKU-namnet och välj **ta bort** att ta bort den.

> [!TIP]
> Du kan redigera eller ta bort SQL-providern resurskvoter på samma plats.

## <a name="make-sql-databases-available-to-users"></a>Gör SQL-databaser som är tillgängliga för användare

Skapa planer och erbjudanden att tillgängliggöra SQL-databaser för användare. Lägg till den **Microsoft.SqlAdapter** service för att planen och skapa en ny kvot.

## <a name="next-steps"></a>Nästa steg

[Lägg till databaser](azure-stack-sql-resource-provider-databases.md)

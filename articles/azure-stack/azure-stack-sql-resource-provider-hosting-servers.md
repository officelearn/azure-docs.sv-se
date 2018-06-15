---
title: SQL som värd för servrar på Azure-stacken | Microsoft Docs
description: Hur du lägger till SQL-instanser för att etablera via Resource Provider för SQL-kort
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
ms.date: 05/18/2018
ms.author: jeffgilb
ms.openlocfilehash: e08c0bfd3cbed64f5042e469801e20c913c2f70e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359432"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Lägg till värdservrar för SQL-resursprovidern
Du kan använda SQL-instanser på virtuella datorer i i din [Azure Stack](azure-stack-poc.md), eller en instans utanför din Azure Stack-miljö tillhandahålls resursprovidern kan ansluta till den. Allmänna krav är:

* SQL-instansen måste vara dedikerade för användning av arbetsbelastningar RP och användare. Du kan inte använda en SQL-instans som används av några andra konsumenten, inklusive Apptjänster.
* SQL-resursprovidern VM är inte ansluten till en domän och kan bara ansluta med SQL-autentisering.
* Du måste konfigurera ett konto med rätt behörighet för användning av resursprovidern.
* Resursprovidern och användare, till exempel Web Apps använda nätverkets användare, så krävs anslutning till SQL-instans på det här nätverket. Det här kravet innebär vanligtvis IP-Adressen för din SQL-instanser måste vara i ett offentligt nätverk.
* Hantering av SQL-instanser och deras värdar är; resursprovidern matchar inte utföra uppdatering, säkerhetskopiering, autentiseringsuppgifter rotation, osv.
* SKU: er kan användas för att skapa olika klasser av SQL-funktioner, till exempel prestanda, alltid på osv.

Ett antal SQL IaaS avbildningar av virtuella datorer är tillgängliga via Marketplace-hanteringsfunktionen. Se till att du alltid ladda ned den senaste versionen av den **SQL IaaS tillägget** innan du distribuerar en virtuell dator med hjälp av en Marketplace-objektet. SQL-avbildningar är desamma som de SQL virtuella datorer som är tillgängliga i Azure. Tillhandahåller funktioner som automatisk uppdatering och funktionerna för säkerhetskopiering för SQL virtuella datorer skapas från dessa avbildningar IaaS-tillägget och motsvarande portalen förbättringar.

Det finns andra alternativ för att distribuera SQL virtuella datorer, inklusive mallar i den [Azure Stack Snabbstartsgalleriet](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Värd-servrar som installerats på ett flernodigt Azure-stacken måste skapas från en prenumeration för användaren. De kan inte skapas från prenumerationen Standard Provider. De måste skapas från användarportalen eller från en PowerShell-session med en lämplig inloggning. Alla värdservrar avgiftsbelagda virtuella datorer och måste ha rätt SQL-licenser. Tjänstens _kan_ vara ägare till den prenumerationen.


### <a name="required-privileges"></a>Behörigheter som krävs

Du kan skapa en ny administrativ användare med mindre än fullständig sysadmin-behörigheter. Åtgärderna som ska tillåtas är:

- Databas: Skapa, ändra släpp säkerhetskopiering med inneslutning (alltid på endast),
- Tillgänglighetsgruppen: Alter, ansluta, Lägg till/ta bort databasen
- Inloggning: Skapa, Välj, Alter, Drop, återkalla
- Välj Operations: \[master\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn) sys.availability_replicas (AlwaysOn), sys.databases, \[master\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[master\].\[ sys\].\[ availability_groups\] (AlwaysOn) sys.master_files



## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Ange kapacitet genom att ansluta till en fristående värd för SQLServer
Du kan använda fristående (icke-hög tillgänglighet) SQL-servrar med hjälp av en utgåva av SQL Server 2014 eller SQL Server 2016. Kontrollera att du har autentiseringsuppgifter för ett konto med systemadministratörsprivilegier.

Följ dessa steg för att lägga till en fristående värd server som redan har etablerats:

1. Logga in på administrationsportalen för Azure-stacken som tjänstadministratör

2. Klicka på **Bläddra** &gt; **administrativa resurser** &gt; **SQL som värd för servrar**.

  ![](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

  Den **värd för SQL-servrar** bladet är där du kan ansluta Resource Provider för SQL Server till faktiska instanser av SQL Server som fungerar som den resursprovidern backend.

  ![Värdservrar](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. Fyll i formuläret med anslutningsinformationen för SQL Server-instansen.

  ![Nya värdservern](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Du kan du också inkludera ett instansnamn och ett portnummer som kan tillhandahållas om instansen inte har tilldelats standardporten 1433.

  > [!NOTE]
  > Så länge som SQL-instans kan användas av användare och Azure Resource Manager-administratören, kan den placeras under kontroll av resursprovidern. SQL-instansen __måste__ tilldelas enbart RP.

4. När du lägger till servrar måste du tilldela dem till en ny eller befintlig SKU att skilja Tjänsterbjudanden. Du kan till exempel ha en SQL Enterprise instans tillhandahåller:
  - databaskapacitet
  - automatisk säkerhetskopiering
  - reservera högpresterande servrar för olika avdelningar
  - och så vidare.

  SKU-namnet bör avspegla egenskaper så att användarna kan placera sina databaser på lämpligt sätt. Alla värdservrar i en SKU ska ha samma funktioner.

> [!IMPORTANT]
> Specialtecken, inklusive blanksteg och punkter, stöds inte i den **familj** eller **nivå** namn när du skapar en SKU för SQL och MySQL-resursprovidrar.

Ett exempel:

![SKU:er](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

>[!NOTE]
> SKU: er kan ta upp till en timme att vara synliga i portalen. Användare kan inte skapa en databas tills SKU: N skapas fullständigt.

## <a name="provide-capacity-using-sql-always-on-availability-groups"></a>Ange kapacitet med hjälp av SQL Always On-Tillgänglighetsgrupper
Konfigurera SQL Always On instanser kräver ytterligare åtgärder och omfattar minst tre virtuella datorer (eller fysiska datorer).

> [!NOTE]
> SQL-kortet RP _endast_ stöder SQL 2016 SP1 Enterprise eller senare instanser för alltid på, eftersom den kräver nya SQL-funktioner som automatisk seeding. Utöver föregående vanliga lista över kraven:

Du måste specifikt aktivera [automatisk Seeding](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) på varje tillgänglighetsgrupp för varje instans av SQL Server:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON 'InstanceName'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

Använd följande SQL-kommandon på sekundär instanser:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

Så här lägger du till SQL alltid på värdservrar:

1. Logga in på administrationsportalen för Azure-stacken som en tjänstadministratör

2. Klicka på **Bläddra** &gt; **administrativa resurser** &gt; **SQL som värd för servrar** &gt; **+ Lägg till**.

    Den **värd för SQL-servrar** bladet är där du kan ansluta Resource Provider för SQL Server till faktiska instanser av SQL Server som fungerar som den resursprovidern backend.

3. Fyll i formuläret med anslutningsinformationen för SQL Server-instansen, som till att använda FQDN-adressen för alltid på lyssnare (och valfritt portnummer). Ange kontoinformationen för det konto som du har konfigurerat med systemadministratörsprivilegier.

4. Den här kryssrutan om du vill aktivera stöd för instanser av SQL Always On-Tillgänglighetsgruppen.

    ![Värdservrar](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Lägga till en SQL Always On-instans till en SKU. 

> [!IMPORTANT]
> Du kan blanda fristående servrar med Always On-instanser i samma SKU: N. Ett försök görs att blanda typer när du lägger till de första värd servern resulterar i ett fel.


## <a name="making-sql-databases-available-to-users"></a>Göra SQL-databaser som är tillgängliga för användare

Skapa planer och erbjudanden om du vill göra SQL-databaser som är tillgängliga för användare. Lägg till tjänsten Microsoft.SqlAdapter i planen och lägga till en befintlig kvot eller skapa en ny. Om du skapar en kvot kan ange du kapacitet för att tillåta användaren.

![Skapa planer och erbjudanden om du vill inkludera databaser](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)


## <a name="next-steps"></a>Nästa steg

[Lägga till databaser](azure-stack-sql-resource-provider-databases.md)

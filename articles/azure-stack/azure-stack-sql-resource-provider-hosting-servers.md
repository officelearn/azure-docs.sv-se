---
title: "SQL som värd för servrar på Azure-stacken | Microsoft Docs"
description: "Hur du lägger till SQL-instanser för att etablera via Resource Provider för SQL-kort"
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: JeffGo
ms.openlocfilehash: da76eaf92bf27195b4f1780511818a7689300f66
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2017
---
# <a name="add-hosting-servers-for-use-by-the-sql-adapter"></a>Lägg till värdservrar för användning av SQL-kort

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan använda SQL-instanser på virtuella datorer i i din [Azure Stack](azure-stack-poc.md), eller en instans utanför din Azure Stack-miljö tillhandahålls resursprovidern kan ansluta till den. Allmänna krav är:

* SQL-instansen måste vara dedikerade för användning av arbetsbelastningar RP och användare. Du kan inte använda en SQL-instans som används av några andra konsumenten, inklusive Apptjänster.
* RP-nätverkskort är inte ansluten till en domän och kan bara ansluta med SQL-autentisering.
* Du måste konfigurera ett konto med rätt behörighet för användning av RP.
* RP och användare, till exempel Web Apps använda nätverkets användare, så krävs anslutning till SQL-instans på det här nätverket. Det här kravet innebär vanligtvis IP-Adressen för din SQL-instanser måste vara i ett offentligt nätverk.
* Hantering av SQL-instanser och deras värdar är; RP matchar inte utföra uppdatering, säkerhetskopiering, autentiseringsuppgifter rotation, osv.
* SKU: er kan användas för att skapa olika klasser av SQL-funktioner, till exempel prestanda, alltid på osv.


Ett antal SQL IaaS avbildningar av virtuella datorer är tillgängliga via Marketplace-hanteringsfunktionen. Se till att du alltid den senaste versionen av SQL IaaS tillägget innan du distribuerar en virtuell dator med hjälp av en Marketplace-objektet. SQL-avbildningar är desamma som de SQL virtuella datorer som är tillgängliga i Azure. Tillhandahåller funktioner som automatisk uppdatering och funktionerna för säkerhetskopiering för SQL virtuella datorer skapas från dessa avbildningar IaaS-tillägget och motsvarande portalen förbättringar.

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

    Ett exempel:

![SKU:er](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

>[!NOTE]
> SKU: er kan ta upp till en timme att vara synliga i portalen. Användare kan inte skapa en databas tills SKU: N skapas fullständigt.

## <a name="provide-capacity-using-sql-always-on-availability-groups"></a>Ange kapacitet med hjälp av SQL Always On-Tillgänglighetsgrupper
Konfigurera SQL Always On instanser kräver ytterligare åtgärder och omfattar minst tre virtuella datorer (eller fysiska datorer).

> [!NOTE]
> SQL-kortet RP _endast_ stöder SQL 2016 SP1 Enterprise eller senare instanser för alltid på, eftersom den kräver nya SQL-funktioner som automatisk seeding. Utöver föregående vanliga lista över kraven:

* Du måste ange en filserver förutom SQL Always On-datorer. Det finns en [Azure Stack Quickstart mallen](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-ha) som kan skapa den här miljön för dig. Det kan också användas som en guide för att skapa din egen instans.

* Du måste konfigurera SQL-servrar. Du måste specifikt aktivera [automatisk Seeding](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) på varje tillgänglighetsgrupp för varje instans av SQL Server.

```
ALTER AVAILABILITY GROUP [<availability_group_name>]
    MODIFY REPLICA ON 'InstanceName'
    WITH (SEEDING_MODE = AUTOMATIC)
GO
```

På sekundära instanser
```
ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
GO

```



Så här lägger du till SQL alltid på värdservrar:

1. Logga in på administrationsportalen för Azure-stacken som en tjänstadministratör

2. Klicka på **Bläddra** &gt; **administrativa resurser** &gt; **SQL som värd för servrar** &gt; **+ Lägg till**.

    Den **värd för SQL-servrar** bladet är där du kan ansluta Resource Provider för SQL Server till faktiska instanser av SQL Server som fungerar som den resursprovidern backend.


3. Fyll i formuläret med anslutningsinformationen för SQL Server-instansen, som till att använda FQDN eller IPv4-adress för alltid på lyssnare (och valfritt portnummer). Ange kontoinformationen för det konto som du har konfigurerat med systemadministratörsprivilegier.

4. Den här kryssrutan om du vill aktivera stöd för instanser av SQL Always On-Tillgänglighetsgruppen.

    ![Värdservrar](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Lägga till en SQL Always On-instans till en SKU. Du kan blanda fristående servrar med Always On-instanser i samma SKU: N. Som bestäms när du lägger till den första värdservern. Försök att blanda typer efteråt resulterar i ett fel.


## <a name="making-sql-databases-available-to-users"></a>Göra SQL-databaser som är tillgängliga för användare

Skapa planer och erbjudanden om du vill göra SQL-databaser som är tillgängliga för användare. Lägg till tjänsten Microsoft.SqlAdapter i planen och lägga till en befintlig kvot eller skapa en ny. Om du skapar en kvot kan ange du kapacitet för att tillåta användaren.

![Skapa planer och erbjudanden om du vill inkludera databaser](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="maintenance-of-the-sql-adapter-rp"></a>Underhåll av SQL-kortet RP

Underhåll av SQL-instanser omfattas inte här, förutom rotation lösenordsinformation. Du är ansvarig för uppdatering och säkerhetskopiering/återställning av database-servrar som används med SQL-kort.

### <a name="patching-and-updating"></a>Uppdateringsverktyg och uppdatera
 SQL-kortet servas inte som en del av Azure-stacken eftersom det är en tilläggskomponent. Microsoft tillhandahåller uppdateringar till SQL-kort efter behov. SQL-Adapter instansieras på en _användaren_ virtuell dator i prenumerationen Standard Provider. Därför är det nödvändigt att ange Windows korrigeringsfiler, antivirus signaturer osv. Windows uppdateringspaket som tillhandahålls som en del av korrigeringsfil och uppdateringscykeln kan användas för att installera uppdateringar för Windows-VM. När en uppdaterad adapter släpps, tillhandahålls ett skript för att installera uppdateringen. Det här skriptet skapar en ny RP VM och migrera några tillstånd som du redan har.

 ### <a name="backuprestoredisaster-recovery"></a>Säkerhetskopiering/återställning/Disaster Recovery
 SQL-kortet säkerhetskopieras inte som en del av Azure Stack BC-DR-processen, eftersom det är en tilläggskomponent. Skript kommer att tillhandahållas för att underlätta:
- Säkerhetskopiering av nödvändiga statusinformation (som lagras i ett lagringskonto i Azure-Stack)
- Återställer RP återställning av en fullständig stack blir nödvändigt.
Databasservrar måste återställas först (om det behövs), innan RP återställs.

### <a name="updating-sql-credentials"></a>Uppdatera autentiseringsuppgifterna för SQL

Du ansvarar för att skapa och underhålla system administratörskonton på SQL-servrar. RP måste ha ett konto med dessa behörigheter för att hantera databaser åt användare - behöver inte åtkomst till data i dessa databaser. Om du behöver uppdatera sa-lösenord på SQL-servrar använda du update möjligheterna för den RP administratörsgränssnittet för att ändra det lagrade lösenordet som används av RP. Lösenord lagras i ett Nyckelvalv på Azure Stack-instansen.

Om du vill ändra inställningarna klickar du på **Bläddra** &gt; **ADMINISTRATIONSRESURSER** &gt; **värd för SQL-servrar** &gt; **SQL-inloggningar** och välj ett inloggningsnamn. Den måste ändras för SQL-instans först (och alla repliker, om det behövs). I den **inställningar** panelen, klickar du på **lösenord**.

![Uppdatera administratörslösenordet](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)


## <a name="next-steps"></a>Nästa steg

[Lägga till databaser](azure-stack-sql-resource-provider-databases.md)

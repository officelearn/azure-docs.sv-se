---
title: SQL som värd för servrar på Azure-stacken | Microsoft Docs
description: Hur du lägger till SQL-instanser för att etablera via SQL kortet resursprovidern.
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
ms.date: 06/27/2018
ms.author: jeffgilb
ms.openlocfilehash: af820f90c5d8822dbdaa768b16360d534fd47828
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060050"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Lägg till värdservrar för SQL-resursprovidern

Du kan vara värd för en SQL-instans på en virtuell dator (VM) i [Azure Stack](azure-stack-poc.md), eller på en virtuell dator utanför Azure-stacken miljö, så länge resursprovidern SQL kan ansluta till instansen.

## <a name="overview"></a>Översikt

Granska följande obligatoriska och allmänna krav innan du lägger till en SQL på värdservern.

**Obligatoriska krav**

* Aktivera SQL-autentisering för SQL Server-instansen. Eftersom SQL-resursprovidern VM inte är ansluten till en domän, kan den bara ansluta till en värdservern med SQL-autentisering.
* Konfigurera IP-adresser för SQL-instanser som offentliga. Resursprovidern och användare, till exempel Web Apps kommunicerar via nätverket användaren så att anslutningen till SQL-instans på det här nätverket krävs.

**Allmänna krav**

* Tilldela SQL-instansen för användning av resource provider och arbetsbelastningar. Du kan inte använda en SQL-instans som används av andra konsument. Den här begränsningen gäller även för Apptjänster.
* Konfigurera ett konto med rätt behörighetsnivåer för resursprovidern.
* Du ansvarar för att hantera SQL-instanser och deras värdar.  Till exempel resursprovidern inte tillämpa uppdateringar, hantera säkerhetskopieringar och hantera autentiseringsuppgifter rotation.

### <a name="sql-server-virtual-machine-images"></a>SQL Server-avbildningar för virtuell dator

SQL-IaaS avbildningar av virtuella datorer är tillgängliga via Marketplace-hanteringsfunktionen. Dessa avbildningar är desamma som de SQL virtuella datorer som är tillgängliga i Azure.

Se till att du alltid ladda ned den senaste versionen av den **SQL IaaS tillägget** innan du distribuerar en SQL VM som använder en Marketplace-objektet. IaaS-filtillägg och motsvarande portalen förbättringar tillhandahålla ytterligare funktioner såsom automatisk uppdatering och säkerhetskopiering. Mer information om det här tillägget finns [automatisera hanteringsuppgifter på Azure Virtual Machines med SQL Server Agent tillägget](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

Det finns andra alternativ för att distribuera SQL virtuella datorer, inklusive mallar i den [Azure Stack Snabbstartsgalleriet](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Värd-servrar som installerats på ett flernodigt Azure-stacken måste skapas från en prenumeration för användaren. De kan inte skapas från prenumerationen Standard Provider. De måste skapas från användarportalen eller från en PowerShell-session med en lämplig inloggning. Alla värdservrar fakturerbar virtuella datorer och måste ha rätt SQL-licenser. Tjänstens _kan_ vara ägare till den prenumerationen.

### <a name="required-privileges"></a>Behörigheter som krävs

Du kan skapa en administrativ användare med lägre behörighet än en SQL-sysadmin. Användaren bara behöver behörighet för följande åtgärder:

* Databas: Skapa, ändra, med inneslutning (för Always On endast), Drop, säkerhetskopiera
* Tillgänglighetsgruppen: Alter, ansluta, Lägg till/ta bort databasen
* Inloggning: Skapa, Välj, Alter, Drop, återkalla
* Välj Operations: \[master\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn) sys.availability_replicas (AlwaysOn), sys.databases, \[master\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[master\].\[ sys\].\[ availability_groups\] (AlwaysOn) sys.master_files

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Ange kapacitet genom att ansluta till en fristående värd för SQLServer

Du kan använda fristående (icke-hög tillgänglighet) SQL-servrar med hjälp av en utgåva av SQL Server 2014 eller SQL Server 2016. Kontrollera att du har autentiseringsuppgifter för ett konto med systemadministratörs-behörighet.

Följ dessa steg för att lägga till en fristående värd-server som redan har konfigurerat:

1. Logga in på Azure-stacken operatorn portalen som tjänstadministratör.

2. Välj **Bläddra** &gt; **administrativa resurser** &gt; **SQL som värd för servrar**.

   ![Värd för SQL-servrar](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   Under **värd för SQL-servrar**, kan du ansluta resursprovidern SQL till instanser av SQL Server som fungerar som den resursprovidern backend.

   ![Instrumentpanel för SQL-kort](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. På **lägga till en värd för SQL-Server**, ange anslutningsinformationen för SQL Server-instansen.

   ![Lägg till en SQL på värdservern](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Du kan också ange ett instansnamn och ange ett portnummer om instansen inte är tilldelad standardporten 1433.

   > [!NOTE]
   > Så länge som SQL-instans kan användas av användare och Azure Resource Manager-administratören, kan den placeras under kontroll av resursprovidern. SQL-instansen __måste__ tilldelas enbart resursprovidern.

4. När du lägger till servrar, måste du tilldela dem till en befintlig SKU eller skapa en ny SKU. Under **lägga till en Server som värd för**väljer **SKU: er**.

   * Välj en tillgänglig SKU för att använda en befintlig SKU, och välj sedan **skapa**.
   * Om du vill skapa en SKU **+ skapa nya SKU**. I **skapa SKU**, ange nödvändig information och välj sedan **OK**.

     > [!IMPORTANT]
     > Specialtecken, inklusive blanksteg och punkter, stöds inte i **namn** fältet. Använd exemplen i följande skärmbild för att ange värden för den **familj**, **nivå**, och **Edition** fält.

     ![Skapa en SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

      SKU: er kan ta upp till en timme att vara synliga i portalen. Användare kan inte skapa en databas tills SKU: N skapas fullständigt.

### <a name="sku-notes"></a>SKU-information

Du kan använda SKU: er för att skilja Tjänsterbjudanden. Du kan till exempel ha en SQL Enterprise-instans som har följande egenskaper:
  
* hög kapacitet
* hög prestanda
* Hög tillgänglighet

Du kan skapa en SKU för föregående exempel, begränsa åtkomst till specifika grupper som behöver en databas med hög prestanda.

>[!TIP]
>Använd en SKU-namn som motsvarar beskriver funktionerna i servrar i SKU: N, till exempel kapacitet och prestanda. Namnet som fungerar som en hjälp för att hjälpa användare att distribuera sina databaser till lämplig SKU: N.

Som bästa praxis, ska värdservrar i en SKU ha samma egenskaper för resursen och prestanda.

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Ger hög tillgänglighet med hjälp av SQL Always On-Tillgänglighetsgrupper

Konfigurera SQL Always On instanser kräver ytterligare åtgärder och kräver tre virtuella datorer (eller fysiska datorer.) Den här artikeln förutsätter att du redan har en god förståelse av Always On-Tillgänglighetsgrupper. Mer information finns i följande artiklar:

* [Introduktion till SQL Server Always On-Tillgänglighetsgrupper på Azure virtual machines](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Always On-Tillgänglighetsgrupper (SQLServer)](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> SQL-kort resursprovidern _endast_ stöder SQL 2016 SP1 Enterprise eller senare instanser för Always On. Den här konfigurationen för nätverkskort kräver nya SQL-funktioner som automatisk seeding.

Dessutom måste du aktivera [automatisk Seeding](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) på varje tillgänglighetsgrupp för varje instans av SQL Server.

Om du vill aktivera automatisk seeding på alla instanser, redigera och kör följande SQL-kommando för varje instans:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON 'InstanceName'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

Redigera och kör följande SQL-kommando för varje instans på de sekundära instanserna:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>Att lägga till SQL Always On värd för servrar

1. Logga in på Azure Stack-administrationsportalen som en tjänstadministratör

2. Välj **Bläddra** &gt; **administrativa resurser** &gt; **SQL som värd för servrar** &gt; **+ Lägg till**.

   Under **värd för SQL-servrar**, kan du ansluta Resource Provider för SQL Server till faktiska instanser av SQL Server som fungerar som den resursprovidern backend.

3. Fyll i formuläret med anslutningsinformationen för SQL Server-instansen. Kontrollera att du använder FQDN-adressen för alltid på lyssnare (och valfritt portnummer.) Ange information för det konto som du har konfigurerat med sysadmin-behörigheter.

4. Always On-Tillgänglighetsgruppen kryssrutan om du vill aktivera stöd för instanser av SQL Always On-Tillgänglighetsgruppen.

   ![Aktivera alltid på](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Lägga till en SQL Always On-instans till en SKU.

   > [!IMPORTANT]
   > Du kan blanda fristående servrar med Always On-instanser i samma SKU: N. Ett försök görs att blanda typer när du lägger till de första värd servern resulterar i ett fel.

## <a name="make-the-sql-databases-available-to-users"></a>Göra SQL-databaser som är tillgängliga för användarna

Skapa planer och erbjudanden om du vill göra SQL-databaser som är tillgängliga för användare. Lägg till den **Microsoft.SqlAdapter** tjänst i planen och Lägg till standard kvot eller skapa en ny kvot.

![Skapa planer och erbjudanden om du vill inkludera databaser](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="next-steps"></a>Nästa steg

[Lägga till databaser](azure-stack-sql-resource-provider-databases.md)

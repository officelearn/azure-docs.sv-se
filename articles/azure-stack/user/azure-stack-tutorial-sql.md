---
title: Skapa SQL-databaser med hög tillgänglighet i Azure Stack | Microsoft Docs
description: Lär dig hur du skapar en SQL Server-resursleverantör värddatorn och högtillgänglig SQL AlwaysOn-databaser med Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: b353b1c359ea663201b3fa5a72b28ab3298acd09
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46369269"
---
# <a name="tutorial-create-highly-available-sql-databases"></a>Självstudie: Skapa SQL-databaser med hög tillgänglighet

Du kan konfigurera server-datorer till värden SQL Server-databaser som en klient Azure Stack-användare. När du har en SQL server som värd är har skapas och hanteras av Azure Stack, användare som prenumererar på SQL-tjänster kan enkelt skapa SQL-databaser.

Den här kursen visar hur du använder en Azure Stack-snabbstartsmall för att skapa en [SQL Server AlwaysOn-tillgänglighetsgruppen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017), Lägg till den som en Azure Stack SQL som är värd för Server och sedan skapa en SQL-databas med hög tillgänglighet.

Vad du lära dig:

> [!div class="checklist"]
> * Skapa en SQL Server AlwaysOn-tillgänglighetsgrupp från en mall
> * Skapa ett Azure Stack som är värd för SQLServer
> * Skapa en SQL-databas med hög tillgänglighet

I den här självstudien en två Virtuella SQL Server AlwaysOn-tillgänglighetsgrupp kommer att skapas och konfigureras med tillgängliga Azure Stack marketplace-objekt. 

Se till att Azure Stack-operatör har gjort följande objekt tillgängliga i Azure Stack marketplace innan du börjar med stegen i den här självstudien:

> [!IMPORTANT]
> Följande krävs för Azure Stack-snabbstartsmall som ska användas.

- [Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer) marketplace-avbildning.
- SQL Server 2016 SP1 eller SP2 (Enterprise, Standard eller utvecklare) på Windows Server 2016 server-avbildning. Den här självstudien används den [SQL Server 2016 SP2 Enterprise på Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.sqlserver2016sp2enterprisewindowsserver2016) marketplace-avbildning.
- [SQL Server IaaS-tillägget](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) version 1.2.30 eller högre. SQL IaaS-tillägget installerar nödvändiga komponenter som krävs av Marketplace SQL Server-objekt för alla Windows-versioner. Det gör att SQL-specifika inställningar konfigureras på virtuella datorer med SQL. Om tillägget inte är installerat på den lokala Marketplace, misslyckas etableringen av SQL.
- [Tillägget för anpassat skript för Windows](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension) version 1.9.1 eller högre. Tillägget för anpassat skript är ett verktyg som kan användas för att starta automatiskt efter distributionen uppgifter för anpassning av virtuell dator.
- [PowerShell Desired State Configuration (DSC)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) version 2.76.0.0 eller högre. DSC är en hanteringsplattform i Windows PowerShell som gör det möjligt för distribution och hantering av konfigurationsdata för programtjänster och hantering av miljön där de här tjänsterna körs.

  > [!TIP]
  > Du kommer inte att kunna se den SQL Server IaaS-tillägget, tillägg för anpassat skript eller PowerSHell DSC marketplace-objekt när du skapar en virtuell dator från användarportalen. Kontakta Azure Stack-operatör för att säkerställa att de här objekten har laddats ned från Azure innan du börjar med stegen i den här självstudien.

Mer information om att lägga till objekt i Azure Stack Marketplace finns på [översikt över Azure Stack Marketplace](.\.\azure-stack-marketplace.md).

## <a name="create-a-sql-server-alwayson-availability-group"></a>Skapa en SQL Server AlwaysOn-tillgänglighetsgrupp
Använd steg i det här avsnittet för att distribuera SQL Server AlwaysOn-tillgänglighetsgruppen med hjälp av den [sql 2016 alwayson Azure Stack-snabbstartsmall](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson). Den här mallen distribuerar två instanser av SQL Server Enterprise, Standard eller utvecklare i en ständigt aktiverad tillgänglighetsgrupp. Den skapar följande resurser:

- En nätverkssäkerhetsgrupp
- Ett virtuellt nätverk
- Fyra storage-konton (en för Active Directory (AD), en för SQL, ett för filresursvittnet och en VM-diagnostik)
- Fyra offentliga IP-adresser (en för AD två för varje SQL-VM och en offentlig belastningsutjämnare som är bunden till SQL AlwaysOn-lyssnaren)
- En extern belastningsutjämnare för virtuella datorer med SQL med offentliga IP-bundet till SQL AlwaysOn-lyssnare
- En virtuell dator (Windows Server 2016) konfigurerats som domänkontrollant för en ny skog med en enda domän
- Två virtuella datorer (Windows Server 2016) konfigurerade med SQL Server 2016 SP1 eller SP2 Enterprise, Standard och Developer Edition och klustrade. Dessa måste vara marketplace-avbildningar.
- En virtuell dator (Windows Server 2016) konfigurerats som filresursvittne för klustret
- En tillgänglighetsuppsättning som innehåller SQL- och dela vittne virtuella datorer  

> [!NOTE]
> Kör de här stegen från användarportalen för Azure Stack som en klientanvändare med en prenumeration som tillhandahåller IaaS-funktioner (beräkning, nätverk, storage-tjänster).

1. Logga in på användarportalen:
    - Ett integrerat system-distribution varierar Portaladress beroende på region och externa domännamn för din lösning. Det ska vara i formatet https://portal.&lt; *region*&gt;.&lt; *FQDN*&gt;.
    - Om du använder Azure Stack Development Kit (ASDK) användaren Portaladress är [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

2. Välj **\+** **skapa en resurs** > **anpassade**, och sedan **malldistributionen**.

   ![Anpassad malldistribution](media/azure-stack-tutorial-sqlrp/custom-deployment.png)


3. På den **anpassad distribution** bladet väljer **redigera mallen** > **snabbstartsmall** och sedan använda listan över tillgängliga anpassade mallar till Välj den **sql 2016 alwayson** mall, klickar du på **OK**, och sedan **spara**.

   ![Välj snabbstartsmall](./media/azure-stack-tutorial-sqlrp/quickstart-template.png)


4. På den **anpassad distribution** bladet väljer **redigera parametrar** och granska standardinställningarna. Ändra värden som behövs för att ange alla nödvändiga parameterinformation och klicka sedan på **OK**.<br><br> Minst:

    - Ange komplexa lösenord för parametrarna ADMINPASSWORD och SQLSERVERSERVICEACCOUNTPASSWORD SQLAUTHPASSWORD.
    - Ange DNS-Suffix för omvänd sökning med små bokstäver för parametern DNSSUFFIX (**azurestack.external** för ASDK installationer).
    
    ![Distribution av anpassade parametrar](./media/azure-stack-tutorial-sqlrp/edit-parameters.png)

5. På den **anpassad distribution** bladet välj prenumerationen som ska använda och skapa en ny resursgrupp eller välj en befintlig resursgrupp för anpassad distribution.<br><br> Välj plats för resursgruppen (**lokala** för ASDK installationer) och klicka sedan på **skapa**. De anpassade distributionsinställningarna kommer att valideras och sedan distributionen startar.

    ![Distribution av anpassade parametrar](./media/azure-stack-tutorial-sqlrp/create-deployment.png)


6. Välj i användarportalen, **resursgrupper** och sedan namnet på resursgruppen du skapade för anpassad distribution (bara **resursgrupp** i det här exemplet). Visa statusen för distributionen för att se till att alla distributioner har slutförts.<br><br>Granska resursobjekt för gruppen och välj sedan den **SQLPIPsql\<Resursgruppsnamn\>**  offentliga IP-adress-objekt. Anteckna den offentliga IP-adress och fullständig FQDN för den offentliga IP-load balancer. Du måste ge det till en Azure Stack-operatör så att de kan skapa en SQL-värdserver som att använda den här SQL AlwaysOn-tillgänglighetsgruppen.

   > [!NOTE]
   > Malldistributionen tar flera timmar att slutföra.

   ![Distribution av anpassade parametrar](./media/azure-stack-tutorial-sqlrp/deployment-complete.png)

### <a name="enable-automatic-seeding"></a>Aktivera automatisk seeding
När mallen har distribuerats och konfigurerats SQL AlwaysON-tillgänglighetsgruppen, måste du aktivera [automatisk seeding](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) i varje instans av SQL Server i tillgänglighetsgruppen. 

När du skapar en tillgänglighetsgrupp med automatisk seeding skapar SQL Server automatiskt de sekundära replikerna för varje databas i gruppen utan manuella åtgärder krävs för att garantera hög tillgänglighet för AlwaysOn-databaser.

Använd följande SQL-kommandon för att konfigurera automatisk seeding för AlwaysOn-tillgänglighetsgruppen.

På den primära SQL-instansen (Ersätt <InstanceName> med det primära SQL Server-instansnamnet):

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

>  ![Primära SQL-instans-skript](./media/azure-stack-tutorial-sqlrp/sql1.png)

På sekundära SQL-instanser (Ersätt < availability_group_name > med AlwaysOn tillgänglighetsgruppens namn):

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```
>  ![Sekundär SQL-instans-skript](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>Konfigurera innesluten databas-autentisering
Innan du lägger till en innesluten databas till en tillgänglighetsgrupp, kontrollerar du att alternativet innesluten databas authentication server är inställd på 1 på varje server-instansen som är värd för en tillgänglighetsreplik för tillgänglighetsgruppen. Mer information finns i [innesluten Databasautentisering](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Med dessa kommandon anger alternativet innesluten databas authentication server för varje SQL Server-instans i tillgänglighetsgruppen:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```
>  ![Ange innehöll Databasautentisering](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-sql-hosting-server"></a>Skapa ett Azure Stack som är värd för SQLServer
När SQL Server AlwayOn availability-gruppen har skapats och konfigurerats korrekt, måste en Azure Stack-Operator skapa ett Azure Stack som är värd för SQLServer för att göra den ytterligare kapaciteten tillgänglig för användare att skapa databaser. 

Kom ihåg att tillhandahålla Azure Stack-operatör offentlig IP-adress eller fullständiga FQDN för offentliga IP-Adressen för belastningsutjämnaren SQL som har registrerats tidigare när SQL AlwaysOn-tillgänglighetsgruppen resursgruppen skapades (**SQLPIPsql\<resurs gruppnamn\>**). Dessutom kan behöver operatören veta SQL Server-autentiseringsuppgifter används för att få åtkomst till SQL-instanser i AlwaysOn-tillgänglighetsgruppen.

> [!NOTE]
> Det här steget måste köras från Azure Stack-administrationsportalen av en Azure Stack-operatör.

Med SQL AlwaysOn-tillgänglighetsgruppen load balancer lyssnare offentlig IP-adress och SQL-inloggning autentiseringsinformation tillhandahålls av klientanvändare, en Azure Stack-operatör kan nu [skapar en SQL som är värd för Server med gruppen SQL AlwaysOn-tillgänglighet ](.\.\azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups). 

Se också till att Azure Stack-operatör har skapat planer och erbjudanden för att tillgängliggöra skapande av SQL AlwaysOn-databas för användare. Operatören behöver du lägga till den **Microsoft.SqlAdapter** tjänsten till en plan och skapa en ny kvot specifikt för höggradigt tillgängliga databaser. Mer information om hur du skapar planer finns i [översikt över Plan, erbjudande, kvot och prenumeration](.\.\azure-stack-plan-offer-quota-overview.md).

> [!TIP]
> Den **Microsoft.SqlAdapter** tjänsten kan inte läggas till planer tills den [SQL Server-resursprovidern har distribuerats](.\.\azure-stack-sql-resource-provider-deploy.md).

## <a name="create-a-highly-available-sql-database"></a>Skapa en SQL-databas med hög tillgänglighet
När SQL AlwaysOn-tillgänglighetsgrupp som har skapats, konfigurerats och läggas till som en Azure Stack som är värd för SQLServer av en Azure Stack-operatör, kan en klientanvändare med en prenumeration som innehåller funktioner som SQL Server-databas skapa SQL-databaser som stöder Funktionen AlwaysOn genom att följa stegen i det här avsnittet. 

> [!NOTE]
> Kör de här stegen från användarportalen för Azure Stack som en klientanvändare med en prenumeration som ger SQL Server-funktioner (Microsoft.SQLAdapter service).

1. Logga in på användarportalen:
    - Ett integrerat system-distribution varierar Portaladress beroende på region och externa domännamn för din lösning. Det ska vara i formatet https://portal.&lt; *region*&gt;.&lt; *FQDN*&gt;.
    - Om du använder Azure Stack Development Kit (ASDK) användaren Portaladress är [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

2. Välj **\+** **skapa en resurs** > **Data \+ Storage**, och sedan **SQL Database**.<br><br>Ange den egenskapen databasinformation som krävs, inklusive namn, sortering, maximal storlek och prenumeration, resursgrupp och plats för distributionen. 

   ![Skapa SQL-databas](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. Välj **SKU** och välj sedan den lämpliga SQL som är värd för Server-SKU du använder. I det här exemplet Azure Stack-operatör har skapat den **Enterprise-HA** SKU för hög tillgänglighet för SQL AlwaysOn-Tillgänglighetsgrupper.

   ![Välj SKU](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. Välj **inloggning** > **skapa en ny inloggning** och ange autentiseringsuppgifter för SQL-autentisering som ska användas för den nya databasen. När du är klar klickar du på **OK** och sedan **skapa** att starta distributionsprocessen för databasen.

   ![Skapa inloggning](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. När SQL database-distributionen är klar kan du granska egenskaperna för databasen för att identifiera strängen som du använder för att ansluta till den nya databasen med hög tillgänglighet. 

   ![Visa anslutningssträng](./media/azure-stack-tutorial-sqlrp/createdb4.png)




## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en SQL Server AlwaysOn-tillgänglighetsgrupp från en mall
> * Skapa ett Azure Stack som är värd för SQLServer
> * Skapa en SQL-databas med hög tillgänglighet

Fortsätt till nästa självstudie och lär dig hur du:
> [!div class="nextstepaction"]
> [Skapa högtillgängliga MySQL-databaser](azure-stack-tutorial-mysql.md)
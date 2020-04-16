---
title: Köra SSIS-paket av Azure SQL Managed Instance Agent
description: Lär dig hur du kör SSIS-paket av Azure SQL Managed Instance Agent.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: b3b7a25149a9d075c81b30307ade2beb71907637
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394727"
---
# <a name="execute-ssis-packages-by-azure-sql-managed-instance-agent"></a>Köra SSIS-paket av Azure SQL Managed Instance Agent
I den här artikeln beskrivs hur du kör ett SSIS-paket (SQL Server Integration Services) med hjälp av Azure SQL Managed Instance Agent. Den här funktionen ger liknande beteenden precis som när du schemalägger SSIS-paket av SQL Server Agent i din on-prem-miljö.

Med den här funktionen kan du köra SSIS-paket som lagras i SSISDB för Azure SQL Managed Instance eller File System, till exempel Azure Files.

## <a name="prerequisites"></a>Krav
Om du vill använda den här funktionen hämtar och installerar du den senaste versionen av SSMS, som är version 18.5 eller senare. Ladda ner den från [denna webbplats](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

Och du måste etablera en Azure-SSIS Integration Runtime i Azure Data Factory, som använder Azure SQL Managed Instance som slutpunktsserver. Om du inte redan har etablerat den, etablera den genom att följa instruktionerna i [självstudien](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="run-ssis-packages-in-ssisdb-by-azure-sql-managed-instance-agent"></a>Kör SSIS-paket i SSISDB av Azure SQL Managed Instance Agent
I det här steget använder du Azure SQL Managed Instance Agent för att anropa SSIS-paket som lagras i SSISDB i Azure SQL Managed Instance.
1. Anslut till Azure SQL Managed Instance i den senaste versionen av SSMS.
2. Skapa ett nytt agentjobb och ett nytt jobbsteg.

![Nytt agentjobb](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. Välj **pakettyp** för SQL Server Integration Services på sidan Nytt **jobbsteg.**

![Nytt SSIS-jobbsteg](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. Välj **SSIS-katalog** som paketkällatyp på fliken **Paket.**
5. Eftersom SSISDB finns i samma Azure SQL-hanterade instans behöver du inte ange autentisering.
6. Ange ett SSIS-paket från din SSISDB.

![Typ av paketkälla - SSIS-katalog](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

7. På fliken **Konfigurationar** kan du ange **parametervärden,** åsidosätta värden i **Anslutningshanterare**, **åsidosätta egenskap** och välja **Loggningsnivå**.

![Typ av paketkälla - SSIS-katalogkonfiguration](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

8. När du har slutfört all konfiguration ovan klickar du på **OK** för att spara agentjobbkonfigurationen.
9. Starta agentjobbet för att köra SSIS-paketet.


## <a name="run-ssis-packages-in-file-system-by-azure-sql-managed-instance-agent"></a>Kör SSIS-paket i filsystemet av Azure SQL-hanterad instansagent
I det här steget använder du Azure SQL Managed Instance Agent för att anropa SSIS-paket som lagras i Filsystem för att köras.
1. Anslut till Azure SQL Managed Instance i den senaste versionen av SSMS.
2. Skapa ett nytt agentjobb och ett nytt jobbsteg.

   ![Nytt agentjobb](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. Välj **pakettyp** för SQL Server Integration Services på sidan Nytt **jobbsteg.**

   ![Nytt SSIS-jobbsteg](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. Välj **Filsystem** som paketkällatyp på fliken **Paket.**

   ![Typ av paketkälla - filsystem](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

   1. Om ditt paket överförs till Azure File väljer du **Azure-filresurs** som filkällatyp.
      - Paketsökvägen är ** \\ <storage account name>.file.core.windows.net\<filresursnamn>\<paketnamn>.dtsx**
      - Skriv in Azure-filkontonamnet och kontonyckeln i **Package file access-autentiseringsuppgifter** för att komma åt Azure-filen. Domänen anges som **Azure**.
   2. Om paketet överförs till en nätverksresurs väljer du **Nätverksresurs** som filkällatyp.
      - Paketsökvägen är **UNC-sökvägen** till paketfilen med dtsx-tillägget.
      - Skriv in motsvarande **domän,** **användarnamn**och **lösenord** för att komma åt paketfilen för nätverksresurs.
   3. Om paketfilen är krypterad med lösenord väljer du **Krypteringslösenord** och skriver in lösenordet.

 5. På fliken Konfigurationer skriver du **sökvägen till konfigurationsfilen** om du behöver en konfigurationsfil för att köra SSIS-paketet. **Configurations**
 6. På fliken **Körningsalternativ** kan du välja om du vill använda **Windows-autentisering** eller **32-bitars körning** för att köra SSIS-paketet.
 7. På fliken **Loggning** kan du välja **loggningssökväg** och motsvarande loggningsåtkomstautentisering för att lagra loggfilerna. Som standard kommer loggningssökvägen att vara samma som sökvägen till paketmappen och loggningsåtkomstautentiseringsuppgifterna är samma som autentiseringsuppgifterna för paketåtkomst.
 8. På fliken **Ange värden** kan du skriva in **egenskapssökvägen** och **värdet för** att åsidosätta paketegenskaperna.
 Om du till exempel vill åsidosätta värdet för användarvariabeln anger du dess sökväg i följande format: **<variable name>\Package.Variabler[Användare:: ]. Värde**.
 9. När du har slutfört all konfiguration ovan klickar du på **OK** för att spara agentjobbkonfigurationen.
 10. Starta agentjobbet för att köra SSIS-paketet.


 ## <a name="cancel-ssis-package-execution"></a>Avbryta körning av SSIS-paket
 Om du vill avbryta paketkörning från ett Azure SQL Managed Agent-jobb bör du följa stegen nedan i stället för att direkt stoppa agentjobbet.
 1. Hitta din SQL-agent **jobId** från **msdb.dbo.sysjobs**.
 2. Hitta motsvarande **SSIS-körningId** baserat på jobb-ID:et nedan:
    ```sql
    select * from ssisdb.internal.execution_parameter_values_noncatalog where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
    ```
 3. Välj **Aktiva operationer** under SSIS-katalogen.

    ![Typ av paketkälla - filsystem](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

 4. Stoppa motsvarande åtgärd baserat på **executionId**.

## <a name="next-steps"></a>Nästa steg
 Du kan också schemalägga SSIS-paket med Azure Data Factory. Stegvisa instruktioner finns i [Azure Data Factory Event Trigger](how-to-create-event-trigger.md). 
---
title: Installera elastic database-jobb | Microsoft Docs
description: Gå igenom installationen av funktionen Elastiskt jobb.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 32df3e7ac6dc22e247bd4aecea4f39bf6d3a8017
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475787"
---
# <a name="installing-elastic-database-jobs-overview"></a>Installera översikt över elastiska databasjobb

[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]



[**Elastic Database-jobb** ](sql-database-elastic-jobs-overview.md) kan installeras via PowerShell eller via Azure portal. Du kan komma åt för att skapa och hantera jobb med hjälp av PowerShell-API endast om du installerar PowerShell-paketet. Dessutom ger PowerShell APIs betydligt fler funktioner än portalen vid denna tidpunkt.

Om du redan har installerat **elastiska databasjobb** via portalen från en befintlig **elastisk pool**, den senaste förhandsversionen av PowerShell innehåller skript för att uppgradera den befintliga installationen. Vi rekommenderar starkt att uppgradera installationen till senast **elastiska databasjobb** komponenter för att kunna dra nytta av nya funktioner som exponeras via PowerShell APIs.

## <a name="prerequisites"></a>Nödvändiga komponenter
* En Azure-prenumeration. En kostnadsfri utvärderingsversion, se [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* Azure PowerShell. Installera den senaste versionen med hjälp av den [Web Platform Installer](https://go.microsoft.com/fwlink/p/?linkid=320376). Mer information finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).
* [NuGet Command-line Utility](https://nuget.org/nuget.exe) används för att installera paketet för Elastic Database-jobb. Mer information finns i https://docs.nuget.org/docs/start-here/installing-nuget.

## <a name="download-and-import-the-elastic-database-jobs-powershell-package"></a>Hämta och importera PowerShell-paketet för Elastic Database-jobb
1. Starta Microsoft Azure PowerShell-Kommandotolken och navigera till katalogen där du hämtade NuGet Command-line Utility (nuget.exe).
2. Hämta och importera **elastiska databasjobb** paketet till den aktuella katalogen med följande kommando:
   
        PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease
   
    Den **elastiska databasjobb** filer placeras i den lokala katalogen i en mapp med namnet **Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x** där *x.x.xxxx.x* återspeglar den versionsnummer. PowerShell-cmdlets (inklusive nödvändiga DLL-filer) finns i den **tools\ElasticDatabaseJobs** underkatalog och PowerShell-skript för att installera, uppgradera och avinstallera finns också i den **verktyg** underkatalog.
3. Gå till den underordnade verktygsmappen under mappen Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x genom att skriva cd-verktyg, till exempel:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4. Kör skript för att kopiera katalogen ElasticDatabaseJobs till $home\Documents\WindowsPowerShell\Modules.\InstallElasticDatabaseJobsCmdlets.ps1. Detta kommer också automatiskt importera modulen för användning, till exempel:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## <a name="install-the-elastic-database-jobs-components-using-powershell"></a>Installera elastiska jobb med hjälp av PowerShell
1. Starta ett Microsoft Azure PowerShell-kommandofönster och gå till katalogen \tools underordnade under mappen Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x: Skriv cd \tools
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2. Kör.\InstallElasticDatabaseJobs.ps1 PowerShell-skript och ange värden för de begärda variablerna. Det här skriptet skapar de komponenter som beskrivs i [Elastic Database-jobb komponenter och priser](sql-database-elastic-jobs-overview.md#components-and-pricing) tillsammans med Konfigurera Azure-molntjänst för att använda på lämpligt sätt beroende komponenter.

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

När du kör det här kommandot ett fönster upp som ber om en **användarnamn** och **lösenord**. Detta är inte dina autentiseringsuppgifter för Azure, ange användarnamn och lösenord som autentiseringsuppgifterna som du vill skapa för den nya servern.

Parametrarna som anges på det här exemplet anropet kan ändras för dina önskade inställningar. Nedan finns mer information om beteendet för varje parameter:

<table style="width:100%">
  <tr>
    <th>Parameter</th>
    <th>Beskrivning</th>
  </tr>

<tr>
    <td>ResourceGroupName</td>
    <td>Tillhandahåller Azure resursgruppens namn innehåller de nyligen skapade Azure-komponenterna. Den här parametern som standard ”__ElasticDatabaseJob”. Du bör inte ändra det här värdet.</td>
    </tr>

<tr>
    <td>ResourceGroupLocation</td>
    <td>Innehåller den Azure-platsen som ska användas för de nyligen skapade Azure-komponenterna. Den här parametern standard platsen för centrala USA.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>Visar antalet arbetare för tjänsten att installera. Den här parametern standardvärdet 1. Ett högre antal arbetare kan användas för att skala ut tjänsten och för att ge hög tillgänglighet. Det rekommenderas att använda ”2” för distributioner som kräver hög tillgänglighet till tjänsten.</td>
</tr>

<tr>
    <td>ServiceVmSize</td>
    <td>Innehåller VM-storleken för användning i Molntjänsten. Den här parametern standard A0. Parametervärdena för... /.. / A3 accepteras vilket leder till att worker-roll att använda en ExtraSmall/Small/Medium/Large storlek respektive. Läs mer på worker rollstorlekar <a href="https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview#components-and-pricing">Elastic Database-jobb komponenter och priser</a>.</td>
</tr>

<tr>
    <td>SqlServerDatabaseSlo</td>
    <td>Tillhandahåller beräkningsstorleken för en Standard-utgåva. Den här parametern standard S0. Parametervärdena för... /.. /.. /.. / S9/S12 accepteras vilket leder till Azure SQL-databas att använda respektive beräkningsstorleken. Mer information om storlekar på SQL-databas finns i <a href="https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview#components-and-pricing">Elastic Database-jobb komponenter och priser</a>.</td>
</tr>

<tr>
    <td>SqlServerAdministratorUserName</td>
    <td>Innehåller ett administratörsanvändarnamn för den nyligen skapade Azure SQL Database-servern. Om inget värde anges öppnas ett fönster för PowerShell-autentiseringsuppgifter för att efterfråga autentiseringsuppgifter.</td>
</tr>

<tr>
    <td>SqlServerAdministratorPassword</td>
    <td>Tillhandahåller administratörslösenordet för den nyligen skapade Azure SQL Database-servern. När inte anges så öppnas ett fönster för PowerShell-autentiseringsuppgifter för att efterfråga autentiseringsuppgifter.</td>
</tr>
</table>

För system som är avsedda att ha stort antal jobb som körs parallellt mot ett stort antal databaser, rekommenderar vi att du anger parametrar, till exempel: - ServiceWorkerCount 2 - ServiceVmSize A2 - SqlServerDatabaseSlo S2.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## <a name="update-an-existing-elastic-database-jobs-components-installation-using-powershell"></a>Uppdatera en befintlig Elastic Database-jobb komponentinstallation med hjälp av PowerShell
**Elastic Database-jobb** kan uppdateras i en befintlig installation för skalbarhet och hög tillgänglighet. Den här processen kan för framtida uppgraderingar av tjänsten kod utan att behöva ta bort och återskapa databasen kontroll. Den här processen kan också användas inom samma version för att ändra tjänsten VM-storlek eller worker-antal servrar.

För att uppdatera VM-storleken för en installation, kör du följande skript med parametrar som uppdateras till värdena för ditt val.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>Parameter</th>
  <th>Beskrivning</th>
</tr>

<tr>
    <td>ResourceGroupName</td>
    <td>Identifierar Azure resursgruppens namn används när elastiska jobb databaskomponenterna ursprungligen har installerats. Den här parametern som standard ”__ElasticDatabaseJob”. Eftersom det inte rekommenderas att ändra det här värdet, har du inte anger den här parametern.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>Visar antalet arbetare för tjänsten att installera.  Den här parametern standardvärdet 1.  Ett högre antal arbetare kan användas för att skala ut tjänsten och för att ge hög tillgänglighet.  Det rekommenderas att använda ”2” för distributioner som kräver hög tillgänglighet till tjänsten.</td>
</tr>

<tr>
    <td>ServiceVmSize</td>
    <td>Innehåller VM-storleken för användning i Molntjänsten. Den här parametern standard A0. Parametervärdena för... /.. / A3 accepteras vilket leder till att worker-roll att använda en ExtraSmall/Small/Medium/Large storlek respektive. Läs mer på worker rollstorlekar <a href="https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview#components-and-pricing">Elastic Database-jobb komponenter och priser</a>.</td>
</tr>

</table>

## <a name="install-the-elastic-database-jobs-components-using-the-portal"></a>Installera elastiska jobb med hjälp av portalen
När du har [skapas en elastisk pool](sql-database-elastic-pool-manage-portal.md), kan du installera **elastiska databasjobb** komponenter för att aktivera körning av administrativa uppgifter mot varje databas i den elastiska poolen. Till skillnad från när du använder den **elastiska databasjobb** PowerShell APIs portalgränssnittet är för närvarande begränsade till bara köras mot en befintlig pool.

**Uppskattad tidsåtgång:** 10 minuter.

1. I instrumentpanelsvyn för elastisk pool via den [Azure-portalen](https://portal.azure.com/#) , klickar du på **skapa jobb**.
2. Om du skapar ett jobb för första gången, måste du installera **elastiska databasjobb** genom att klicka på **FÖRHANDSVERSIONSVILLKOREN**.
3. Acceptera villkoren genom att klicka på kryssrutan.
4. I ”installera tjänster” visas klickar du på **JOBBAUTENTISERINGSUPPGIFTER**.
   
    ![Installera tjänsterna][1]
5. Skriv ett användarnamn och lösenord för en databas. Som en del av installationen skapas en ny Azure SQL Database-server. I den här nya servern, en ny databas, kallas även kontroll-databasen skapas och används för att lagra metadata för Elastic Database-jobb. Användarnamn och lösenord som skapas här används för att logga in på åtkomstkontroll-databasen. Separata autentiseringsuppgifter används för körning av skript mot databaserna i poolen.
   
    ![Skapa användarnamn och lösenord][2]
6. Klicka på OK. Komponenterna har skapats för dig om några minuter i en ny [resursgrupp](../azure-resource-manager/resource-group-overview.md). Den nya resursgruppen är fäst på start-tavlan som visas nedan. När du skapade, elastiska databasjobb (Cloud Services, SQL Database, Service Bus och lagring) skapas i gruppen.
   
    ![resursgrupp i start-tavla][3]
7. Om du försöker skapa eller hantera ett jobb medan elastiska databasjobb installeras när du anger **autentiseringsuppgifter** visas följande meddelande.
   
    ![Distribution pågår fortfarande][4]

Om avinstallationen krävs måste du ta bort resursgruppen. Se [avinstallera elastiska jobb databaskomponenterna](sql-database-elastic-jobs-uninstall.md).

## <a name="next-steps"></a>Nästa steg
Se till att en autentiseringsuppgift med rätt behörighet för körning av skript skapas för varje databas i gruppen för mer information finns i [säkra din SQL Database](sql-database-manage-logins.md).
Se [skapa och hantera en Elastic Database-jobb](sql-database-elastic-jobs-create-and-manage.md) att komma igång.

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/not-done.png

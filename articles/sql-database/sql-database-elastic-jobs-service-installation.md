---
title: Installera elastisk databas jobb | Microsoft Docs
description: "Gå igenom installationen av elastiska jobb."
services: sql-database
manager: craigg
author: ddove
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 10/24/2016
ms.author: sstein
ms.openlocfilehash: 7e5258e0ccb88c8e2f3fe3277f69444ae5424cd0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="installing-elastic-database-jobs-overview"></a>Installera översikt över elastisk databas
[**Den elastiska databasen jobb** ](sql-database-elastic-jobs-overview.md) kan installeras via PowerShell eller via Azure-portalen. Du kan komma åt för att skapa och hantera jobb med hjälp av PowerShell API endast om du har installerat PowerShell. Dessutom tillhandahålla PowerShell APIs betydligt fler funktioner än portalen vid denna tidpunkt.

Om du redan har installerat **elastisk databas jobb** via portalen från en befintlig **elastisk pool**, innehåller den senaste Powershell-förhandsversionen skript för att uppgradera den befintliga installationen. Vi rekommenderar starkt att uppgradera installationen till senast **elastisk databas jobb** komponenter för att kunna dra nytta av nya funktioner som exponeras via PowerShell APIs.

## <a name="prerequisites"></a>Förutsättningar
* En Azure-prenumeration. För en kostnadsfri utvärderingsversion finns [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* Azure PowerShell. Installera den senaste versionen med hjälp av den [installationsprogram för webbplattform](http://go.microsoft.com/fwlink/p/?linkid=320376). Mer information finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).
* [NuGet Command-line Utility](https://nuget.org/nuget.exe) används för att installera paketet för elastisk databas jobb. Mer information finns i http://docs.nuget.org/docs/start-here/installing-nuget.

## <a name="download-and-import-the-elastic-database-jobs-powershell-package"></a>Hämta och importera jobb PowerShell-paketet för elastisk databas
1. Starta Microsoft Azure PowerShell-kommandofönster och gå till den katalog där du hämtade NuGet Command-line Utility (nuget.exe).
2. Hämta och importera **elastisk databas jobb** paketet till den aktuella katalogen med följande kommando:
   
        PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease
   
    Den **elastisk databas jobb** filerna placeras på den lokala katalogen i en mapp med namnet **Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x** där *x.x.xxxx.x* återspeglar den versionsnummer. PowerShell-cmdlet (inklusive nödvändiga klient DLL-filer) finns i den **tools\ElasticDatabaseJobs** underkatalog och PowerShell-skript för att installera, uppgradera och avinstallera finns också i den **verktyg** underkatalog.
3. Gå till underkatalogen verktyg under mappen Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x genom att skriva CD-verktyg, till exempel:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4. Kör skriptet.\InstallElasticDatabaseJobsCmdlets.ps1 om du vill kopiera katalogen ElasticDatabaseJobs till $home\Documents\WindowsPowerShell\Modules. Detta kommer också automatiskt importera modulen för användning, till exempel:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## <a name="install-the-elastic-database-jobs-components-using-powershell"></a>Installera elastiska jobb med hjälp av PowerShell
1. Starta Microsoft Azure PowerShell-Kommandotolken och navigera till \tools underkatalog under mappen Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x: Skriv cd \tools
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2. Köra.\InstallElasticDatabaseJobs.ps1 PowerShell-skript och ange värden för de begärda variablerna. Det här skriptet skapar de komponenter som beskrivs i [elastisk databas jobb komponenter och prissättning](sql-database-elastic-jobs-overview.md#components-and-pricing) tillsammans med hur du konfigurerar Azure Cloud Service om du vill använda på lämpligt sätt beroende komponenter.

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

När du kör det här kommandot ett fönster upp som ber om en **användarnamn** och **lösenord**. Detta är inte dina Azure-autentiseringsuppgifter, ange användarnamn och lösenord som administratören som du vill skapa för den nya servern.

De parametrar som ges i det här exemplet anrop kan ändras för inställningarna. Nedan finns mer information om beteendet för varje parameter:

<table style="width:100%">
  <tr>
    <th>Parameter</th>
    <th>Beskrivning</th>
  </tr>

<tr>
    <td>ResourceGroupName</td>
    <td>Tillhandahåller Azure resursgruppens namn innehåller de nyligen skapade Azure komponenterna. Den här parametern standard ”__ElasticDatabaseJob”. Du bör inte ändra det här värdet.</td>
    </tr>

</tr>

    <tr>
    <td>ResourceGroupLocation</td>
    <td>Innehåller den Azure-platsen som ska användas för de nyligen skapade Azure-komponenterna. Den här parametern standard centrala USA-plats.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>Visar antalet service anställda att installera. Den här parametern standardvärdet 1. Ett högre antal anställda kan användas för att skala ut tjänsten och för att tillhandahålla hög tillgänglighet. Det rekommenderas att använda ”2” för distributioner som kräver hög tillgänglighet för tjänsten.</td>
    </tr>

</tr>
    <tr>
    <td>ServiceVmSize</td>
    <td>Innehåller VM-storlek för användning i Molntjänsten. Den här parametern standard A0. Parametervärdena A0/A1/A2/a3 accepteras som orsakar arbetsrollen att använda en ExtraSmall/Small/Medium/Large storlek respektive. För mer information om storlekar för worker-rollen och se [elastisk databas jobb komponenter och prissättning](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerDatabaseSlo</td>
    <td>Tillhandahåller servicenivåmålet för en Standard-utgåva. Den här parametern standardvärdet S0. Parametervärdena för S0/S1/S2/S3/S4/S6/S9/S12 förpackningen accepteras vilket leder till Azure SQL-databasen ska användas på respektive SLO. Mer information om servicenivåmål för SQL-databasen finns [elastisk databas jobb komponenter och prissättning](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorUserName</td>
    <td>Ger administratören användarnamnet för den nyligen skapade Azure SQL Database-servern. Om inget värde anges öppnas ett fönster för PowerShell-autentiseringsuppgifter för att efterfråga autentiseringsuppgifter.</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorPassword</td>
    <td>Tillhandahåller administratörslösenordet för den nyligen skapade Azure SQL Database-servern. När inte tillhandahålls, öppnas ett fönster för PowerShell-autentiseringsuppgifter för att efterfråga autentiseringsuppgifter.</td>
</tr>
</table>

För datorer som är avsedda att ha stort antal jobb som körs parallellt mot ett stort antal databaser, rekommenderas att ange parametrar, till exempel: - ServiceWorkerCount 2 - ServiceVmSize A2 - SqlServerDatabaseSlo S2.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## <a name="update-an-existing-elastic-database-jobs-components-installation-using-powershell"></a>Uppdatera en befintlig elastisk databas jobb komponenter installation med hjälp av PowerShell
**Den elastiska databasen jobb** kan uppdateras i en befintlig installation för skalning och hög tillgänglighet. Den här processen kan för framtida uppgraderingar av Tjänstkod utan att behöva ta bort och återskapa kontroll-databasen. Den här processen kan också användas inom samma version för att ändra tjänsten VM-storlek eller antalet server worker.

För att uppdatera VM-storlek för en installation, kör du följande skript med parametrarna uppdateras till värdena du väljer.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>Parameter</th>
  <th>Beskrivning</th>
</tr>

  <tr>
    <td>ResourceGroupName</td>
    <td>Identifierar Azure resursgruppens namn används när elastisk databas jobbet-komponenter installerades från början. Den här parametern standard ”__ElasticDatabaseJob”. Eftersom det inte rekommenderas att ändra det här värdet, inte bör du måste ange den här parametern.</td>
    </tr>
</tr>

</tr>

  <tr>
    <td>ServiceWorkerCount</td>
    <td>Visar antalet service anställda att installera.  Den här parametern standardvärdet 1.  Ett högre antal anställda kan användas för att skala ut tjänsten och för att tillhandahålla hög tillgänglighet.  Det rekommenderas att använda ”2” för distributioner som kräver hög tillgänglighet för tjänsten.</td>
</tr>

</tr>

    <tr>
    <td>ServiceVmSize</td>
    <td>Innehåller VM-storlek för användning i Molntjänsten. Den här parametern standard A0. Parametervärdena A0/A1/A2/a3 accepteras som orsakar arbetsrollen att använda en ExtraSmall/Small/Medium/Large storlek respektive. För mer information om storlekar för worker-rollen och se [elastisk databas jobb komponenter och prissättning](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</table>

## <a name="install-the-elastic-database-jobs-components-using-the-portal"></a>Installera elastiska jobb med hjälp av portalen
När du har [skapas en elastisk pool](sql-database-elastic-pool-manage-portal.md), kan du installera **elastisk databas jobb** komponenter om du vill aktivera körningen av administrativa uppgifter mot varje databas i den elastiska poolen. Till skillnad från när du använder den **jobb för elastisk databas** PowerShell APIs gränssnittet Företagsportalen är för närvarande begränsad till endast körs mot en befintlig adresspool.

**Uppskattad tidsåtgång:** 10 minuter.

1. Från instrumentpanelsvyn för den elastiska poolen via den [Azure-portalen](https://portal.azure.com/#) , klickar du på **skapa jobbet**.
2. Om du skapar ett jobb för första gången, måste du installera **elastisk databas jobb** genom att klicka på **FÖRHANDSGRANSKNINGSVILLKOREN**.
3. Acceptera villkoren genom att klicka på kryssrutan.
4. I ”installera services” visas klickar du på **jobbet AUTENTISERINGSUPPGIFTER**.
   
    ![Installera tjänsterna][1]
5. Ange ett användarnamn och lösenord för databas-administratör. Som en del av installationen skapas en ny Azure SQL Database-server. I den här nya servern, en ny databas kallas kontroll-databasen skapas och används för att innehålla metadata för elastisk databas jobb. Användarnamn och lösenord som skapas här används för att logga in på databasen för kontrollen. En separat autentiseringsuppgifter används för körning av skript mot databaser i poolen.
   
    ![Skapa användarnamn och lösenord][2]
6. Klicka på OK. Komponenterna som skapas automatiskt om några minuter i en ny [resursgruppen](../azure-resource-manager/resource-group-overview.md). Den nya resursgruppen är fäst på start-planen som visas nedan. När du skapade, elastisk databas jobb (Cloud Service, SQL Database, Service Bus och Storage) skapas i gruppen.
   
    ![resursgrupp i start-kort][3]
7. Om du försöker skapa eller hantera ett jobb när elastisk databas jobb installeras när de tillhandahåller **autentiseringsuppgifter** visas följande meddelande.
   
    ![Distributionen fortfarande pågår][4]

Ta bort resursgruppen om avinstallation krävs. Se [avinstallera elastiska jobb databaskomponenterna](sql-database-elastic-jobs-uninstall.md).

## <a name="next-steps"></a>Nästa steg
Se till att en autentiseringsuppgift med rätt behörighet för körning av skript skapas på varje databas i gruppen, mer information finns i [skydda SQL-databasen](sql-database-manage-logins.md).
Se [skapa och hantera en elastisk databas jobb](sql-database-elastic-jobs-create-and-manage.md) att komma igång.

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/not-done.png

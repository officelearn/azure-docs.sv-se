---
title: Importera eller exportera en SQL-databas
description: Importera eller exportera en Azure SQL-databas utan att låta Azure-tjänster komma åt servern.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 61a85b2554bbd69541b3081f72525d2b7deed625
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529237"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Importera eller exportera en Azure SQL-databas utan att låta Azure-tjänster komma åt servern

Den här artikeln visar hur du importerar eller exporterar en Azure SQL-databas när *Tillåt Azure Services* är inställt på *OFF* på Azure SQL-servern. Arbetsflödet använder en virtuell Azure-dator för att köra SqlPackage för att utföra import- eller exportåtgärden.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-the-azure-virtual-machine"></a>Skapa den virtuella Azure-datorn

Skapa en virtuell Azure-dator genom att välja knappen **Distribuera till Azure.**

Med den här mallen kan du distribuera en enkel virtuell Windows-dator med några olika alternativ för Windows-versionen med den senaste korrigerade versionen. Detta distribuerar en virtuell A2-storlek på resursgruppsplatsen och returnerar det fullständigt kvalificerade domännamnet för den virtuella datorn.
<br><br>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Mer information finns i [Mycket enkel distribution av en Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows).


## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn

Följande steg visar hur du ansluter till din virtuella dator med hjälp av en fjärrskrivbordsanslutning.

1. När distributionen är klar går du till den virtuella datorresursen.

    ![Virtuell dator](./media/import-export-from-vm/vm.png)  

2. Välj **Anslut**.

   Ett filformulär för fjärrskrivbordsprotokoll (.rdp-fil) visas med den offentliga IP-adressen och portnumret för den virtuella datorn.

   ![RDP-formulär](./media/import-export-from-vm/rdp.png)  

3. Välj **Ladda ned RDP-fil**.

   > [!NOTE]
   > Du kan också använda SSH för att ansluta till din virtuella dator.

4. Stäng formuläret **Anslut till virtuell dator.**
5. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn.
6. När du uppmanas till det väljer du **Anslut**. På en Mac-dator behöver du en RDP-klient som denna [Fjärrskrivbordsklient](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) från Mac App Store.

7. Ange det användarnamn och lösenord som du angav när du skapade den virtuella datorn och välj sedan **OK**.

8. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** att fortsätta med anslutningen.



## <a name="install-sqlpackage"></a>Installera SqlPackage

[Ladda ner och installera den senaste versionen av SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).




Mer information finns i [SqlPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage).

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Skapa en brandväggsregel för att tillåta vm-åtkomst till databasen

Lägg till den virtuella datorns offentliga IP-adress i SQL Database-serverbrandväggen.

I följande steg skapas en IP-brandväggsregel på servernivå för den virtuella datorns offentliga IP-adress och möjliggör anslutning från den virtuella datorn.

1. Välj **SQL-databaser** på menyn till vänster och välj sedan databasen på **sidan SQL-databaser.** Översiktssidan för databasen öppnas och visar det fullständigt kvalificerade servernamnet (till exempel **servername.database.windows.net)** och innehåller alternativ för ytterligare konfiguration.

2. Kopiera det fullständigt kvalificerade servernamnet som ska användas när du ansluter till servern och dess databaser.

   ![servernamn](./media/sql-database-get-started-portal/server-name.png)

3. Välj **Konfigurera serverns brandvägg** i verktygsfältet. Sidan **Brandväggsinställningar** för databasservern öppnas.

   ![IP-brandväggsregel på servernivå](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Välj **Lägg till klient-IP** i verktygsfältet om du vill lägga till den virtuella datorns offentliga IP-adress i en ny IP-brandväggsregel på servernivå. Med en IP-brandväggsregel på servernivå kan du öppna port 1433 för en enskild IP-adress eller för ett IP-adressintervall.

5. Välj **Spara**. En IP-brandväggsregel på servernivå skapas för den virtuella datorns offentliga IP-adress som öppnar port 1433 på SQL Database-servern.

6. Stäng sidan **Brandväggsinställningar**.



## <a name="export-a-database-using-sqlpackage"></a>Exportera en databas med SqlPackage

Information om hur du exporterar en SQL-databas med kommandoradsverktyget [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) finns i [Exportera parametrar och egenskaper](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). SqlPackage-verktyget levereras med de senaste versionerna av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) och SQL Server Data [Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt), eller så kan du hämta den senaste versionen av [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Vi rekommenderar att du använder verktyget SqlPackage för skalning och prestanda i de flesta produktionsmiljöer. En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).

I det här exemplet visas hur du exporterar en databas med SqlPackage.exe med Universell Active Directory-autentisering. Ersätt med värden som är specifika för din miljö.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```




## <a name="import-a-database-using-sqlpackage"></a>Importera en databas med SqlPackage

Information om hur du importerar en SQL Server-databas med kommandoradsverktyget [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) finns i [importparametrar och egenskaper](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage har de senaste [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) och SQL Server Data [Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt). Du kan också ladda ner den senaste versionen av [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

För skalning och prestanda rekommenderar vi att du använder SqlPackage i de flesta produktionsmiljöer i stället för att använda Azure-portalen. En blogg för SQL Server Customer Advisory `BACPAC` Team om migrering med filer finns [i migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Följande SqlPackage-kommando importerar **AdventureWorks2017-databasen** från lokal lagring till en Azure SQL Database-server. Det skapar en ny databas som kallas **myMigratedDatabase** med en **Premium-tjänstnivå** och en **P6** Service Objective. Ändra dessa värden som lämpliga för din miljö.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Om du vill ansluta till en SQL Database-server som hanterar en enskild databas bakom en företagsbrandvägg måste brandväggen ha port 1433 öppen. Om du vill ansluta till en hanterad instans måste du ha en [punkt-till-plats-anslutning](sql-database-managed-instance-configure-p2s.md) eller en expressvägsanslutning.

I det här exemplet visas hur du importerar en databas med SqlPackage med Universell Active Directory-autentisering.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

Exporthastigheterna varierar beroende på många faktorer (till exempel dataform) så det är omöjligt att förutsäga vilken hastighet som ska förväntas. SqlPackage kan ta lång tid, särskilt för stora databaser.

För att få bästa resultat kan du prova följande strategier:

1. Kontrollera att ingen annan arbetsbelastning körs i databasen. Skapa en kopia före export kan vara den bästa lösningen för att säkerställa att inga andra arbetsbelastningar körs.
2. Öka målnivån för databastjänst (SLO) för att bättre hantera exportarbetsbelastningen (i första hand läs I/O). Om databasen för närvarande GP_Gen5_4 kanske en affärskritisk nivå skulle hjälpa till med läsarbetsbelastningen.
3. Kontrollera att det finns klustrade index, särskilt för stora tabeller. 
4. Virtuella datorer bör vara i samma region som databasen för att undvika nätverksbegränsningar.
5. Virtuella datorer bör ha SSD med tillräcklig storlek för att generera temp artefakter innan du laddar upp till blob lagring.
6. Virtuella datorer bör ha tillräcklig kärn- och minneskonfiguration för den specifika databasen.

## <a name="store-the-imported-or-exported-bacpac-file"></a>Lagra den importerade eller exporterade . BACPAC-fil

Den . BACPAC-filen kan lagras i [Azure Blobbar](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)eller [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction). 

Använd Azure Files för att uppnå bästa prestanda. SqlPackage fungerar med filsystemet så att det kan komma åt Azure-filer direkt.

För att minska kostnaderna använder du Azure Blobbar, som kostar mindre än en premium Azure-filresurs. Det kommer dock att kräva att du [kopierar . BACPAC-filen](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) mellan blobben och det lokala filsystemet före import- eller exportåtgärden. Som ett resultat av processen kommer att ta längre tid.

Så här laddar du upp eller laddar ned . BACPAC-filer, se [Överföra data med AzCopy- och Blob-lagring](../storage/common/storage-use-azcopy-blobs.md)och [Överför data med AzCopy och fillagring](../storage/common/storage-use-azcopy-files.md).

Beroende på din miljö kan du behöva [konfigurera Azure Storage-brandväggar och virtuella nätverk](../storage/common/storage-network-security.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du ansluter till och frågar en importerad SQL-databas finns i [Snabbstart: Azure SQL Database: Använd SQL Server Management Studio för att ansluta och fråga data](sql-database-connect-query-ssms.md).
- En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (på engelska).
- En diskussion om hela SQL Server-databasmigreringsprocessen, inklusive prestandarekommendationer, finns i [SQL Server-databasmigrering till Azure SQL Database](sql-database-single-database-migrate.md).
- Mer information om hur du hanterar och delar lagringsnycklar och signaturer för delad åtkomst på ett säkert sätt finns i [Azure Storage Security Guide](https://docs.microsoft.com/azure/storage/common/storage-security-guide).

---
title: Anslut till SQL Database med C ochC++
description: Använd exempel koden i den här snabb starten för att skapa ett modernt program C++ med och som backas upp av en kraftfull Relations databas i molnet med Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: cpp
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/12/2018
ms.openlocfilehash: fb6094ec418d2b212759bddd2c4d49c7e6193849
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690696"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Anslut till SQL Database med C ochC++

Det här inlägget syftar på C C++ och utvecklare försöker ansluta till Azure SQL DB. Den är uppdelad i avsnitt så att du kan gå till det avsnitt som bäst samlar in ditt intresse.

## <a name="prerequisites-for-the-cc-tutorial"></a>Krav för C/C++ självstudie

Se till att du har följande objekt:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). Du måste installera C++ språk komponenterna för att kunna skapa och köra det här exemplet.
* [Visual Studio Linux-utveckling](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Om du utvecklar på Linux måste du också installera Visual Studio Linux-tillägget.

## <a id="AzureSQL"></a>Azure SQL Database och SQL Server på virtuella datorer
Azure SQL bygger på Microsoft SQL Server och är utformat för att tillhandahålla en hög tillgänglig, effektiv och skalbar tjänst. Det finns många fördelar med att använda SQL Azure över din egna databas som körs lokalt. Med SQL Azure behöver du inte installera, konfigurera, underhålla eller hantera din databas, men endast innehållet och strukturen i databasen. Vanliga saker som vi behöver veta om med databaser som fel tolerans och redundans är inbyggda.

Azure har för närvarande två alternativ för att vara värd för SQL Server-arbetsbelastningar: Azure SQL Database, databas som en tjänst och SQL Server på Virtual Machines (VM). Vi kommer inte att få detaljerad information om skillnaderna mellan dessa två förutom att Azure SQL Database är det bästa valet för nya molnbaserade program för att dra nytta av de kostnads besparingar och prestanda optimeringar som moln tjänster erbjuder. Om du överväger att migrera eller utöka dina lokala program till molnet kan SQL Server på den virtuella Azure-datorn fungera bättre för dig. För att det ska vara enkelt för den här artikeln ska vi skapa en Azure SQL-databas.

## <a id="ODBC"></a>Data åtkomst tekniker: ODBC och OLE DB
Anslutning till Azure SQL DB är inte detsamma och det finns för närvarande två sätt att ansluta till databaser: ODBC (Open Database Connectivity) och OLE DB (objekt länkning och inbäddning av databas). Under de senaste åren har Microsoft justerats med [ODBC för intern Relations data åtkomst](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/). ODBC är relativt enkelt och också mycket snabbare än OLE DB. Den enda villkoret här är att ODBC använder ett gammalt C-format-API.

## <a id="Create"></a>Steg 1: skapa din Azure SQL Database
Se [sidan komma igång](sql-database-single-database-get-started.md) för att lära dig hur du skapar en exempel databas.  Alternativt kan du följa den här [korta två minuter långa videon](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) för att skapa en Azure SQL-databas med hjälp av Azure Portal.

## <a id="ConnectionString"></a>Steg 2: hämta anslutnings sträng
När din Azure SQL-databas har etablerats måste du utföra följande steg för att fastställa anslutnings information och lägga till klientens IP-adress för brand Väggs åtkomst.

I [Azure Portal](https://portal.azure.com/)går du till din Azure SQL Database ODBC-anslutningssträng genom att använda **databas anslutnings** strängarna som visas som en del av översikts avsnittet för databasen:

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Kopiera innehållet i **ODBC (inkluderar Node. js) [SQL Authentication]** -sträng. Vi använder den här strängen senare för att ansluta C++ från vår ODBC kommando tolk. Den här strängen innehåller information som driv rutin, server och andra databas anslutnings parametrar.

## <a id="Firewall"></a>Steg 3: Lägg till din IP-adress i brand väggen
Gå till avsnittet brand vägg för din databas server och Lägg till [klientens IP-adress i brand väggen med hjälp av de här stegen](sql-database-configure-firewall-settings.md) för att se till att vi kan upprätta en lyckad anslutning:

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

Nu har du konfigurerat Azure SQL DB och är redo att ansluta från din C++ kod.

## <a id="Windows"></a>Steg 4: ansluta från ett Windows C/C++ -program
Du kan enkelt ansluta till din [Azure SQL-databas med hjälp av ODBC i Windows med hjälp av det här exemplet](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) som bygger med Visual Studio. Exemplet implementerar en ODBC kommando rads tolk som kan användas för att ansluta till vår Azure SQL-databas. Det här exemplet tar antingen en DSN-fil (Database Source Name File) som ett kommando rads argument eller den utförlig anslutnings sträng som vi kopierade tidigare från Azure Portal. Öppna egenskaps sidan för det här projektet och klistra in anslutnings strängen som ett kommando argument som visas här:

![DSN-Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Se till att du anger rätt autentiseringsinformation för databasen som en del av databas anslutnings strängen.

Starta programmet för att bygga det. Du bör se följande fönster som verifierar en lyckad anslutning. Du kan till och med köra vissa grundläggande SQL-kommandon som **Skapa tabell** för att verifiera din databas anslutning:

![SQL-kommandon](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Du kan också skapa en DSN-fil med hjälp av guiden som startas när inga kommando argument har angetts. Vi rekommenderar också att du testar det här alternativet. Du kan använda den här DSN-filen för automatisering och skydda dina autentiseringsinställningar:

![Skapa DSN-fil](./media/sql-database-develop-cplusplus-simple/datasource.png)

Grattis! Nu har du anslutit till Azure SQL med C++ och ODBC i Windows. Du kan fortsätta att läsa för att göra samma för Linux-plattformen också.

## <a id="Linux"></a>Steg 5: ansluta från ett Linux C/C++ Application
Om du inte har hört med nyheterna än kan du nu utveckla C++ Linux-program med Visual Studio. Du kan läsa om det här nya scenariot i bloggen [för utveckling av C++ visuella objekt för Linux](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) . För att bygga för Linux behöver du en fjärrdator där Linux-distribution körs. Om du inte har någon tillgänglig kan du snabbt ställa in ett snabbt med [virtuella Linux Azure-datorer](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

I den här självstudien antar vi att du har konfigurerat en Ubuntu 16,04 Linux-distribution. Stegen här gäller även för Ubuntu 15,10, Red Hat 6 och Red Hat 7.

Följande steg installerar de bibliotek som krävs för SQL och ODBC för din distribution:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Starta Visual Studio. Under Verktyg-> Alternativ-> plattforms oberoende-> anslutnings hanteraren, lägger du till en anslutning till din Linux-ruta:

![Verktyg alternativ](./media/sql-database-develop-cplusplus-simple/tools.png)

När anslutningen över SSH har upprättats skapar du en tom projekt-mall (Linux):

![Mall för ny projekt](./media/sql-database-develop-cplusplus-simple/template.png)

Du kan sedan lägga till en [ny C-källfil och ersätta den med det här innehållet](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Med hjälp av ODBC-API: erna SQLAllocHandle, SQLSetConnectAttr och SQLDriverConnect, bör du kunna initiera och upprätta en anslutning till databasen.
Precis som med Windows ODBC-exemplet måste du ersätta SQLDriverConnect-anropet med information från parametrarna för databas anslutnings strängen som kopierats från Azure Portal tidigare.

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Det sista du gör innan du kompilerar är att lägga till **ODBC** som ett biblioteks beroende:

![Lägga till ODBC som indatamängds bibliotek](./media/sql-database-develop-cplusplus-simple/lib.png)

Starta programmet genom att öppna Linux-konsolen från **fel söknings** menyn:

![Linux-konsol](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Om anslutningen lyckades bör du nu se det aktuella databas namnet som skrivs ut i Linux-konsolen:

![Utdata för Linux-konsol fönstret](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Grattis! Du har slutfört självstudien och kan nu ansluta till din Azure SQL- C++ databas från på Windows-och Linux-plattformar.

## <a id="GetSolution"></a>Hämta den fullständiga lösningen förC++ C/självstudie
Du kan hitta GetStarted-lösningen som innehåller alla exempel i den här artikeln på GitHub:

* [ODBC C++ Windows-exempel](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), ladda ned C++ Windows ODBC-exemplet för att ansluta till Azure SQL
* [ODBC C++ Linux-exempel](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), Hämta Linux C++ ODBC-exemplet för att ansluta till Azure SQL

## <a name="next-steps"></a>Nästa steg
* Läs [översikten över SQL Database utveckling](sql-database-develop-overview.md)
* Mer information om [ODBC API-referensen](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Ytterligare resurser
* [Designmönster för SaaS-program med flera klientorganisationer med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Utforska alla [funktioner i SQL Database](https://azure.microsoft.com/services/sql-database/)


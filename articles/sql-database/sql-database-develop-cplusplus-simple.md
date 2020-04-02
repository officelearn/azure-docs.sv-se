---
title: Ansluta till SQL Database med C och C++
description: Använd exempelkoden i den här snabbstarten för att skapa ett modernt program med C++ och backas upp av en kraftfull relationsdatabas i molnet med Azure SQL Database.
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
ms.openlocfilehash: ed8e5eaa0ff9b58f80473b052aacfb9f01d45055
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529210"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Ansluta till SQL Database med C och C++

Det här inlägget riktar sig till C- och C++-utvecklare som försöker ansluta till Azure SQL DB. Det är uppdelat i sektioner så att du kan hoppa till det avsnitt som bäst fångar ditt intresse.

## <a name="prerequisites-for-the-cc-tutorial"></a>Förutsättningar för självstudien C/C++

Se till att du har följande objekt:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). Du måste installera C++ språkkomponenter för att kunna skapa och köra det här exemplet.
* [Visual Studio Linux Utveckling](https://docs.microsoft.com/cpp/linux/?view=vs-2019). Om du utvecklar på Linux, måste du också installera Visual Studio Linux förlängning.

## <a name="azure-sql-database-and-sql-server-on-virtual-machines"></a><a id="AzureSQL"></a>Azure SQL Database och SQL Server på virtuella datorer
Azure SQL bygger på Microsoft SQL Server och är utformat för att ge en tjänst med hög tillgänglighet, prestanda och skalbar. Det finns många fördelar med att använda SQL Azure via din egen databas som körs lokalt. Med SQL Azure behöver du inte installera, konfigurera, underhålla eller hantera databasen, utan bara innehållet och strukturen i databasen. Typiska saker som vi oroar oss för med databaser som feltolerans och redundans är alla inbyggda.

Azure har för närvarande två alternativ för att vara värd för SQL-serverarbetsbelastningar: Azure SQL-databas, databas som tjänst och SQL-server på virtuella datorer (VM). Vi kommer inte att gå in på detaljer om skillnaderna mellan dessa två förutom att Azure SQL-databas är din bästa insats för nya molnbaserade program för att dra nytta av kostnadsbesparingar och prestandaoptimering som molntjänster tillhandahåller. Om du funderar på att migrera eller utöka dina lokala program till molnet kan SQL-servern på den virtuella Azure-datorn fungera bättre för dig. Om du vill hålla det enkelt för den här artikeln ska vi skapa en Azure SQL-databas.

## <a name="data-access-technologies-odbc-and-ole-db"></a><a id="ODBC"></a>Dataåtkomstteknik: ODBC och OLE DB
Att ansluta till Azure SQL DB är inte annorlunda och för närvarande finns det två sätt att ansluta till databaser: ODBC (Open Database connectivity) och OLE DB (Object Linking and Embedding database). Under de senaste åren har Microsoft anpassat sig till [ODBC för inbyggt relationsdataåtkomst](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/). ODBC är relativt enkel, och också mycket snabbare än OLE DB. Den enda varning här är att ODBC använder en gammal C-stil API.

## <a name="step-1--creating-your-azure-sql-database"></a><a id="Create"></a>Steg 1: Skapa din Azure SQL-databas
Se [komma igång-sidan](sql-database-single-database-get-started.md) om du vill lära dig hur du skapar en exempeldatabas.  Alternativt kan du följa den här [korta tvåminutersvideon](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) för att skapa en Azure SQL-databas med Azure-portalen.

## <a name="step-2--get-connection-string"></a><a id="ConnectionString"></a>Steg 2: Hämta anslutningssträng
När din Azure SQL-databas har etablerats måste du utföra följande steg för att fastställa anslutningsinformation och lägga till klient-IP för brandväggsåtkomst.

I [Azure-portalen](https://portal.azure.com/)går du till odbc-anslutningssträngen för Azure SQL-databasen med hjälp av **anslutningssträngarna Visa databas** som anges som en del av översiktsavsnittet för databasen:

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Kopiera innehållet i **ODBC-strängen (inkluderar nod.js) [SQL-autentisering].** Vi använder den här strängen senare för att ansluta från vår C++ ODBC-kommandoradstolk. Den här strängen innehåller information som drivrutin, server och andra databasanslutningsparametrar.

## <a name="step-3--add-your-ip-to-the-firewall"></a><a id="Firewall"></a>Steg 3: Lägg till din IP i brandväggen
Gå till brandväggsavsnittet för databasservern och lägg till [klient-IP i brandväggen med hjälp av dessa steg](sql-database-configure-firewall-settings.md) för att se till att vi kan upprätta en lyckad anslutning:

![Lägg till din IPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

Nu har du konfigurerat din Azure SQL DB och är redo att ansluta från din C++-kod.

## <a name="step-4-connecting-from-a-windows-cc-application"></a><a id="Windows"></a>Steg 4: Ansluta från ett Windows C++-program
Du kan enkelt ansluta till din [Azure SQL DB med ODBC i Windows med det här exemplet](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) som bygger med Visual Studio. Exemplet implementerar en ODBC-kommandoradstolk som kan användas för att ansluta till vår Azure SQL DB. Det här exemplet tar antingen en DSN-fil (Database Source name file) som ett kommandoradsargument eller den utförliga anslutningssträngen som vi kopierade tidigare från Azure-portalen. Visa egenskapssidan för det här projektet och klistra in anslutningssträngen som ett kommandoargument som visas här:

![DSN-propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Se till att du anger rätt autentiseringsinformation för databasen som en del av databasanslutningssträngen.

Starta programmet för att bygga den. Du bör se följande fönster som validerar en lyckad anslutning. Du kan även köra några grundläggande SQL-kommandon som **skapa tabell** för att validera din databasanslutning:

![SQL-kommandon](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Du kan också skapa en DSN-fil med hjälp av guiden som startas när inga kommandoargument anges. Vi rekommenderar att du provar det här alternativet också. Du kan använda den här DSN-filen för automatisering och för att skydda dina autentiseringsinställningar:

![Skapa DSN-fil](./media/sql-database-develop-cplusplus-simple/datasource.png)

Grattis! Du har nu anslutit till Azure SQL med C++ och ODBC i Windows. Du kan fortsätta läsa för att göra samma sak för Linux-plattformen också.

## <a name="step-5-connecting-from-a-linux-cc-application"></a><a id="Linux"></a>Steg 5: Ansluta från ett Linux C/C++-program
Om du inte har hört nyheterna ännu, visual studio nu kan du utveckla C + + Linux-program också. Du kan läsa om det här nya scenariot i [bloggen Visual C++ för Linux Development.](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) För att bygga för Linux behöver du en fjärrdator där din Linux-distributioner körs. Om du inte har en tillgänglig kan du konfigurera en snabbt med [Linux Azure Virtual Machines](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

För den här guiden, låt oss anta att du har en Ubuntu 16,04 Linux-distribution inrättas. Stegen här bör också gälla Ubuntu 15,10, Red Hat 6 och Red Hat 7.

Följande steg installerar biblioteken som behövs för SQL och ODBC för distributionen:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Starta Visual Studio. Lägg till en anslutning till din Linux-box under Verktyg -> Alternativ -> Cross Platform -> Connection Manager:

![Alternativ för verktyg](./media/sql-database-develop-cplusplus-simple/tools.png)

När anslutningen över SSH har upprättats skapar du en Linux-mall (Empty Project):

![Ny projektmall](./media/sql-database-develop-cplusplus-simple/template.png)

Du kan sedan lägga till en [ny C-källfil och ersätta den med det här innehållet](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Med HJÄLP AV ODBC API:er SQLAllocHandle, SQLSetConnectAttr och SQLDriverConnect bör du kunna initiera och upprätta en anslutning till databasen.
Precis som med Windows ODBC-exemplet måste du ersätta SQLDriverConnect-anropet med information från databasanslutningssträngparametrarna som kopierats från Azure-portalen tidigare.

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Det sista du ska göra innan du kompilerar är att lägga **till odbc** som ett bibliotek beroende:

![Lägga till ODBC som indatabibliotek](./media/sql-database-develop-cplusplus-simple/lib.png)

Om du vill starta programmet tar du upp Linux-konsolen från **Felsökningsmenyn:**

![Linux-konsolen](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Om anslutningen lyckades bör du nu se det aktuella databasnamnet som skrivs ut i Linux-konsolen:

![Fönsterutdata för Linux-konsolen](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Grattis! Du har slutfört självstudien och kan nu ansluta till din Azure SQL DB från C++ på Windows- och Linux-plattformar.

## <a name="get-the-complete-cc-tutorial-solution"></a><a id="GetSolution"></a>Få den kompletta C/C++-självstudielösningen
Du hittar GetStarted-lösningen som innehåller alla exempel i den här artikeln på GitHub:

* [EXEMPEL PÅ ODBC C++ Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), Hämta Windows C++ ODBC-exemplet för att ansluta till Azure SQL
* [ODBC C++ Linux-exempel](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), Ladda ned Linux C++ ODBC-exemplet för att ansluta till Azure SQL

## <a name="next-steps"></a>Nästa steg
* Granska [utvecklingsöversikten för SQL-databas](sql-database-develop-overview.md)
* Mer information om [ODBC API-referens](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Ytterligare resurser
* [Designmönster för SaaS-program med flera klientorganisationer med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Utforska alla [funktioner i SQL Database](https://azure.microsoft.com/services/sql-database/)


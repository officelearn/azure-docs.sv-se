---
title: Ansluta till SQL-databas med C och C++ | Microsoft Docs
description: Använd exempelkoden i den här snabbstarten för att skapa ett modernt program med C++ och täcks av en kraftfull relationsdatabas i molnet med Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: cpp
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/12/2018
ms.openlocfilehash: 6ebef74cee6f919fe6b8cf666db06e0ab22cec73
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2019
ms.locfileid: "55566932"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Ansluta till SQL-databas med C och C++

Det här inlägget är att C och C++-utvecklare som försöker ansluta till Azure SQL DB. Det är uppdelad i avsnitt så att du kan gå till avsnittet som bäst samlar in ditt intresse.

## <a name="prerequisites-for-the-cc-tutorial"></a>Förutsättningar för självstudien om C/C++

Se till att du har följande objekt:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). Du måste installera språkkomponenter för att bygga och köra det här exemplet.
* [Linux-utveckling i Visual Studio](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Om du utvecklar på Linux, måste du också installera Visual Studio-Linux-tillägget.

## <a id="AzureSQL"></a>Azure SQL Database och SQL Server på virtuella datorer
Azure SQL bygger på Microsoft SQL Server och är utformad att ge en hög tillgänglighet, högpresterande och skalbara service. Det finns många fördelar med att använda SQL Azure till din egna databas som körs lokalt. Du behöver inte installera, konfigurera, underhålla och hantera din databas, men endast innehåll och strukturen för din databas med SQL Azure. Exempel på vanliga saker som vi oroa dig med databaser som feltolerans och redundans är inbyggda i.

Azure har för närvarande två alternativ för att hantera SQL server-arbetsbelastningar: Azure SQL-databas, databas som en tjänst och SQLServer på virtuella datorer (VM). Vi kommer inte att hämta information om skillnaderna mellan dessa två förutom att Azure SQL-databas är lämpligast för nya molnbaserade program att kunna utnyttja besparingarna och prestandaoptimering som molntjänster. Om du funderar på att migrera eller utöka ditt lokala program till molnet, kanske SQLServer på Azure-dator fungerar ut bättre för dig. Om du vill att göra det enkelt för den här artikeln ska vi skapa en Azure SQL database.

## <a id="ODBC"></a>Dataåtkomsttekniker: ODBC- och OLE DB
Ansluta till Azure SQL DB fungerar på samma sätt och det finns för närvarande två sätt att ansluta till databaser: ODBC (Open Database connectivity) och OLE DB (Object Linking och bädda in-database). På senare år har Microsoft justerad med [ODBC för inbyggd relationsdatabas dataåtkomst](https://blogs.msdn.microsoft.com/sqlnativeclient/2011/08/29/microsoft-is-aligning-with-odbc-for-native-relational-data-access/). ODBC är relativt enkla och även mycket snabbare än OLE DB. Den enda teamindela här är att ODBC använder en gammal C-stil-API.

## <a id="Create"></a>Steg 1:  Skapa din Azure SQL-databas
Se den [komma igång med](sql-database-get-started-portal.md) att lära dig hur du skapar en exempeldatabas.  Alternativt kan du följa den här [kort tvåminutersvideon](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) att skapa en Azure SQL-databas med Azure portal.

## <a id="ConnectionString"></a>Steg 2:  Hämta anslutningssträngen
När Azure SQL-databasen har etablerats, måste du utföra följande steg för att fastställa anslutningsinformationen och Lägg till klient-IP för brandväggsåtkomst.

I [Azure-portalen](https://portal.azure.com/)går du till Azure SQL database ODBC-anslutningssträng med hjälp av den **visa databasanslutningssträngar** visas som en del av översiktsavsnittet för databasen:

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Kopiera innehållet i den **ODBC (inkluderar Node.js) [SQL-autentisering]** sträng. Vi använder den här strängen senare för att ansluta från vårt C++ ODBC kommandoradstolken. Den här strängen innehåller information som drivrutinen, server och andra anslutningsparametrar för databasen.

## <a id="Firewall"></a>Steg 3:  Lägga till din IP-adress i brandväggen
Gå till avsnittet brandväggen för databasservern och Lägg till din [klientens IP-adress i brandväggen som använder de här stegen](sql-database-configure-firewall-settings.md) att kontrollera att vi kan upprätta en anslutning:

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

Nu kan du har konfigurerat din Azure SQL-databas och är redo att ansluta från din C++-kod.

## <a id="Windows"></a>Steg 4: Ansluter från en Windows C/C++-program
Du kan enkelt ansluta till din [Azure SQL DB med hjälp av ODBC på Windows med hjälp av det här exemplet](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) som skapas med Visual Studio. Exemplet implementerar en ODBC-kommandoradstolken som kan användas för att ansluta till vår Azure SQL DB. Det här exemplet använder antingen en databas namn på filen (DSN) källfil som ett argument på kommandoraden eller utförlig anslutningssträngen som vi tidigare kopierade från Azure-portalen. Öppna egenskapssidan för det här projektet och klistra in anslutningssträngen som ett kommandoargument som visas här:

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Kontrollera att du anger rätt autentisering-information för din databas som en del av denna anslutningssträng för databasen.

Starta programmet för att skapa den. Du bör se följande fönster verifierar en lyckad anslutning. Du kan även köra vissa grundläggande SQL-kommandon som **Skapa tabell** till databasen anslutningen:

![SQL-kommandon](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Du kan också skapa en DSN-fil med hjälp av guiden som startas när inga kommandoargument anges. Vi rekommenderar att du prova även det här alternativet. Du kan använda den här DNS-filen för automation och skydda dina autentiseringsinställningar:

![Skapa DNS-fil](./media/sql-database-develop-cplusplus-simple/datasource.png)

Grattis! Du har nu anslutit till Azure SQL med C++ och ODBC på Windows. Du kan fortsätta läsa för att göra detsamma för Linux-plattformen.

## <a id="Linux"></a>Steg 5: Ansluta från en Linux C/C++-program
Om du inte har hört nyheterna ännu, kan Visual Studio du nu du utvecklar C++ Linux-program. Du kan läsa om den här nya scenariot i den [Visual C++ för Linux-utveckling](https://blogs.msdn.microsoft.com/vcblog/2016/03/30/visual-c-for-linux-development/) blogg. Om du vill skapa för Linux, måste en fjärrdator där din Linux-distribution som körs. Om du inte har någon tillgänglig, kan du ange ett snabbt med [Linux virtuella datorer med Azure](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Den här självstudien kan vi anta att du har en Ubuntu 16.04 Linux-distribution som ställer in. De här stegen bör också gälla 15.10 Ubuntu, Red Hat 6 och 7 för Red Hat.

Följande steg installerar de bibliotek som behövs för SQL och ODBC för din distribution:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Starta Visual Studio. Under Verktyg -> Alternativ -> Cross Platform -> Anslutningshanteraren, lägga till en anslutning till din Linux-box:

![Verktygsalternativ](./media/sql-database-develop-cplusplus-simple/tools.png)

När anslutningen via SSH har upprättats kan du skapa en tom projektmall (Linux):

![Ny projektmall](./media/sql-database-develop-cplusplus-simple/template.png)

Du kan sedan lägga till en [nya C källfil och Ersätt den med det här innehållet](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Med ODBC-API: er SQLAllocHandle, SQLSetConnectAttr och SQLDriverConnect, ska du kunna initiera och upprätta en anslutning till databasen.
Som med Windows ODBC-exempel måste du ersätta SQLDriverConnect anropet med information från din Databasparametrar för anslutningssträngen kopieras från Azure portal tidigare.

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Det sista göra innan du kompilerar är att lägga till **odbc** som ett beroende bibliotek:

![Om du lägger till ODBC som ett inkommande bibliotek](./media/sql-database-develop-cplusplus-simple/lib.png)

Om du vill starta programmet, ta fram Linux-konsolen från den **felsöka** menyn:

![Linux-konsolen](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Om din anslutning har upprättats, bör du nu se det aktuella databasnamnet ut i Linux-konsolen:

![Linux Konsolfönstrets utdata](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Grattis! Du har slutfört självstudien och kan nu ansluta till din Azure SQL-databas från C++ på Windows och Linux-plattformar.

## <a id="GetSolution"></a>Hämta fullständiga lösningen för C/C++-självstudiekursen
Du kan hitta GetStarted-lösningen med alla exempel i den här artikeln på GitHub:

* [ODBC-C++ Windows exemplet](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), ladda ned Windows C++ ODBC-exemplet för att ansluta till Azure SQL
* [ODBC-C++ Linux exemplet](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), ladda ned Linux C++ ODBC-exemplet för att ansluta till Azure SQL

## <a name="next-steps"></a>Nästa steg
* Granska den [översikt över SQL Database-utveckling](sql-database-develop-overview.md)
* Mer information om den [ODBC API-referens](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Ytterligare resurser
* [Designmönster för SaaS-program med flera klientorganisationer med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Utforska alla [funktioner i SQL Database](https://azure.microsoft.com/services/sql-database/)


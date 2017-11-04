---
title: Ansluta till SQL Database med C och C++ | Microsoft Docs
description: "Använd exempelkoden i den här Snabbstartsguide för att skapa en modern program med C++ och säkerhetskopieras av en relationsdatabaser i molnet med Azure SQL Database."
services: sql-database
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: 
ms.assetid: 07d9e0b1-3234-4f17-a252-a7559160a9db
ms.service: sql-database
ms.custom: develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: article
ms.date: 03/06/2017
ms.author: edmacauley
ms.openlocfilehash: 7c78f90c14c1915c760720948d6d7ae99ceb6f1d
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Ansluta till SQL Database med C och C++
Det här exemplet syftar C och C++ utvecklare som försöker ansluta till Azure SQL DB. Den är uppdelad i avsnitt så att du kan gå till avsnittet som bäst samlar in ditt intresse. 

## <a name="prerequisites-for-the-cc-tutorial"></a>Kraven för C/C++-självstudier
Se till att du har följande objekt:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). Du måste installera komponenter för C++-språk för att bygga och köra det här exemplet.
* [Visual Studio Linux Development](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Om du utvecklar på Linux, måste du också installera Visual Studio Linux-tillägget. 

## <a id="AzureSQL"></a>Azure SQL Database och SQL Server på virtuella datorer
Azure SQL bygger på Microsoft SQL Server och är utformad att ge en hög tillgänglighet, performant och skalbar service. Det finns många fördelar med att använda SQL Azure via din egna databas som körs lokalt. Du behöver inte installera, konfigurera, underhålla eller hantera din databas men bara innehåll och struktur för databasen med SQL Azure. Vanliga saker som vi oroa dig med databaser som feltolerans och redundans är inbyggda i. 

Azure har två alternativ för värd för SQL server-arbetsbelastningar: Azure SQL-databas, databasen som en tjänst och SQLServer på virtuella datorer (VM). Vi kommer inte att hämta information om skillnaderna mellan dessa två förutom att Azure SQL-databas är det bästa alternativet för nya molnbaserade program kan dra nytta av besparingarna och prestandaoptimeringar som molntjänster. Om du funderar på att migrera eller utöka ditt lokala program till molnet, kanske SQLServer på virtuella Azure-datorn fungerar ut bättre för dig. Om du vill behålla saker enkel för den här artikeln ska vi skapa en Azure SQL database. 

## <a id="ODBC"></a>Tekniker för dataåtkomst: ODBC och OLE DB
Ansluta till Azure SQL DB är inte annorlunda och det finns två sätt att ansluta till databaser: ODBC (Open Database connectivity) och OLE DB (Object Linking och inbäddning databas). Under de senaste åren har Microsoft justerad med [ODBC för åtkomst till interna relationella data](https://blogs.msdn.microsoft.com/sqlnativeclient/2011/08/29/microsoft-is-aligning-with-odbc-for-native-relational-data-access/). ODBC är relativt enkel och även mycket snabbare än OLE DB. Den enda begränsning är ODBC använder en gamla API C-format. 

## <a id="Create"></a>Steg 1: Skapa din Azure SQL-databas
Finns det [komma igång med](sql-database-get-started-portal.md) att lära dig hur du skapar en exempeldatabas.  Du kan också följa detta [korta videon i två minuter](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) att skapa en Azure SQL-databas med Azure-portalen.

## <a id="ConnectionString"></a>Steg 2: Hämta anslutningssträngen
När Azure SQL database har etablerats, måste du utföra följande steg för att fastställa anslutningsinformationen och Lägg till din klientens IP-Adressen för brandväggen åtkomst. 

I [Azure-portalen](https://portal.azure.com/), gå till Azure SQL database ODBC-anslutningssträngen med hjälp av den **visa databasanslutningssträngar** visas som en del av översiktsavsnittet för databasen: 

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Kopiera innehållet i den **ODBC (innehåller Node.js) [SQL-autentisering]** sträng. Vi använder den här strängen senare för att ansluta från våra kommandoradsverktyget C++ ODBC-tolken. Den här strängen innehåller information som drivrutinen, server och andra parametrar för anslutningen. 

## <a id="Firewall"></a>Steg 3: Lägga till IP-brandväggen
Gå till avsnittet Brandvägg för databasservern och Lägg till din [klientens IP-Adressen för brandväggen med hjälp av de här stegen](sql-database-configure-firewall-settings.md) att se till att vi kan upprätta en anslutning: 

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

Nu har konfigurerat din Azure SQL-databas och är redo att ansluta från din kod i C++. 

## <a id="Windows"></a>Steg 4: Ansluter från en Windows C/C++-program
Du kan enkelt ansluta till din [Azure SQL-databas med hjälp av ODBC i Windows med det här exemplet](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) som bygger med Visual Studio. Exemplet implementerar en ODBC-kommandoradsverktyget tolk som kan användas för att ansluta till vår Azure SQL DB. Det här exemplet krävs antingen en databas namnet fil (DSN) källfil som kommandoradsargument eller utförlig anslutningssträngen som vi tidigare kopieras från Azure-portalen. Öppna egenskapssidan för det här projektet och klistra in anslutningssträngen som ett kommandoargument som visas här: 

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Kontrollera att du anger rätt autentisering-information för databasen som en del av den anslutningssträngen för databasen. 

Starta programmet för att skapa den. Du bör se följande fönster för verifiera anslutningen. Du kan även köra vissa grundläggande SQL-kommandon som **Skapa tabell** att verifiera din databasanslutning:

![SQL-kommandon](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Du kan också skapa en DSN-fil med hjälp av guiden startas när inga kommandoargument har angetts. Vi rekommenderar att det här alternativet och försök. Du kan använda den här DSN-filen för automatisering och skydda dina inställningar för autentisering: 

![Skapa DSN-fil](./media/sql-database-develop-cplusplus-simple/datasource.png)

Grattis! Du har nu har anslutit till Azure SQL med C++ och ODBC i Windows. Du kan fortsätta läsa för att göra samma för Linux-plattformen. 

## <a id="Linux"></a>Steg 5: Anslutning från ett C/C++ för Linux-program
Om du inte har hört nyheter ännu, kan Visual Studio du nu att utveckla C++ Linux-program. Du kan läsa om den här nytt scenario i den [Visual C++ för Linux-utveckling](https://blogs.msdn.microsoft.com/vcblog/2016/03/30/visual-c-for-linux-development/) blogg. Om du vill skapa för Linux måste en fjärrdator där Linux-distro körs. Om du inte har någon tillgänglig, kan du ange ett dig snabbt med [Linux Azure virtuella datorer](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

För den här självstudiekursen Låt oss förutsätter att du har en distributionsplats för Ubuntu 16.04 Linux ställa in. De här stegen bör också gälla Ubuntu 15.10, Red Hat 6 och 7 för Red Hat. 

Följande steg installera de bibliotek som krävs för SQL och ODBC för din distro:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Starta Visual Studio. Under Verktyg -> Alternativ -> Cross Platform -> Anslutningshanteraren, lägga till en anslutning till Linux-box: 

![Alternativ](./media/sql-database-develop-cplusplus-simple/tools.png)

När SSH-anslutningen har upprättats kan du skapa en anges projektmallen (Linux): 

![Ny projektmall](./media/sql-database-develop-cplusplus-simple/template.png)

Du kan sedan lägga till en [nya C källfil och Ersätt den med det här innehållet](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Med ODBC-API: er SQLAllocHandle, SQLSetConnectAttr och SQLDriverConnect ska du kunna initiera och upprätta en anslutning till databasen. Precis som med hjälp av Windows ODBC-exempel måste du ersätta SQLDriverConnect-anrop med information från din databas sträng anslutningsparametrar kopieras från Azure portal tidigare. 

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Det sista du behöver göra innan du kompilerar är att lägga till **odbc** som biblioteket beroende: 

![Lägga till ODBC som ett inkommande bibliotek](./media/sql-database-develop-cplusplus-simple/lib.png)

Om du vill starta programmet registreringspunkter Linux-konsolen från den **felsöka** menyn: 

![Linux-konsolen](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Om anslutningen har upprättats bör nu se databasnamnet på den aktuella ut i Linux-konsolen: 

![Utdata från Linux-konsolen](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Grattis! Du har slutfört självstudierna och kan nu ansluta till din Azure SQL-databas från C++ på Windows- och Linux-plattformar.

## <a id="GetSolution"></a>Hämta den fullständiga självstudiekursen C/C++-lösningen
Du kan hitta GetStarted-lösningen som innehåller alla exempel i den här artikeln på github:

* [ODBC-C++ Windows exempel](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), hämta Windows C++ ODBC-exempel för att ansluta till SQL Azure
* [ODBC-C++ Linux exempel](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), hämta Linux C++ ODBC-exempel för att ansluta till SQL Azure

## <a name="next-steps"></a>Nästa steg
* Granska de [Utvecklingsöversikt för SQL-databas](sql-database-develop-overview.md)
* Mer information om den [ODBC API-referens](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Ytterligare resurser
* [Designmönster för SaaS-program med flera klientorganisationer med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Utforska alla [funktioner i SQL Database](https://azure.microsoft.com/services/sql-database/)


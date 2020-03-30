---
title: Felsöka intermittenta utgående anslutningsfel i Azure App Service
description: Felsöka intermittenta anslutningsfel och relaterade prestandaproblem i Azure App Service
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 03/24/2020
ms.author: ramakoni
ms.custom: security-recommendations
ms.openlocfilehash: 028ddccdb989d35710e387081b08a3b973d75bdc
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367556"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Felsöka intermittenta utgående anslutningsfel i Azure App Service

Den här artikeln hjälper dig att felsöka intermittenta anslutningsfel och relaterade prestandaproblem i [Azure App Service](https://docs.microsoft.com/azure/app-service/overview). Det här avsnittet kommer att ge mer information om och felsökningsmetoder för utmattning av SNAT-portar (Source Address Network Translation). Om du behöver mer hjälp när som helst i den här artikeln kontaktar du Azure-experterna på [MSDN Azure och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure Support-webbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.

## <a name="symptoms"></a>Symtom

Program och funktioner som finns på Azure App-tjänsten kan uppvisa ett eller flera av följande symptom:

* Långsamma svarstider för alla eller några av instanserna i en serviceplan.
* Återkommande fel i 5xx eller **felaktig gateway**
* Felmeddelanden om timeout
* Det gick inte att ansluta till externa slutpunkter (som SQLDB, Service Fabric, andra App-tjänster osv.)

## <a name="cause"></a>Orsak

En viktig orsak till dessa symptom är att programinstansen inte kan öppna en ny anslutning till den externa slutpunkten eftersom den har nått någon av följande gränser:

* TCP-anslutningar: Det finns en gräns för hur många utgående anslutningar som kan göras. Detta är associerat med storleken på den arbetare som används.
* SNAT-portar: Som diskuterats i [Utgående anslutningar i Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)använder Azure översättning av källnätverksadresser (SNAT) och en belastningsutjämnare (inte exponerad för kunder) för att kommunicera med slutpunkter utanför Azure i det offentliga IP-adressutrymmet. Varje instans på Azure App-tjänsten får inledningsvis ett förallokerat antal **128** SNAT-portar. Den gränsen påverkar öppnandet av anslutningar till samma värd- och portkombination. Om appen skapar anslutningar till en blandning av adress- och portkombinationer kommer du inte att använda upp dina SNAT-portar. SNAT-portarna används när du har upprepade anrop till samma adress och portkombination. När en port har släppts är porten tillgänglig för återanvändning efter behov. Azure Network load balancer återtar SNAT-porten från stängda anslutningar först efter att ha väntat i 4 minuter.

När program eller funktioner snabbt öppnar en ny anslutning kan de snabbt uttömma sin förallokerade kvot för de 128 portarna. De blockeras sedan tills en ny SNAT-port blir tillgänglig, antingen genom dynamisk allokering av ytterligare SNAT-portar eller genom återanvändning av en återvunnen SNAT-port. Program eller funktioner som blockeras på grund av den här oförmågan att skapa nya anslutningar börjar uppleva ett eller flera av de problem som beskrivs i avsnittet **Symptom** i den här artikeln.

## <a name="avoiding-the-problem"></a>Undvika problemet

Att undvika SNAT-portproblemet innebär att man undviker att skapa nya anslutningar upprepade gånger till samma värd och port.

Allmänna strategier för att minska SNAT-portutmattning diskuteras i avsnittet Problemlösning i **de utgående anslutningarna för** [Azure-dokumentation.](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#problemsolving) Av dessa strategier gäller följande för appar och funktioner som finns på Azure App-tjänsten.

### <a name="modify-the-application-to-use-connection-pooling"></a>Ändra programmet så att det använder anslutningspooler

* Om du vill slå samman HTTP-anslutningar granskar du [SIM-anslutningar med HttpClientFactory](https://docs.microsoft.com/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory).
* Information om SQL Server-anslutningspooler finns i [SQL Server Connection Pooling (ADO.NET).](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
* För att implementera sammanslagning med ramprogram för entiteter, granska [DbContext-sammanslagning](https://docs.microsoft.com/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling).

Här är en samling länkar för att implementera Anslutningspoolning med olika lösningsstack.

#### <a name="node"></a>Node

Som standard hålls inte anslutningar för NodeJS vid liv. Nedan finns populära databaser och paket för anslutningspoolning som innehåller exempel för hur du implementerar dem.

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [Mongodb](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP Keep-alive

* [agentkeepalive (agentkeepalive)](https://www.npmjs.com/package/agentkeepalive)
* [Node.js v13.9.0 Dokumentation](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Nedan finns de populära bibliotek som används för JDBC-anslutningspoolning som innehåller exempel för hur du implementerar dem: JDBC Connection Pooling.

* [Tomcat 8 (På andra)](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0 (på andra sätt)](https://github.com/swaldman/c3p0)
* [HikariCP (av hikaricp)](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

HTTP-anslutningspooler

* [Hantering av Apache-anslutning](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [KlasspoolninghttpClientConnectionManager](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

Även om PHP inte stöder anslutningspoolning kan du prova att använda beständiga databasanslutningar till serverdelsservern.
 
* MySQL-server

   * [MySQLi-anslutningar](https://www.php.net/manual/mysqli.quickstart.connections.php) för nyare versioner
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php) för äldre versioner av PHP

* Andra datakällor

   * [HANTERING AV PHP-anslutning](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [Mongodb](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools) (OBS: SQLAlchemy kan användas med andra databaser förutom MicrosoftSQL Server)
* [HTTP Keep-alive](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)(Keep-Alive är automatisk när du använder [sessionssession-objekt](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)).

För andra miljöer bör du granska provider eller drivrutinsspecifika dokument för implementering av anslutningspoolning i dina program.

### <a name="modify-the-application-to-reuse-connections"></a>Ändra programmet för återanvändning av anslutningar

*  Om du vill ha ytterligare pekare och exempel på hur du hanterar anslutningar i Azure-funktioner läser du [Hantera anslutningar i Azure Functions](https://docs.microsoft.com/azure/azure-functions/manage-connections).

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>Ändra programmet så att det använder mindre aggressiv logik för återförsök

* Mer vägledning och exempel finns [i Mönster för återförsök](https://docs.microsoft.com/azure/architecture/patterns/retry).

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>Använd keepalives för att återställa den utgående tidsgränsen för inaktiv

* För att implementera keepalives för Node.js-appar läser du [Mitt nodprogram gör överdrivna utgående samtal](https://docs.microsoft.com/azure/app-service/app-service-web-nodejs-best-practices-and-troubleshoot-guide#my-node-application-is-making-excessive-outbound-calls).

### <a name="additional-guidance-specific-to-app-service"></a>Ytterligare vägledning som är specifik för App Service:

* Ett [belastningstest](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test) bör simulera verkliga data i en jämn matningshastighet. Testa appar och funktioner under verklig stress kan identifiera och lösa problem med SNAT-portutmattning i förväg.
* Se till att backend-tjänsterna snabbt kan returnera svar. Om du vill felsöka prestandaproblem med Azure SQL-databasen läser [du felsöka prestandaproblem i Azure SQL Database med Intelligent Insights](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance#recommended-troubleshooting-flow).
* Skala ut App Service-planen till fler instanser. Mer information om skalning finns [i Skala en app i Azure App Service](https://docs.microsoft.com/azure/app-service/manage-scale-up). Varje arbetsinstans i en apptjänstplan tilldelas ett antal SNAT-portar. Om du sprider din användning över fler instanser kan du få SNAT-portanvändningen per instans under den rekommenderade gränsen på 100 utgående anslutningar, per unik fjärrslutpunkt.
* Överväg att flytta till [Ase (App Service Environment),](https://docs.microsoft.com/azure/app-service/environment/using-an-ase)där du tilldelas en enda utgående IP-adress, och gränserna för anslutningar och SNAT-portar är mycket högre.

Att undvika de utgående TCP-gränserna är lättare att lösa, eftersom gränserna anges efter storleken på din arbetare. Du kan se gränserna i [Begränsat VM-numeriska gränser - TCP-anslutningar](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Begränsa namn|Beskrivning|Liten (A1)|Medium (A2)|Stor (A3)|Isolerad nivå (ASE)|
|---|---|---|---|---|---|
|Anslutningar|Antal anslutningar över hela den virtuella datorn|1920|3968|8064|16 000|

Om du vill undvika utgående TCP-gränser kan du antingen öka storleken på dina arbetare eller skala ut vågrätt.

## <a name="troubleshooting"></a>Felsökning

Att känna till de två typerna av utgående anslutningsgränser och vad appen gör bör göra det enklare att felsöka. Om du vet att appen ringer många samtal till samma lagringskonto kan du misstänka en SNAT-gräns. Om din app skapar många anrop till slutpunkter över hela Internet, misstänker du att du når den virtuella datorn gränsen.

Om du inte känner till programmets beteende tillräckligt för att snabbt fastställa orsaken finns det några verktyg och tekniker som finns tillgängliga i App Service för att hjälpa till med den bestämningen.

### <a name="find-snat-port-allocation-information"></a>Hitta information om SNAT-portallokering

Du kan använda [App Service Diagnostics](https://docs.microsoft.com/azure/app-service/overview-diagnostics) för att hitta SNAT-portallokeringsinformation och observera SNAT-porternas allokeringsmått för en App Service-webbplats. Så här hittar du information om SNAT-portallokering:

1. Om du vill komma åt App Service-diagnostik navigerar du till din App Service-webbapp eller App Service-miljö i [Azure-portalen](https://portal.azure.com/). I den vänstra navigeringen väljer du **Diagnostisera och lösa problem**.
2. Välj tillgänglighets- och prestandakategori
3. Välj panelen SNAT Port Exhaustion i listan över tillgängliga paneler under kategorin. Praxis är att hålla den under 128.
Om du behöver det kan du fortfarande öppna en supportbiljett och supportteknikern får måttet från backend för dig.

Observera att eftersom SNAT-portanvändning inte är tillgänglig som ett mått, är det inte möjligt att antingen automatiskt skala baserat på SNAT-portanvändning eller att konfigurera automatisk skala baserat på SNAT-portallokeringsmått.

### <a name="tcp-connections-and-snat-ports"></a>TCP-anslutningar och SNAT-portar

TCP-anslutningar och SNAT-portar är inte direkt relaterade. En TCP-anslutningsanvändningsdetektor ingår i bladet Diagnostisera och lösa problem på en App Service-webbplats. Sök efter frasen "TCP-anslutningar" för att hitta den.

* SNAT-portarna används endast för externa nätverksflöden, medan den totala TCP-anslutningar innehåller accessorbackanslutningar.
* En SNAT-port kan delas av olika flöden, om flödena är olika i antingen protokoll, IP-adress eller port. TCP-anslutningarnas mått räknar varje TCP-anslutning.
* TCP-anslutningsgränsen sker på arbetarinstansnivå. Azure Network utgående belastningsutjämning använder inte TCP-anslutningar måttet för SNAT-portbegränsning.
* TCP-anslutningsgränserna beskrivs i [Numeriska gränser för begränsat virtuell dator - TCP-anslutningar](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Begränsa namn|Beskrivning|Liten (A1)|Medium (A2)|Stor (A3)|Isolerad nivå (ASE)|
|---|---|---|---|---|---|
|Anslutningar|Antal anslutningar över hela den virtuella datorn|1920|3968|8064|16 000|

### <a name="webjobs-and-database-connections"></a>WebJobs- och databasanslutningar
 
Om SNAT-portar är uttömda, där WebJobs inte kan ansluta till Azure SQL-databasen, finns det inget mått som visar hur många anslutningar som öppnas av varje enskild webbprogramprocess. Om du vill hitta den problematiska WebJob flyttar du flera WebJobs ut till en annan App Service-plan för att se om situationen förbättras eller om ett problem kvarstår i ett av planerna. Upprepa processen tills du hittar det problematiska WebJob.

### <a name="using-snat-ports-sooner"></a>Använda SNAT-portar tidigare

Du kan inte ändra några Azure-inställningar för att släppa de använda SNAT-portarna tidigare, eftersom alla SNAT-portar kommer att släppas enligt nedanstående villkor och beteendet är avsiktligt.
 
* Om någon av servrarna eller klienten skickar FINACK [kommer SNAT-porten att släppas](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#tcp-snat-port-release) efter 240 sekunder.
* Om en RST visas kommer SNAT-porten att släppas efter 15 sekunder.
* Om inaktiv timeout har nåtts släpps porten.
 
## <a name="additional-information"></a>Ytterligare information

* [SNAT med apptjänst](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Felsöka problem med långsamma appprestanda i Azure App Service](https://docs.microsoft.com/azure/app-service/troubleshoot-performance-degradation)

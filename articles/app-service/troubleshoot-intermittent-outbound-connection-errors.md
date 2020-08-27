---
title: Felsöka återkommande utgående anslutnings fel i Azure App Service
description: Felsök tillfälliga anslutnings fel och relaterade prestanda problem i Azure App Service
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 07/24/2020
ms.author: ramakoni
ms.custom: security-recommendations,fasttrack-edit
ms.openlocfilehash: 467f7b3525883e16e57a06ff97cf4fd386279d22
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958243"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Felsöka återkommande utgående anslutnings fel i Azure App Service

Den här artikeln hjälper dig att felsöka tillfälliga anslutnings fel och relaterade prestanda problem i [Azure App Service](./overview.md). Det här avsnittet innehåller mer information om, och fel söknings metoder för, belastning av SNAT-portar (Source Address Network Translation). Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också fil en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.

## <a name="symptoms"></a>Symtom

Program och funktioner som finns på Azure App-tjänsten kan ha ett eller flera av följande problem:

* Långsamma svars tider för alla eller några av instanserna i en tjänste plan.
* Tillfälligt 5xx eller **dåliga Gateway** -fel
* Fel meddelanden för tids gräns
* Det gick inte att ansluta till externa slut punkter (t. ex. SQLDB, Service Fabric, andra app Services osv.)

## <a name="cause"></a>Orsak

En större orsak till dessa symptom är att program instansen inte kan öppna en ny anslutning till den externa slut punkten eftersom den har nått någon av följande gränser:

* TCP-anslutningar: det finns en gräns för hur många utgående anslutningar som kan göras. Detta är associerat med den arbets storlek som används.
* SNAT-portar: som beskrivs i [utgående anslutningar i Azure](../load-balancer/load-balancer-outbound-connections.md)använder azure käll Network Address TRANSLATION (SNAT) och en Load Balancer (visas inte för kunder) för att kommunicera med slut punkter utanför Azure i det offentliga IP-adressutrymmet. Varje instans i Azure Apps tjänsten tilldelas ursprungligen ett förallokerat antal **128** SNAT-portar. Den gränsen påverkar öppnande av anslutningar till samma värd-och port kombination. Om din app skapar anslutningar till en blandning av adress-och kombinations kombinationer kan du inte använda dina SNAT-portar. SNAT-portarna används när du upprepade samtal till samma adress-och port kombination. När en port har frigjorts är porten tillgänglig för åter användning vid behov. Azure Network Load Balancer återtar SNAT-porten från stängda anslutningar endast efter en väntan på 4 minuter.

När program eller funktioner snabbt öppnar en ny anslutning kan de snabbt försätta sin förallokerade kvot på 128-portarna. De blockeras sedan tills en ny SNAT-port blir tillgänglig, antingen genom att dynamiskt allokera ytterligare SNAT-portar eller genom åter användning av en reportad SNAT-port. Program eller funktioner som blockeras på grund av att det inte går att skapa nya anslutningar kommer att påbörjas ett eller flera av problemen som beskrivs i avsnittet **symptom** i den här artikeln.

## <a name="avoiding-the-problem"></a>Undvika problemet

Om målet är en Azure-tjänst som stöder tjänst slut punkter kan du undvika problem med SNAT-portar genom att använda [regional VNet-integrering](./web-sites-integrate-with-vnet.md) och tjänst slut punkter eller privata slut punkter. När du använder regional VNet-integrering och placerar tjänst slut punkter i integrations under nätet, kommer din app utgående trafik till dessa tjänster inte att ha några utgående SNAT-port begränsningar. Om du använder regional VNet-integrering och privata slut punkter får du inte heller några utgående SNAT-ports problem till det målet. 

Att undvika problem med SNAT-porten innebär att undvika att nya anslutningar skapas upprepade gånger till samma värd och port.

Allmänna strategier för att minska antalet SNAT-portar beskrivs i avsnittet om [problemlösning](../load-balancer/load-balancer-outbound-connections.md) i de **utgående anslutningarna i Azure** -dokumentationen. Av dessa strategier gäller följande för appar och funktioner som finns på Azure App-tjänsten.

### <a name="modify-the-application-to-use-connection-pooling"></a>Ändra programmet så att anslutningspoolen används

* För pooling av HTTP-anslutningar granskar du [HTTP-anslutningar för poolen med HttpClientFactory](/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory).
* Information om hur du SQL Server anslutningspoolen finns i [SQL Server anslutningspoolen (ADO.net)](/dotnet/framework/data/adonet/sql-server-connection-pooling).
* För att implementera pooler med Entity Framework-program, granska [DbContext-poolning](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling).

Här är en samling länkar för att implementera anslutningspoolen från en annan lösnings stack.

#### <a name="node"></a>Node

Som standard hålls inte anslutningar för NodeJS i livet. Nedan visas populära databaser och paket för anslutningspoolen som innehåller exempel på hur du implementerar dem.

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP Keep-Alive

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [ Dokumentation omNode.js v 13.9.0](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Nedan visas de populära biblioteken som används för JDBC-anslutningspoolen som innehåller exempel på hur du implementerar dem: JDBC anslutningspoolen.

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

Anslutningspoolen för HTTP-anslutning

* [Hantering av Apache-anslutning](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [Klass PoolingHttpClientConnectionManager](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

Även om PHP inte stöder anslutningspoolen kan du prova att använda beständiga databas anslutningar till backend-servern.
 
* MySQL-Server

   * [MySQL-anslutningar](https://www.php.net/manual/mysqli.quickstart.connections.php) för nyare versioner
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php) för äldre versioner av php

* Andra data källor

   * [Hantering av PHP-anslutning](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools) (Obs: SQLAlchemy kan användas med andra databaser förutom MicrosoftSQL-Server)
* [Http Keep-Alive](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)(Keep-Alive är automatiskt när du använder sessioner [-objekt](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)).

För andra miljöer granskar du provider-eller drivrutinsspecifika dokument för att implementera anslutningspoolen i dina program.

### <a name="modify-the-application-to-reuse-connections"></a>Ändra programmet för att återanvända anslutningar

*  Ytterligare pekare och exempel på hur du hanterar anslutningar i Azure Functions finns [i hantera anslutningar i Azure Functions](../azure-functions/manage-connections.md).

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>Ändra programmet för att använda mindre aggressiva omprövnings logik

* Mer information och exempel finns i [mönster för återförsök](/azure/architecture/patterns/retry).

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>Använd keepalive för att återställa tids gränsen för utgående inaktivitet

* För att implementera keepalive-program för Node.js-appar gör granskningen av [mitt Node-program att det gör alltför utgående samtal](./app-service-web-nodejs-best-practices-and-troubleshoot-guide.md#my-node-application-is-making-excessive-outbound-calls).

### <a name="additional-guidance-specific-to-app-service"></a>Ytterligare vägledning som är speciell för App Service:

* Ett [belastnings test](/azure/devops/test/load-test/app-service-web-app-performance-test) bör simulera verkliga världs data i en konstant matnings hastighet. Testning av appar och funktioner under verklig världs belastning kan identifiera och lösa problem med SNAT-port överbelastning i förväg.
* Se till att Server dels tjänsterna kan returnera svar snabbt. Om du vill felsöka prestanda problem med Azure SQL Database granskar du [fel sökning Azure SQL Database prestanda problem med intelligent Insights](../azure-sql/database/intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow).
* Skala ut App Service plan till fler instanser. Mer information om skalning finns i [skala en app i Azure App Service](./manage-scale-up.md). Varje arbets instans i en app service-plan tilldelas ett antal SNAT-portar. Om du sprider din användning över flera instanser kan du få SNAT-port användningen per instans under den rekommenderade gränsen på 100 utgående anslutningar per unik fjärrslutpunkt.
* Överväg att flytta till [App Service-miljön (ASE)](./environment/using-an-ase.md), där du tilldelas en enda utgående IP-adress och gränserna för anslutningar och SNAT-portar är mycket högre. I en ASE är antalet SNAT-portar per instans baserat på [Azure Load Balancer-tabellen för Förallokering](../load-balancer/load-balancer-outbound-connections.md#snatporttable) , så till exempel är en ASE med 1-50 Worker-instanser 1024 förallokerade portar per instans, medan en ASE med 51-100 Worker-instanser har 512 förallokerade portar per instans.

Att undvika de utgående TCP-gränserna är enklare att lösa, eftersom gränserna anges av storleken på din arbets grupp. Du kan se gränserna i [sand Box tvärs med numeriska gränser – TCP-anslutningar](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Gräns namn|Beskrivning|Liten (a1)|Medel (a2)|Stor (a3)|Isolerad nivå (ASE)|
|---|---|---|---|---|---|
|Anslutningar|Antal anslutningar över hela den virtuella datorn|1920|3968|8064|16 000|

För att undvika utgående TCP-gränser kan du antingen öka storleken på dina arbetare eller skala ut vågrätt.

## <a name="troubleshooting"></a>Felsökning

Att känna till de två typerna av utgående anslutnings gränser och vad din app gör, bör göra det enklare att felsöka. Om du vet att din app gör många anrop till samma lagrings konto kan du misstänkt en SNAT-gräns. Om din app skapar ett stort antal anrop till slut punkter över Internet, skulle du misstänker att du når den virtuella datorns gräns.

Om du inte känner till programmets beteende tillräckligt för att snabbt ta reda på orsaken finns det några verktyg och tekniker som är tillgängliga i App Service för att hjälpa till med detta.

### <a name="find-snat-port-allocation-information"></a>Hitta information om SNAT-port tilldelning

Du kan använda [App Service Diagnostics](./overview-diagnostics.md) för att hitta SNAT-port tilldelnings information och se tilldelnings måttet för SNAT-portar på en app service plats. Följ stegen nedan om du vill hitta SNAT-port tilldelnings information:

1. Om du vill komma åt App Service diagnostik navigerar du till din App Service webbapp eller App Service-miljön i [Azure Portal](https://portal.azure.com/). I det vänstra navigerings fältet väljer du **diagnostisera och lösa problem**.
2. Välj tillgänglighet och prestanda kategori
3. Välj port överbelastnings panel för SNAT i listan över tillgängliga paneler under kategorin. Övningen är att hålla den under 128.
Om du behöver det kan du fortfarande öppna ett support ärende så får support teknikern mått från backend-servern åt dig.

Observera att eftersom SNAT-porten inte är tillgänglig som ett mått, är det inte möjligt att antingen Autoskala baserat på användning av SNAT-portar eller konfigurera automatisk skalning baserat på tilldelnings måtten för SNAT-portar.

### <a name="tcp-connections-and-snat-ports"></a>TCP-anslutningar och SNAT-portar

TCP-anslutningar och SNAT-portar är inte direkt relaterade. En användnings detektor för TCP-anslutningar ingår i bladet diagnostisera och lösa problem på alla App Service-webbplatser. Sök efter frasen "TCP-anslutningar" för att hitta den.

* SNAT-portarna används bara för externa nätverks flöden, medan det totala antalet TCP-anslutningar innehåller lokala loopback-anslutningar.
* En SNAT-port kan delas av olika flöden, om flödena skiljer sig åt antingen i protokoll, IP-adress eller port. Måttet för TCP-anslutningar räknas varje TCP-anslutning.
* Gränsen för TCP-anslutningar sker på arbets instans nivån. Azure Network utgående belastnings utjämning använder inte TCP-anslutnings måttet för begränsning av SNAT-portar.
* Gränsen för TCP-anslutningar beskrivs i [sand Box Cross VM-numeriska gränser-TCP-anslutningar](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Gräns namn|Beskrivning|Liten (a1)|Medel (a2)|Stor (a3)|Isolerad nivå (ASE)|
|---|---|---|---|---|---|
|Anslutningar|Antal anslutningar över hela den virtuella datorn|1920|3968|8064|16 000|

### <a name="webjobs-and-database-connections"></a>WebJobs och databas anslutningar
 
Om SNAT-portar är uttömda, där WebJobs inte kan ansluta till SQL Database, finns det inget mått för att visa hur många anslutningar som öppnas av varje enskild webb program process. Om du vill hitta ett problematiskt webb jobb flyttar du flera WebJobs till ett annat App Service plan för att se om situationen förbättras eller om ett problem kvarstår i ett av planerna. Upprepa processen tills du hittar det problematiska webb jobbet.

### <a name="using-snat-ports-sooner"></a>Använda SNAT-portar tidigare

Du kan inte ändra några Azure-inställningar för att frigöra de använda SNAT-portarna tidigare, eftersom alla SNAT-portar släpps enligt nedanstående villkor och beteendet är avsiktligt.
 
* Om någon av servrarna eller klienten skickar FINACK, frigörs [SNAT-porten](../load-balancer/load-balancer-outbound-connections.md) efter 240 sekunder.
* Om en min visas kommer SNAT-porten att lanseras efter 15 sekunder.
* Om tids gränsen för inaktivitet har nåtts släpps porten.
 
## <a name="additional-information"></a>Ytterligare information

* [SNAT med App Service](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Felsök problem med långsam program prestanda i Azure App Service](./troubleshoot-performance-degradation.md)
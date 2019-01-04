---
title: Java Enterprise support på Linux – Azure App Service | Microsoft Docs
description: Utvecklarens guide till att distribuera Java Enterprise-appar med Wildfly med Azure App Service i Linux.
keywords: Azure apptjänst, webbapp, linux, oss, java, wildfly, enterprise
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/29/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 408141650a11a81f0c6000c6e7927af8333e2afe
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548484"
---
# <a name="java-enterprise-guide-for-app-service-on-linux"></a>Java företagens guide för App Service på Linux

Azure App Service i Linux kan Java-utvecklare för att bygga, distribuera och skala program för JEE (Java Enterprise) på en helt hanterad tjänst för Linux-baserade.  Den underliggande Java Enterprise-körningsmiljön är öppen källkod [Wildfly](https://wildfly.org/) programserver.

Den här guiden innehåller viktiga begrepp och instruktioner för Java-företagsutvecklare med i App Service för Linux. Om du aldrig har distribuerat Java-program med Azure App Service för Linux, bör du genomföra den [Java-quickstart](quickstart-java.md) första. Om App Service för Linux som inte är specifika för Java Enterprise datasäkerhetsfrågor besvaras i den [Java developer's guide](app-service-linux-java.md) och [App Service Linux vanliga frågor och svar](app-service-linux-faq.md).

## <a name="scale-with-app-service"></a>Skala med App Service 

Programservern WildFly som körs i App Service på Linux körs i fristående läge, inte i en domänkonfiguration. När du skalar ut App Service-planen konfigureras varje WildFly-instans som en fristående server.

 Skala ditt program vågrätt eller lodrätt med [regler](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started?toc=%2Fazure%2Fapp-service%2Fcontainers%2Ftoc.json) och av [ökar din instansantalet](https://docs.microsoft.com/azure/app-service/web-sites-scale?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json). 

## <a name="customize-application-server-configuration"></a>Anpassa programmet serverkonfiguration

Web App-instanserna är tillståndslösa, så att varje ny instans som är igång måste konfigureras på Start som stöder Wildfly-konfiguration som krävs av programmet.
Du kan skriva en start Bash-skript för att anropa WildFly CLI för att:

- Skapa datakällor
- Konfigurera meddelanden-providrar
- Lägga till andra moduler och beroenden i Wildfly serverkonfigurationen.

 Skriptet körs när Wildfly är igång, men innan programmet startas. Skriptet bör använda den [JBOSS CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) anropas från `/opt/jboss/wildfly/bin/jboss-cli.sh` att konfigurera application server med en konfiguration eller ändringar som behövs när servern startas. 

Använd inte interaktivt läge i CLI för att konfigurera Wildfly. I stället, du kan ange ett skript med kommandon till JBoss CLI med hjälp av den `--file` kommandot, till exempel:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Ladda upp startskriptet att `/home/site/deployments/tools` i din App Service-instans. Se [det här dokumentet](/azure/app-service/deploy-configure-credentials#userscope) anvisningar om hur du hämtar din FTP-autentiseringsuppgifter. 

Ange den **startskript** fältet i Azure-portalen till platsen för din start shell-skript, till exempel `/home/site/deployments/tools/your-startup-script.sh`.

Ange [programinställningar](/azure/app-service/web-sites-configure#application-settings) i programkonfigurationen att skicka miljövariabler för användning i skriptet. Programinställningar Behåll anslutningssträngar och andra hemligheter som behövs för att konfigurera ditt program utanför versionskontroll.

## <a name="modules-and-dependencies"></a>Moduler och beroenden

För att installera moduler och deras beroenden i klassökvägen Wildfly via JBoss CLI, behöver du skapa följande filer i sina egna katalogen.  Vissa moduler och beroenden kanske behöver ytterligare konfiguration, till exempel JNDI naming eller andra API-konfiguration, så att den här listan är en minimiuppsättning för vad du behöver konfigurera ett beroende i de flesta fall.

- En [XML-modulen descriptor](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Den här XML-filen definierar namn, attribut och beroenden modulens. Detta [module.xml-exempelfil](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) definierar en Postgres-modul, beroendet JDBC JAR-filen och andra modulberoenden som krävs.
- Alla nödvändiga JAR-filen beroenden för din modul.
- Ett skript med din JBoss CLI-kommandon för att konfigurera den nya modulen. Den här filen innehåller kommandon som ska köras av JBoss CLI att konfigurera servern för att använda beroendet. Dokumentation om kommandon för att lägga till moduler, datakällor och leverantörer av meddelanden som avser [det här dokumentet](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
-  Ett Bash-startskript att anropa JBoss CLI och köra skriptet i föregående steg. Den här filen ska utföras när din App Service-instans startas eller när nya instanser som levereras under en skalbar.  Den här startskript är där du kan utföra alla andra inställningar för ditt program som JBoss kommandon skickas till JBoss CLI. Den här filen kan vara ett enda kommando för att skicka ditt kommando JBoss CLI-skript till JBoss CLI minimum: 
   
```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli` 
``` 

När du har de filer och innehåll för din modul, följer du stegen nedan för att lägga till modulen på programservern Wildfly. 

1. FTP-filer till `/home/site/deployments/tools` i din App Service-instans. Se det här dokumentet för instruktioner om hur du hämtar din FTP-autentiseringsuppgifter. 
2. På bladet programinställningar i Azure-portalen ange fältet ”startskript” till platsen för din startskript shell, till exempel `/home/site/deployments/tools/your-startup-script.sh` .
3. Starta om din App Service-instans genom att trycka på den **starta om** knappen i den **översikt** avsnittet i portalen eller med hjälp av Azure CLI.

## <a name="data-sources"></a>Datakällor

Följ samma process som beskrivs ovan i avsnittet Installera moduler och beroenden för att konfigurera Wildfly för anslutning till en datakälla. Du kan följa samma steg för alla Azure-databastjänsten.

1. Ladda ned JDBC-drivrutinen för din databas smak. För enkelhetens skull följer drivrutinerna för [Postgres](https://jdbc.postgresql.org/download.html) och [MySQL](https://dev.mysql.com/downloads/connector/j/). Packa upp hämtningen för att hämta .jar-filen.
2. Följ stegen disposition i ”moduler och beroenden” kan skapa och överföra din XML-modulen descriptor JBoss CLI-skript, startskript och JDBC .jar-beroendet.


Mer information om hur du konfigurerar Wildfly med [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7) , [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource), och [SQL Database](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898) är tillgänglig. Du kan använda dessa anpassade anvisningar tillsammans med den generaliserade metoden ovan för att lägga till definitioner av datakällor i din server.

## <a name="messaging-providers"></a>Leverantörer av meddelanden

För att aktivera meddelandefunktioner Beans använder Service Bus som mekanism för meddelanden:

1. Använd den [Apache QPId JMS meddelandebiblioteket](https://qpid.apache.org/proton/index.html). Med det här beroendet i din pom.xml (eller andra-filen) för programmet.

2.  Skapa [Service Bus-resurser](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Skapa en Azure Service Bus-namnområde och kö i namnområdet och en princip för delad åtkomst med skicka och ta emot funktioner.

3. Ange den delade åtkomstnyckeln för principen för koden antingen med URL-kodning den primära nyckeln i din princip eller [Använd Service Bus SDK](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Följ stegen som beskrivs i avsnittet Installera moduler och beroenden med modulen XML descriptor, .jar beroenden, JBoss CLI-kommandon och startskript för JMS-providern. Förutom de fyra filerna måste du också skapa en XML-fil som definierar JNDI namnet för den JMS queue och topic. Se [den här lagringsplatsen](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig) för referens konfigurationsfiler.


## <a name="configure-session-management-caching"></a>Konfigurera sessionen management cachelagring

Som standard App Service på Linux ska använda sessionscookies tillhörighet att se till att klientbegäranden med befintliga sessioner dirigeras samma instans av ditt program. Den här standardfunktionen kräver ingen konfiguration, men har vissa begränsningar:

- Om en programinstans startas om och skalas, går användaren sessionstillstånd i programservern förlorad.
- Om program har lång timeout sessionsinställningar eller ett fast antal användare, kan det ta lite tid för autoscaled nya instanser kan ta emot belastning eftersom endast nya sessioner kommer att dirigeras till de nyligen startats instanserna.

Du kan konfigurera Wildfly om du vill använda en extern session butik som [Azure Cache för Redis](/azure/azure-cache-for-redis/). Behöver du [inaktivera den befintliga instansen ARR-tillhörighet](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) konfiguration för att stänga av sessionen cookie-baserad Routning och tillåta arkivet session konfigurerade Wildfly att fungera utan störningar.

## <a name="enable-web-sockets"></a>Aktivera webbsockets

Som standard aktiveras webbsockets i App Service. Kom igång med WebSockets i ditt program genom att referera till [snabbstarten](https://github.com/wildfly/quickstart/tree/master/websocket-hello).

## <a name="logs-and-troubleshooting"></a>Loggar och felsökning

App Service innehåller verktyg som hjälper dig att felsöka problem med ditt program.

-   Aktivera loggning genom att klicka på **diagnostikloggar** i det vänstra navigeringsfönstret. Klicka på **filsystem** att din kvot och kvarhållning period för lagring och spara dina ändringar. Du hittar de här loggarna under `/home/LogFiles/`.
-   [Använda SSH för att ansluta till programinstansen](app-service-linux-ssh-support.md) att visa loggar för att köra program.
-   Kontrollera diagnostikloggar den **diagnostikloggar** panelen i portalen eller med hjälp av Azure CLI-kommando: ` az webapp log tail --name <your-app-name> --resource-group <your-apps-resource-group> `

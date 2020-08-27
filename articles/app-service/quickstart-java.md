---
title: 'Snabb start: skapa en Java-app på Azure App Service'
description: Distribuera din första Java-Hello World till Azure App Service på några minuter. Med plugin-programmet för Azure-Webbappar för maven kan du enkelt distribuera Java-appar.
keywords: Azure, App Service, Web App, Windows, Linux, Java, maven, snabb start
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: a3067972dc42db6644006e33797fc44c2f494693
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961252"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Snabb start: skapa en Java-app på Azure App Service

[Azure App Service](overview.md) ger en mycket skalbar och automatisk korrigering av webb värd tjänst.  Den här snabb starten visar hur du använder [Azure CLI](/cli/azure/get-started-with-azure-cli) med [plugin-programmet för Azure-Webbappar för maven för](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) att distribuera en War-fil (Java Web Archive).

> [!NOTE]
> I den här artikeln arbetar vi endast med Java-appar som paketerats i WAR-filer. Plugin-programmet stöder också JAR-webbprogram. Läs informationen om att [distribuera en Java SE JAR-fil till App Service på Linux](/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) om du vill testa det.

> [!NOTE]
> Du kan också göra samma användning av populära IDE: er som IntelliJ och Sol förmörkelse. Ta en titt på våra liknande dokument i [Azure Toolkit for IntelliJ snabb start](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) eller [Azure Toolkit for Eclipse snabb start](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).
>
![Exempel app som körs i Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Skapa en Java-app

Kör följande Maven-kommando i Cloud Shell-prompten för att skapa en ny app med namnet `helloworld`:

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Ändra sedan arbets katalogen till projektmappen:

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>Konfigurera Maven-plugin-programmet

Distributions processen till Azure App Service kan hämta dina Azure-autentiseringsuppgifter från Azure CLI automatiskt. Maven-plugin-programmet kommer att logga in dig med OAuth-eller enhets inloggning om Azure CLI inte är lokalt installerat. Kontrol lera informationen om [autentisering med maven-plugin](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) -program om du behöver.

Du kan köra kommandot maven nedan för att konfigurera distributionen
```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

::: zone pivot="platform-windows" 
Du uppmanas att välja 
* **OS (standard: `linux` )**
* **Java-version (standard: `1.8` )**
* **Webb behållare (standard: `tomcat 8.5` )** 
 
Var noga med att ange **`2`** för att välja **Windows** -operativsystem i det första steget. Övriga konfigurationer kan lämnas kvar som standard genom att trycka på **RETUR**. Klicka slutligen **`Y`** på **Bekräfta (j/N)** för att slutföra konfigurationen.

En exempel process ser ut så här:

```console
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use: 2
Define value for javaVersion(Default: 1.8): 
1. 1.7
2. 1.7.0_191_ZULU
3. 1.7.0_51
4. 1.7.0_71
5. 1.7.0_80
6. 1.8 [*]
7. 1.8.0_102
8. 1.8.0_111
9. 1.8.0_144
10. 1.8.0_172
11. 1.8.0_172_ZULU
12. 1.8.0_181
13. 1.8.0_181_ZULU
14. 1.8.0_202
15. 1.8.0_202_ZULU
16. 1.8.0_25
17. 1.8.0_60
18. 1.8.0_73
19. 1.8.0_92
20. 11
21. 11.0.2_ZULU
Enter index to use:
Define value for webContainer(Default: tomcat 8.5): 
1. jetty 9.1
2. jetty 9.1.0.20131115
3. jetty 9.3
4. jetty 9.3.13.20161014
5. tomcat 7.0
6. tomcat 7.0.50
7. tomcat 7.0.62
8. tomcat 8.0
9. tomcat 8.0.23
10. tomcat 8.5 [*]
11. tomcat 8.5.20
12. tomcat 8.5.31
13. tomcat 8.5.34
14. tomcat 8.5.37
15. tomcat 8.5.6
16. tomcat 9.0
17. tomcat 9.0.0
18. tomcat 9.0.12
19. tomcat 9.0.14
20. tomcat 9.0.8
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1590394316693
ResourceGroup : helloworld-1590394316693-rg
Region : westeurope
PricingTier : PremiumV2_P1v2
OS : Windows
Java : 1.8
WebContainer : tomcat 8.5
Deploy to slot : false
Confirm (Y/N)? :
[INFO] Saving configuration to pom.
```
::: zone-end
::: zone pivot="platform-linux"  

Du uppmanas att välja 
* **OS (standard: `linux` )**
* **Java-version (standard: `Java 8` )**
* **Webb behållare (standard: `Tomcat 8.5` )** 

Alla konfigurationer kan lämnas kvar som standard genom att trycka på **RETUR**. Klicka slutligen **`Y`** på **Bekräfta (j/N)** för att slutföra konfigurationen.
En exempel process ser ut så här:

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. Java 11
2. Java 8 [*]
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. TOMCAT 8.5 [*]
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```
::: zone-end

Du kan ändra konfigurationerna för App Service direkt om det `pom.xml` behövs, några vanliga i listan nedan:

 Egenskap | Krävs | Beskrivning | Version
---|---|---|---
`<schemaVersion>` | falskt | Ange konfigurations schemats version. De värden som stöds är: `v1` , `v2` . | 1.5.2
`<resourceGroup>` | true | Azure-resurs grupp för din webbapp. | 0.1.0 +
`<appName>` | true | Namnet på din webbapp. | 0.1.0 +
`<region>` | true | Anger den region där din webbapp ska vara värd. Standardvärdet är **westeurope**. Avsnittet alla giltiga regioner i [regioner som stöds](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) . | 0.1.0 +
`<pricingTier>` | falskt | Pris nivån för din webbapp. Standardvärdet är **P1V2**.| 0.1.0 +
`<runtime>` | true | Konfiguration av körnings miljön kan du se informationen [här](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0 +
`<deployment>` | true | Distributions konfigurationen kan du se informationen [här](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0 +

Var försiktig med värdena i `<appName>` och `<resourceGroup>` ( `helloworld-1590394316693` och `helloworld-1590394316693-rg` därmed i demonstrationen). de kommer att användas senare.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>Distribuera appen

Distributions processen för Azure App Service använder konto uppgifter från Azure CLI. [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) innan du fortsätter.

```azurecli
az login
```
Sedan kan du distribuera din Java-app till Azure med hjälp av följande kommando:

```bash
mvn package azure-webapp:deploy
```

När distributionen är klar är ditt program klart på `http://<appName>.azurewebsites.net/` ( `http://helloworld-1590394316693.azurewebsites.net` i demonstrationen). Öppna webb adressen med din lokala webbläsare, du bör se

![Exempel app som körs i Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Grattis!** Du har distribuerat din första java-app till App Service.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Rensa resurser

I de föregående stegen skapade du Azure-resurser i en resursgrupp. Om du inte tror att du behöver dessa resurser i framtiden tar du bort resurs gruppen från portalen eller genom att köra följande kommando i Cloud Shell:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Det kan några minuter att köra kommandot.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Ansluta till Azure SQL Database med Java](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)

> [!div class="nextstepaction"]
> [Ansluta till Azure DB för MySQL med Java](../mysql/connect-java.md)

> [!div class="nextstepaction"]
> [Ansluta till Azure DB för PostgreSQL med Java](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [Resurser för Azure för Java-utvecklare](/java/azure/)

> [!div class="nextstepaction"]
> [Konfigurera java-app](configure-language-java.md)

> [!div class="nextstepaction"]
> [CI/CD med Jenkins](/azure/developer/jenkins/deploy-to-azure-app-service-using-plugin)

> [!div class="nextstepaction"]
> [Mappa anpassad domän](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Lär dig mer om maven-plugin-program för Azure](https://github.com/microsoft/azure-maven-plugins)
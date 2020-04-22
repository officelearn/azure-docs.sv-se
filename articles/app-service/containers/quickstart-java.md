---
title: 'Snabbstart: Skapa en Linux Java-app'
description: Kom igång med Linux-appar på Azure App Service genom att distribuera din första Java-app till en Linux-behållare i App Service.
keywords: azure, apptjänst, webbapp, linux, java, maven, snabbstart
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 8f2e99ffc9f9ee5c5553e8d933d82f83999c8ab2
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732889"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>Snabbstart: Skapa en Java-app på Azure App Service på Linux

[App Service på Linux](app-service-linux-intro.md) ger en mycket skalbar, självkorrigering, webbhotell med hjälp av Operativsystemet Linux. Den här snabbstarten visar hur du använder [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) med Azure Web App Plugin [för Maven för](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) att distribuera en Java-webbarkivfil (WAR) på Operativsystemet Linux.

> [!NOTE]
>
> Samma sak kan också göras med hjälp av populära IDEs som IntelliJ, Eclipse och VS Code. Kolla in våra liknande dokument på [Azure Toolkit for IntelliJ Quickstart,](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) [Azure Toolkit for Eclipse Quickstart](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app) eller [VS Code Quickstart](https://code.visualstudio.com/docs/java/java-webapp).
>
![Exempel på app som körs i Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Skapa en Java-app

Kör följande Maven-kommando i Cloud Shell-prompten för att skapa en ny app med namnet `helloworld`:

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp"
```
Ändra sedan arbetskatalogen till projektmappen:

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>Konfigurera Maven-plugin-programmet

Distributionsprocessen till Azure App Service använder kontobehörighet från Azure CLI. [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) innan du fortsätter.

```azurecli
az login
```

Sedan kan du konfigurera distributionen, köra maven kommandot i Kommandotolken och använda standardkonfigurationer genom att trycka **på RETUR** tills du får **bekräfta (Y / N)** fråga, tryck sedan **på "y"** och konfigurationen är klar. 
```cmd
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```
En exempelprocess ser ut som:

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

> [!NOTE]
> I den här artikeln arbetar vi endast med Java-appar som paketerats i WAR-filer. Plugin-programmet stöder också JAR-webbprogram. Läs informationen om att [distribuera en Java SE JAR-fil till App Service på Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) om du vill testa det.

Navigera `pom.xml` till igen för att se plugin konfigurationen uppdateras, Du kan ändra andra konfigurationer för App Service direkt i din POM-fil om det behövs, några vanliga som anges nedan:

 Egenskap | Krävs | Beskrivning | Version
---|---|---|---
`<schemaVersion>` | false | Ange versionen av konfigurationsschemat. Värden som `v1`stöds `v2`är: , . | 1.5.2
`<resourceGroup>` | true | Azure Resource Group för din webbapp. | 0.1.0+
`<appName>` | true | Namnet på din webbapp. | 0.1.0+
`<region>` | true | Anger den region där webbappen ska vara värd. standardvärdet är **westeurope**. Alla giltiga regioner på [avsnittet Regioner som stöds.](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) | 0.1.0+
`<pricingTier>` | false | Prisnivån för din webbapp. Standardvärdet är **P1V2**.| 0.1.0+
`<runtime>` | true | Körtidsmiljökonfigurationen kan du se detaljerna [här](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+
`<deployment>` | true | Distributionskonfigurationen kan du se informationen [här](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=config)

## <a name="deploy-the-app"></a>Distribuera appen

Distribuera din Java-app till Azure med följande kommando:

```bash
mvn package azure-webapp:deploy
```

När distributionen är klar bläddrar du till den distribuerade tillämpningen med hjälp av följande webbadress i webbläsaren, till exempel `http://<webapp>.azurewebsites.net`. 

![Exempel på app som körs i Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Grattis!** Du har distribuerat din första Java-app till App Service på Linux.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Rensa resurser

I de föregående stegen skapade du Azure-resurser i en resursgrupp. Om du inte förväntar dig att behöva dessa resurser i framtiden tar du bort resursgruppen från portalen eller genom att köra följande kommando i Cloud Shell:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Det kan några minuter att köra kommandot.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansluta till Azure SQL-databas med Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Ansluta till Azure DB för MySQL med Java](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Ansluta till Azure DB för PostgreSQL med Java](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Konfigurera Java-appen](configure-custom-container.md)

> [!div class="nextstepaction"]
> [CI/CD med Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Andra Azure för Java-utvecklare Resurser](/java/azure/)

> [!div class="nextstepaction"]
> [Läs mer om Maven-plugins för Azure](https://github.com/microsoft/azure-maven-plugins)

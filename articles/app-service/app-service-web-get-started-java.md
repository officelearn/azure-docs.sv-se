---
title: 'Snabb start: skapa en Java-app i Windows'
description: Distribuera din första Java-Hello World till Azure App Service i Windows på några minuter. Med plugin-programmet för Azure-Webbappar för maven kan du enkelt distribuera Java-appar.
keywords: Azure, App Service, Web App, Windows, Java, maven, snabb start
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 6681b2688c7e8884a197ebe27fb784b1a195f4b5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81732167"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>Snabb start: skapa en Java-app på Azure App Service i Windows

> [!NOTE]
> I den här artikeln distribueras en app till App Service i Windows. Information om hur du distribuerar till App Service på _Linux_finns i [skapa Java-webbapp på Linux](./containers/quickstart-java.md).
>

[Azure App Service](overview.md) ger en mycket skalbar och automatisk korrigering av webb värd tjänst.  Den här snabb starten visar hur du använder [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) med [plugin-programmet för Azure-Webbappar för maven för](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) att distribuera en War-fil (Java Web Archive).

> [!NOTE]
> Samma sak kan också göras med populära IDE: er som IntelliJ och Sol förmörkelse. Ta en titt på våra liknande dokument i [Azure Toolkit for IntelliJ snabb start](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) eller [Azure Toolkit for Eclipse snabb start](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Exempel app som körs i Azure App Service](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Skapa en Java-app

Kör följande Maven-kommando i Cloud Shell-prompten för att skapa en ny app med namnet `helloworld`:

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>Konfigurera Maven-plugin-programmet

Distribuera från Maven genom att använda kodredigeraren i Cloud Shell för att öppna projektets `pom.xml`-fil i katalogen `helloworld`. 

```bash
code pom.xml
```

Lägg sedan till följande plugin-definition i `<build>`-elementet i filen `pom.xml`.

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Windows         -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.9.0</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>SUBSCRIPTION_ID</subscriptionId>
            <resourceGroup>RESOURCEGROUP_NAME</resourceGroup>
            <appName>WEBAPP_NAME</appName>
            <region>REGION</region>
            <!-- Java Runtime Stack for App Service on Windows-->
            <runtime>
                <os>windows</os>
                <javaVersion>1.8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>
            <deployment>
                <resources>
                    <resource>
                        <directory>${project.basedir}/target</directory>
                        <includes>
                            <include>*.war</include>
                        </includes>
                    </resource>
                </resources>
            </deployment>
        </configuration>
    </plugin>
</plugins>
```

> [!NOTE]
> I den här artikeln arbetar vi endast med Java-appar som paketerats i WAR-filer. Plugin-programmet stöder också JAR-webbprogram. Läs informationen om att [distribuera en Java SE JAR-fil till App Service på Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) om du vill testa det.


Uppdatera följande platshållare i konfigurationen av plugin-program:

| Platshållare | Beskrivning |
| ----------- | ----------- |
| `SUBSCRIPTION_ID` | Unikt ID för den prenumeration som du vill distribuera appen till. Standard prenumerationens ID kan hittas från Cloud Shell eller CLI med hjälp av `az account show` kommandot. För alla tillgängliga prenumerationer använder du `az account list` kommandot.|
| `RESOURCEGROUP_NAME` | Namnet på den nya resursgrupp där du vill skapa din app. Genom att lägga alla resurser för en app i en grupp, kan du hantera dem tillsammans. Genom att till exempel ta bort resursgruppen skulle du ta bort alla resurser som är associerade med appen. Uppdatera det här värdet med ett unikt nytt resurs grupp namn, till exempel *myResourceGroup*. Du använder den här resursgruppens namn för att rensa alla Azure-resurser i ett senare avsnitt. |
| `WEBAPP_NAME` | Appens namn kommer att ingå i värd namnet för appen när den distribueras till Azure (WEBAPP_NAME. azurewebsites. net). Uppdatera det här värdet med ett unikt namn för den nya App Service-appen, som blir värd för din Java-app, till exempel *contoso*. |
| `REGION` | En Azure-region där appen finns, till exempel *westus2*. Du kan hämta en lista över regioner från Cloud Shell eller CLI med kommandot `az account list-locations`. |

## <a name="deploy-the-app"></a>Distribuera appen

Distribuera din Java-app till Azure med följande kommando:

```bash
mvn package azure-webapp:deploy
```

När distributionen är klar bläddrar du till den distribuerade tillämpningen med hjälp av följande webbadress i webbläsaren, till exempel `http://<webapp>.azurewebsites.net/`.

![Exempel app som körs i Azure App Service](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**Grattis!** Du har distribuerat din första java-app till App Service i Windows.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Ansluta till Azure SQL Database med Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Ansluta till Azure DB för MySQL med Java](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Ansluta till Azure DB för PostgreSQL med Java](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Resurser för Azure för Java-utvecklare](/java/azure/)

> [!div class="nextstepaction"]
> [Mappa anpassad domän](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Lär dig mer om maven-plugin-program för Azure](https://github.com/microsoft/azure-maven-plugins)

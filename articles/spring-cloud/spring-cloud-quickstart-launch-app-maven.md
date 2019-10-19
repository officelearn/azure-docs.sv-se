---
title: 'Snabb start: starta ett program med hjälp av maven – Azure våren Cloud'
description: Starta ett exempel program med hjälp av maven
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: v-vasuke
ms.openlocfilehash: e773b997cca3fa9a1f11fec2ac449e1fc11c5364
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554572"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-by-using-the-maven-plug-in"></a>Snabb start: starta en Azure våren Cloud-App med hjälp av maven-plugin-programmet

Med Azure våren Cloud maven-plugin-programmet kan du enkelt skapa och uppdatera Azure våren Cloud Service-programmen. Genom att fördefiniera en konfiguration kan du distribuera program till din befintliga Azure våren Cloud-tjänst. I den här artikeln använder du ett exempel program som heter PiggyMetrics för att demonstrera den här funktionen.

>[!Note]
> Innan du påbörjar den här snabb starten ska du se till att din Azure-prenumeration har åtkomst till Azure våren Cloud. Som för hands versions tjänst ber vi dig att kontakta oss så att vi kan lägga till din prenumeration i vår lista över tillåtna. Om du vill utforska funktionerna i Azure våren Cloud, fyller du i och skickar [Azure våren Cloud (privat för hands version) – intresse form](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u).

>[!TIP]
> Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt som du kan använda för att köra kommandona i den här artikeln. Den har ett förinstallerat vanligt Azure-verktyg, inklusive de senaste versionerna av Git, Java Development Kit (JDK), Maven och Azure CLI. Om du är inloggad på din Azure-prenumeration startar du [Azure Cloud Shell](https://shell.azure.com). Mer information finns i [Översikt över Azure Cloud Shell](../cloud-shell/overview.md).

För att slutföra den här snabbstarten behöver du:

1. [Installera Git](https://git-scm.com/).
2. [Installera JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Installera Maven 3,0 eller senare](https://maven.apache.org/download.cgi).
4. [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
5. [Registrera dig för en kostnads fri Azure-prenumeration](https://azure.microsoft.com/free/).

## <a name="install-the-azure-cli-extension"></a>Installera Azure CLI-tillägget

Installera Azure våren Cloud-tillägget för Azure CLI med hjälp av följande kommando:

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Etablera en tjänst instans på Azure Portal

1. Öppna den [Azure Portal](https://portal.azure.com)i en webbläsare och logga in på ditt konto.

1. Sök efter och välj sedan **Azure våren Cloud**. 
1. På sidan Översikt väljer du **skapa**och gör sedan följande:  

    a. I rutan **tjänst namn** anger du namnet på din tjänst instans. Namnet måste vara mellan 4 och 32 tecken långt och får bara innehålla gemena bokstäver, siffror och bindestreck. Det första tecknet i tjänst namnet måste vara en bokstav och det sista tecknet måste vara en bokstav eller en siffra.  

    b. I list rutan **prenumeration** väljer du den prenumeration som du vill fakturera för resursen. Se till att den här prenumerationen har lagts till i listan över tillåtna för Azure våren-molnet.  

    c. I rutan **resurs grupp** skapar du en ny resurs grupp. Det är en bra idé att skapa resurs grupper för nya resurser.  

    d. I list rutan **plats** väljer du platsen för din tjänst instans. De platser som stöds för närvarande är USA, västra USA 2, Västeuropa och Sydostasien.
    
Det tar ungefär 5 minuter för tjänsten att distribueras. När tjänsten har distribuerats visas **översikts** sidan för tjänst instansen.

## <a name="set-up-your-configuration-server"></a>Konfigurera konfigurations servern

1. På sidan tjänst **Översikt** väljer du **konfigurations Server**.
1. I avsnittet **standard databas** ställer du in **URI** till **https://github.com/Azure-Samples/piggymetrics** , anger **etikett** till **config**och väljer sedan **tillämpa** för att spara ändringarna.

## <a name="clone-and-build-the-sample-application-repository"></a>Klona och bygga exempel program databasen

1. Klona git-lagringsplatsen genom att köra följande kommando:

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Ändra katalog och bygg projektet genom att köra följande kommando:

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Skapa och Distribuera Azure våren Cloud-konfigurationen

1. Om du vill göra det möjligt för maven att arbeta med Azure våren Cloud lägger du till följande kod i filen *Pom. XML* eller *Settings. XML* .

    ```xml
    <pluginRepositories>
      <pluginRepository>
        <id>maven.snapshots</id>
        <name>Maven Central Snapshot Repository</name>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
        <releases>
          <enabled>false</enabled>
        </releases>
        <snapshots>
          <enabled>true</enabled>
        </snapshots>
      </pluginRepository>
    </pluginRepositories>
    ```

1. Generera en konfiguration genom att köra följande kommando:

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    a. Välj modulerna `gateway`, `auth-service` och `account-service`.

    b. Välj din prenumeration och Azure våren Cloud Service-kluster.

    c. I listan med tillhandahållna projekt anger du det nummer som motsvarar `gateway` för att ge IT-offentlig åtkomst.
    
    d. Bekräfta konfigurationen.

1. Distribuera apparna med hjälp av följande kommando:

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. Du kan komma åt PiggyMetrics genom att använda URL: en som finns i utdata från föregående kommando.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat ett fjäder moln program från en maven-lagringsplats. Om du vill veta mer om Azure våren Cloud fortsätter du till självstudien om hur du förbereder din app för distribution.

> [!div class="nextstepaction"]
> [Förbered ditt Azure våren Cloud-program för distribution](spring-cloud-tutorial-prepare-app-deployment.md)

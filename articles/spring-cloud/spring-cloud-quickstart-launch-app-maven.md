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
ms.openlocfilehash: 01140e94e8d0cc47570824970801bdd0043324d7
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166516"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Snabb start: starta en Azure våren Cloud-App med maven-plugin-programmet

Med Azure våren Clouds maven-plugin-program kan du enkelt skapa och uppdatera Azure våren Cloud Service-programmen. Genom att i förväg definiera en konfiguration kan du distribuera program till din befintliga Azure våren Cloud-tjänst. I den här artikeln använder vi ett exempel program som heter PiggyMetrics för att demonstrera den här funktionen.

>[!Note]
> Innan du påbörjar den här snabb starten ska du se till att din Azure-prenumeration har åtkomst till Azure våren Cloud.  Som förhands gransknings tjänst ber vi kunderna att kontakta oss så att vi kan lägga till din prenumeration i vår lista över tillåtna.  Om du vill utforska funktionerna i Azure våren Cloud kan du [fylla i det här formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u
).

>[!TIP]
> Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln.  Den har ett förinstallerat vanligt Azure-verktyg, inklusive de senaste versionerna av Git, JDK, Maven och Azure CLI. Om du är inloggad på din Azure-prenumeration startar du [Azure Cloud Shell](https://shell.azure.com) från Shell.Azure.com.  Du kan lära dig mer om Azure Cloud Shell genom att [läsa vår dokumentation](../cloud-shell/overview.md)

För att slutföra den här snabbstarten behöver du:

1. [Installera Git](https://git-scm.com/)
2. [Installera JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Installera maven 3,0 eller senare](https://maven.apache.org/download.cgi)
4. [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Installera Azure CLI-tillägget

Installera Azure våren Cloud-tillägget för Azure CLI med hjälp av följande kommando

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Etablera en tjänst instans på Azure Portal

1. Öppna den [Azure Portal](https://portal.azure.com)i en webbläsare och logga in på ditt konto.

1. Sök efter **Azure våren-molnet** och välj att gå till översikts sidan. Välj knappen **skapa** för att komma igång.

1. Fyll i formuläret, med beaktande av följande rikt linjer:
    - Tjänst namn: Ange namnet på din tjänst instans.  Namnet måste vara mellan 4 och 32 tecken långt och får bara innehålla gemena bokstäver, siffror och bindestreck.  Det första tecknet i tjänst namnet måste vara en bokstav och det sista tecknet måste vara en bokstav eller en siffra.
    - Prenumeration: Välj den prenumeration som du vill fakturera för den här resursen.  Se till att den här prenumerationen har lagts till i vår lista över tillåtna för Azure våren-molnet.
    - Resurs grupp: skapa nya resurs grupper för nya resurser är en bra metod.
    - Plats: Välj platsen för din tjänst instans. De platser som stöds för närvarande är USA, västra USA 2, Västeuropa och Sydostasien.
    
Det tar ungefär 5 minuter för tjänsten att distribueras.  När den har distribuerats visas **översikts** sidan för tjänst instansen.

## <a name="set-up-your-configuration-server"></a>Konfigurera konfigurations servern

1. Gå till sidan tjänst **Översikt** och välj **konfigurations Server**.
1. I avsnittet **standard databas** ställer du in **URI** till "https://github.com/Azure-Samples/piggymetrics", anger **etikett** till "config" och väljer **tillämpa** för att spara ändringarna.

## <a name="clone-and-build-the-sample-application-repository"></a>Klona och bygga exempel program databasen

1. Klona git-lagringsplatsen genom att köra följande kommando.

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Ändra katalog och bygg projektet genom att köra följande kommando.

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Skapa och Distribuera Azure våren Cloud-konfigurationen

1. Lägg till följande i din `pom.xml` eller `settings.xml` om du vill att maven ska fungera med Azure våren Cloud.

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

1. Generera en konfiguration genom att köra följande kommando.

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    1. Välj modulerna `gateway`, `auth-service` och `account-service`.

    1. Välj din prenumeration och Azure våren Cloud Service-kluster.

    1. I listan med projekt anger du det nummer som motsvarar `gateway` för att ge IT-offentlig åtkomst.
    
    1. Bekräfta konfigurationen.

1. Distribuera apparna med hjälp av följande kommando:

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. Du kan komma åt PiggyMetrics med hjälp av URL: en som finns i utdata från föregående kommando.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat ett fjäder moln program från en maven-lagringsplats.  Om du vill veta mer om Azure våren Cloud fortsätter du till självstudien om att förbereda din app för distribution.

> [!div class="nextstepaction"]
> [Förbered ditt Azure våren Cloud-program för distribution](spring-cloud-tutorial-prepare-app-deployment.md)

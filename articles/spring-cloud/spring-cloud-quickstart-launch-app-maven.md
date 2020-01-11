---
title: Snabb start – starta ett program med hjälp av maven med Azure våren Cloud
description: Starta ett exempel program med hjälp av maven i den här snabb starten
author: jpconnock
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 8aaf5f5fbcabf3476284f0d5e9f4f50ef3e92bd6
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75891639"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Snabb start: starta en Azure våren Cloud-App med maven-plugin-programmet

Med hjälp av Azure våren Cloud maven-plugin-programmet kan du enkelt skapa och uppdatera dina Azure våren Cloud-program. Genom att fördefiniera en konfiguration kan du distribuera program till din befintliga Azure våren Cloud-tjänst. I den här artikeln använder du ett exempel program som heter PiggyMetrics för att demonstrera den här funktionen.

Efter den här snabb starten får du lära dig att:

> [!div class="checklist"]
> * Etablera en tjänst instans
> * Konfigurera en konfigurations Server för en instans
> * Klona och bygg program för mikrotjänster lokalt
> * Distribuera varje mikrotjänst
> * Tilldela en offentlig slut punkt för ditt program

>[!Note]
> Azure våren Cloud erbjuds för närvarande som en offentlig för hands version. Med den offentliga för hands versionen kan kunder experimentera med nya funktioner före den officiella versionen.  Funktioner och tjänster för offentliga för hands versioner är inte avsedda för användning i produktion.  Om du vill ha mer information om support under för hands versionerna kan du läsa [vanliga frågor och svar](https://azure.microsoft.com/support/faq/) eller arkiv en [supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) .


>[!TIP]
> Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt som du kan använda för att köra kommandona i den här artikeln. Den har ett förinstallerat vanligt Azure-verktyg, inklusive de senaste versionerna av Git, Java Development Kit (JDK), Maven och Azure CLI. Om du är inloggad på din Azure-prenumeration startar du [Azure Cloud Shell](https://shell.azure.com). Mer information finns i [Översikt över Azure Cloud Shell](../cloud-shell/overview.md).

För att slutföra den här snabbstarten behöver du:

1. [Installera Git](https://git-scm.com/).
2. [Installera JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Installera Maven 3,0 eller senare](https://maven.apache.org/download.cgi).
4. [Registrera dig för en kostnads fri Azure-prenumeration](https://azure.microsoft.com/free/).

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Etablera en tjänst instans på Azure Portal

1. Öppna [den här länken till Azure våren Cloud i den Azure Portal](https://ms.portal.azure.com/#create/Microsoft.AppPlatform)i en webbläsare och logga in på ditt konto.

1. Ange **projekt informationen** för exempel programmet på följande sätt:

    1. Välj den **prenumeration** som programmet ska associeras med.
    1. Välj eller skapa en resurs grupp för programmet. Vi rekommenderar att du skapar en ny resurs grupp.  I exemplet nedan visas en ny resurs grupp med namnet `myspringservice`.
    1. Ange ett namn för den nya moln tjänsten Azure våren.  Namnet måste vara mellan 4 och 32 tecken långt och får bara innehålla gemena bokstäver, siffror och bindestreck. Det första tecknet i tjänst namnet måste vara en bokstav och det sista tecknet måste vara en bokstav eller en siffra.  Tjänsten i exemplet nedan har namnet `contosospringcloud`.
    1. Välj en plats för programmet från de tillhandahållna alternativen.  I det här exemplet väljer vi `East US`.
    1. Välj **Granska + skapa** för att granska en sammanfattning av den nya tjänsten.  Om allting ser korrekt ut väljer du **skapa**.

    > [!div class="mx-imgBorder"]
    > ![Välj granska + skapa](media/maven-qs-review-create.jpg)

Det tar ungefär 5 minuter för tjänsten att distribueras. När tjänsten har distribuerats väljer **du gå till resurs** så visas sidan **Översikt** för tjänst instansen.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=provision)

## <a name="set-up-your-configuration-server"></a>Konfigurera konfigurations servern

1. På sidan tjänst **Översikt** väljer du **konfigurations Server**.
1. I avsnittet **standard databas** ställer du in **URI** till **https://github.com/Azure-Samples/piggymetrics** , anger **etikett** till **config**och väljer sedan **tillämpa** för att spara ändringarna.

    > [!div class="mx-imgBorder"]
    > ![definiera och tillämpa konfigurations inställningar](media/maven-qs-apply-config.jpg)

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=config-server)

## <a name="clone-and-build-the-sample-application-repository"></a>Klona och bygga exempel program databasen

1. Starta [Azure Cloud Shell](https://shell.azure.com).

1. Klona git-lagringsplatsen genom att köra följande kommando:

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Ändra katalog och bygg projektet genom att köra följande kommando:

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Generera konfigurationer och distribuera till Azure våren-molnet

1. Generera konfigurationer genom att köra följande kommando i rotmappen för PiggyMetrics som innehåller den överordnade POM:

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.0.0:config
    ```

    a. Välj modulerna `gateway`,`auth-service`och `account-service`.

    b. Välj din prenumeration och Azure våren Cloud Service-kluster.

    c. I listan med tillhandahållna projekt anger du det nummer som motsvarar `gateway` för att ge IT-offentlig åtkomst.
    
    d. Bekräfta konfigurationen.

1. POM innehåller nu plugin-beroenden och konfigurationer. Distribuera apparna med följande kommando:

   ```azurecli
   mvn azure-spring-cloud:deploy
   ```

1. När distributionen är färdig kan du komma åt PiggyMetrics genom att använda URL: en som finns i utdata från föregående kommando.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=deploy)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat ett fjäder moln program från en maven-lagringsplats. Om du vill veta mer om Azure våren Cloud fortsätter du till självstudien om hur du förbereder din app för distribution.

> [!div class="nextstepaction"]
> [Förbered ditt Azure våren Cloud-program för distribution](spring-cloud-tutorial-prepare-app-deployment.md)
> [Lär dig mer om maven-plugin-program för Azure](https://github.com/microsoft/azure-maven-plugin)

Fler exempel finns på GitHub: [Azure våren Cloud-exempel](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

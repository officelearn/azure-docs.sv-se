---
title: Snabbstart - Starta ett program med Maven med Azure Spring Cloud
description: I den här snabbstarten startar du ett exempelprogram med Maven
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 4eddf34f4887f0e2bad08eb5700d83eaeae0ef7e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687095"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Snabbstart: Starta en Azure Spring Cloud-app med maven-plugin-programmet

Med hjälp av plugin-programmet Azure Spring Cloud Maven kan du enkelt skapa och uppdatera dina Azure Spring Cloud-program. Genom att fördefiniera en konfiguration kan du distribuera program till din befintliga Azure Spring Cloud-tjänst. I den här artikeln använder du ett exempelprogram som heter PiggyMetrics för att demonstrera den här funktionen.

Efter den här snabbstarten får du lära dig hur du:

> [!div class="checklist"]
> * Etablera en tjänstinstans
> * Konfigurera en konfigurationsserver för en instans
> * Klona och skapa mikrotjänster lokalt
> * Distribuera varje mikrotjänst
> * Tilldela en offentlig slutpunkt för ditt program

>[!Note]
> Azure Spring Cloud erbjuds för närvarande som en offentlig förhandsversion. Offentliga förhandsversioner gör det möjligt för kunder att experimentera med nya funktioner innan de släpps officiellt.  Offentliga förhandsversionsfunktioner och tjänster är inte avsedda för produktionsanvändning.  Mer information om support under förhandsversioner finns i våra [vanliga frågor](https://azure.microsoft.com/support/faq/) och svar eller en [supportförfrågan för](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) att få veta mer.


>[!TIP]
> Azure Cloud Shell är ett kostnadsfritt interaktivt skal som du kan använda för att köra kommandona i den här artikeln. Den har vanliga Azure-verktyg förinstallerade, inklusive de senaste versionerna av Git, Java Development Kit (JDK), Maven och Azure CLI. Om du är inloggad på din Azure-prenumeration startar du [Azure Cloud Shell](https://shell.azure.com). Mer information finns i [Översikt över Azure Cloud Shell](../cloud-shell/overview.md).

För att slutföra den här snabbstarten behöver du:

1. [Installera Git](https://git-scm.com/).
2. [Installera JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Installera Maven 3.0 eller senare](https://maven.apache.org/download.cgi).
4. [Registrera dig för en kostnadsfri Azure-prenumeration](https://azure.microsoft.com/free/).

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Etablera en tjänstinstans på Azure-portalen

1. Öppna den här [länken till Azure Spring Cloud i Azure-portalen i](https://ms.portal.azure.com/#create/Microsoft.AppPlatform)en webbläsare och logga in på ditt konto.

1. Ange **projektinformation** för exempelprogrammet enligt följande:

    1. Välj den **prenumeration** som programmet ska associeras med.
    1. Markera eller skapa en resursgrupp för programmet. Vi rekommenderar att du skapar en ny resursgrupp.  Exemplet nedan visar en ny `myspringservice`resursgrupp som heter .
    1. Ange ett namn för den nya Azure Spring Cloud-tjänsten.  Namnet måste vara mellan 4 och 32 tecken långt och får bara innehålla gemener, siffror och bindestreck. Det första tecknet i tjänstnamnet måste vara en bokstav och det sista tecknet måste vara antingen en bokstav eller ett nummer.  Tjänsten i exemplet nedan har `contosospringcloud`namnet .
    1. Välj en plats för ditt program bland de alternativ som anges.  I det här `East US`exemplet väljer vi .
    1. Välj **Granska + skapa** om du vill granska en sammanfattning av den nya tjänsten.  Om allt ser korrekt ut väljer du **Skapa**.

    > [!div class="mx-imgBorder"]
    > ![Välj Granska + skapa](media/maven-qs-review-create.jpg)

Det tar cirka 5 minuter innan tjänsten distribueras. När tjänsten har distribuerats väljer du **Gå till resurs** och sidan **Översikt** för tjänstinstansen visas.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=provision)

## <a name="set-up-your-configuration-server"></a>Konfigurera konfigurationsservern

1. Välj **Config Server**på **sidan** Översikt för tjänsten .
1. I avsnittet **Standarddatabas** anger du **https://github.com/Azure-Samples/piggymetrics-config** **URI** till och väljer sedan **Använd** för att spara ändringarna.

    > [!div class="mx-imgBorder"]
    > ![Definiera och tillämpa konfigurationsinställningar](media/maven-qs-apply-config.jpg)

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=config-server)

## <a name="clone-and-build-the-sample-application-repository"></a>Klona och skapa exempelprogramarkivet

1. Starta [Azure Cloud Shell](https://shell.azure.com).

1. Klona Git-databasen genom att köra följande kommando:

    ```console
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Ändra katalogen och skapa projektet genom att köra följande kommando:

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Generera konfigurationer och distribuera till Azure Spring Cloud

1. Generera konfigurationer genom att köra följande kommando i rotmappen i PiggyMetrics som innehåller den överordnade POM:

    ```console
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```

    a. Välj modulerna `gateway``auth-service`, `account-service`och .

    b. Välj din prenumeration och Azure Spring Cloud-tjänstklustret.

    c. I listan över tillföljande projekt anger du `gateway` det nummer som motsvarar för att ge det allmänhetens tillgång.
    
    d. Bekräfta konfigurationen.

1. POM innehåller nu plugin beroenden och konfigurationer. Distribuera apparna med följande kommando:

   ```console
   mvn azure-spring-cloud:deploy
   ```

1. När distributionen är klar kan du komma åt PiggyMetrics med hjälp av URL:en som anges i utdata från föregående kommando.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=deploy)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat ett Spring Cloud-program från en Maven-lagringsplats. Om du vill veta mer om Azure Spring Cloud fortsätter du till självstudien om hur du förbereder din app för distribution.

> [!div class="nextstepaction"]
> [Förbered ditt Azure Spring Cloud-program för distribution](spring-cloud-tutorial-prepare-app-deployment.md)
> [Läs mer om Maven-plugin-program för Azure](https://github.com/microsoft/azure-maven-plugin)

Fler exempel finns på GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

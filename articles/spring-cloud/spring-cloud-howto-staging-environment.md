---
title: Konfigurera en utvecklings miljö i Azure våren Cloud | Microsoft Docs
description: Lär dig hur du använder blå-grön-distribution med Azure våren Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 454eeaa2568891ec35fe698cdb20c5448e10887e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038988"
---
# <a name="how-to-set-up-a-staging-environment"></a>Konfigurera en utvecklings miljö

Den här artikeln visar hur du utnyttjar en mellanlagrings distribution med hjälp av ett blått-grönt distributions mönster i Azure våren Cloud. Du får också se hur du kan använda mellanlagrings distributionen i produktion utan att ändra produktions distributionen direkt.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har distribuerat PiggyMetrics-programmet från vår [självstudie om att starta ett program](spring-cloud-quickstart-launch-app-portal.md). PiggyMetrics består av tre program: "Gateway", "Account-service" och "auth-service".  

Om du har ett annat program som du vill använda för det här exemplet måste du göra en enkel ändring i en offentlig riktad del av programmet.  Den här ändringen särskiljer mellanlagrings distributionen från produktionen.

>[!NOTE]
> Innan du påbörjar den här snabb starten ska du se till att din Azure-prenumeration har åtkomst till Azure våren Cloud.  Som förhands gransknings tjänst ber vi dig att kontakta oss så att vi kan lägga till din prenumeration i vår lista över tillåtna.  Om du vill utforska funktionerna i Azure våren Cloud kan du kontakta oss via e-post: azure-spring-cloud@service.microsoft.com.

>[!TIP]
> Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln.  Den har ett förinstallerat vanligt Azure-verktyg, inklusive de senaste versionerna av Git, JDK, Maven och Azure CLI. Om du är inloggad på din Azure-prenumeration startar du [Azure Cloud Shell](https://shell.azure.com) från Shell.Azure.com.  Du kan lära dig mer om Azure Cloud Shell genom att [läsa vår dokumentation](../cloud-shell/overview.md)

För att slutföra den här artikeln:

1. [Installera Git](https://git-scm.com/)
1. [Installera JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
1. [Installera maven 3,0 eller senare](https://maven.apache.org/download.cgi)
1. [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
1. [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Installera Azure CLI-tillägget

Installera Azure våren Cloud-tillägget för Azure CLI med hjälp av följande kommando

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```
    
## <a name="view-all-deployments"></a>Visa alla distributioner

Gå till tjänst instansen i Azure Portal och välj **distributions hantering** för att visa alla distributioner. Du kan välja varje distribution för mer information.

## <a name="create-a-staging-deployment"></a>Skapa en mellanlagrings distribution

1. I din lokala utvecklings miljö gör du en liten ändring i Piggy-måttets Gateway-program. Ändra till exempel färgen i `gateway/src/main/resources/static/css/launch.css`. På så sätt kan du enkelt särskilja de två distributionerna. Kör följande kommando för att skapa jar-paketet: 

    ```azurecli
    mvn clean package
    ```

1. Skapa en ny distribution med Azure CLI och ge den namnet "grön" för mellanlagrings distributionen.

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. När distributionen har slutförts går du till sidan Gateway från **program instrument panelen** och ser alla instanser på fliken **app instances** till vänster.
  
> [!NOTE]
> Identifierings statusen är "OUT_OF_SERVICE" så att trafiken inte dirigeras till distributionen innan verifieringen är klar.

## <a name="verify-the-staging-deployment"></a>Verifiera mellanlagrings distributionen

1. gå tillbaka till sidan **distributions hantering** och välj den nya distributionen. Distributions statusen **bör visas.** Knappen "Tilldela/ta bort tilldelnings domän" kommer att inaktive ras eftersom den är en fristående miljö.

1. På sidan **Översikt** bör du se en **test slut punkt**. Kopiera och klistra in den på en ny webb sida så visas sidan nya Piggy mått.

>[!TIP]
> * Bekräfta att test slut punkten slutar med "/" för att säkerställa att CSS-inläsningen är korrekt.  
> * Om din webbläsare kräver att du anger inloggnings uppgifter för att visa sidan använder du [URL-avkodning](https://www.urldecoder.org/) för att avkoda test slut punkten. URL-avkodning returnerar en URL i formatet "https://\<username >: \<Password > @ \<cluster-Name >. test. azureapps. io/Gateway/grönt".  Använd detta för att få åtkomst till din slut punkt.

>[!NOTE]    
> Konfigurations Server inställningarna gäller för din utvecklings miljö samt produktion. Om du till exempel anger kontext Sök väg (`server.servlet.context-path`) för din app-gateway i konfigurations servern som *somepath*ändras sökvägen till din gröna distribution: "https://\<username >: \<password > @ \<cluster-Name >. test. azureapps. io/ Gateway/grön/somepath/... "
 
 Om du besöker din publika app gateway bör du se den gamla sidan utan din nya ändring.
    
## <a name="set-the-green-as-production-deployment"></a>Ställ in grön som produktions distribution

1. Om du har verifierat din ändring i din mellanlagrings miljö kan du skicka den till produktion. Gå tillbaka till **distributions hantering** och markera kryss rutan före "Gateway"-programmet.
2. Välj "Ange distribution".
3. Välj "grönt" från menyn "produktions distribution" och välj **Använd**
4. Gå till **översikts** sidan för gateway-program. Om du redan har tilldelat en domän för ditt Gateway-program visas URL: en på sidan **Översikt** . Besök URL: en så visas sidan för ändrade Piggy-mått.

>[!NOTE]
> När den gröna distributionen har angetts till produktions miljön blir den tidigare distributionen den mellanlagrings distributionen.

## <a name="modify-the-staging-deployment"></a>Ändra mellanlagrings distributionen

Om du inte är nöjd med ändringen kan du ändra program koden, bygga ett nytt jar-paket och överföra det till din gröna distribution med hjälp av Azure CLI.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-a-staging-deployment"></a>Ta bort en mellanlagrings distribution

Ta bort din mellanlagrings distribution från Azure-porten genom att gå till sidan för mellanlagrings distribution och välja knappen **ta bort** .

Du kan också ta bort mellanlagrings distributionen från Azure CLI med följande kommando:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

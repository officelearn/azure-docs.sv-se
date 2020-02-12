---
title: Konfigurera en utvecklings miljö i Azure våren Cloud | Microsoft Docs
description: Lär dig hur du använder blå-grön-distribution med Azure våren Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 5612a514ed89f73453f3751b34263b0beeea1c59
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138144"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Konfigurera en utvecklings miljö i Azure våren Cloud

Den här artikeln beskriver hur du konfigurerar en mellanlagrings distribution med hjälp av ett blått-grönt distributions mönster i Azure våren Cloud. Blå/grön distribution är ett mönster för kontinuerlig leverans för Azure DevOps förlitar sig på att hålla en befintlig (blå) version live, medan en ny (grön) distribueras. Den här artikeln visar hur du kan använda mellanlagrings distributionen i produktion utan att ändra produktions distributionen direkt.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har distribuerat PiggyMetrics-programmet från vår [själv studie kurs om hur du startar ett Azure våren Cloud-program](spring-cloud-quickstart-launch-app-portal.md). PiggyMetrics består av tre program: "Gateway", "Account-service" och "auth-service".  

Om du vill använda ett annat program för det här exemplet måste du göra en enkel ändring i en offentlig del av programmet.  Den här ändringen särskiljer mellanlagrings distributionen från produktionen.

>[!TIP]
> Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt som du kan använda för att köra instruktionerna i den här artikeln.  Den har vanliga, förinstallerade Azure-verktyg, inklusive de senaste versionerna av Git, JDK, Maven och Azure CLI. Om du är inloggad på din Azure-prenumeration startar du [Azure Cloud Shell](https://shell.azure.com).  Mer information finns i [Översikt över Azure Cloud Shell](../cloud-shell/overview.md).

Följ anvisningarna i nästa avsnitt om du vill konfigurera en utvecklings miljö i Azure våren Cloud.

## <a name="install-the-azure-cli-extension"></a>Installera Azure CLI-tillägget

Installera Azure våren Cloud-tillägget för Azure CLI med hjälp av följande kommando:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Visa alla distributioner

Gå till tjänst instansen i Azure Portal och välj **Deployment Management** för att visa alla distributioner. Om du vill visa mer information kan du välja varje distribution.

## <a name="create-a-staging-deployment"></a>Skapa en mellanlagrings distribution

1. I din lokala utvecklings miljö gör du en liten ändring i PiggyMetrics Gateway-programmet. Du kan till exempel ändra färgen i filen *Gateway/src/main/Resources/static/CSS/start. CSS* . På så sätt kan du enkelt särskilja de två distributionerna. Skapa jar-paketet genom att köra följande kommando: 

    ```azurecli
    mvn clean package
    ```

1. Skapa en ny distribution i Azure CLI och ge den namnet "grön" för mellanlagrings distributionen.

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. När distributionen har slutförts går du till sidan Gateway från **program instrument panelen**och visar alla dina instanser på fliken **program instanser** till vänster.
  
> [!NOTE]
> Identifierings statusen är *OUT_OF_SERVICE* så att trafiken inte dirigeras till den här distributionen innan verifieringen är klar.

## <a name="verify-the-staging-deployment"></a>Verifiera mellanlagrings distributionen

1. Gå tillbaka till sidan **distributions hantering** och välj den nya distributionen. Distributions statusen *bör visas.* Knappen **tilldela/ta bort tilldelnings domän** ska vara nedtonad, eftersom miljön är en mellanlagrings miljö.

1. I **översikts** fönstret bör du se en **test slut punkt**. Kopiera och klistra in den i ett nytt webbläsarfönster och den nya PiggyMetrics-sidan ska visas.

>[!TIP]
> * Bekräfta att test slut punkten slutar med ett snedstreck (/) för att säkerställa att CSS-filen läses in på rätt sätt.  
> * Om din webbläsare kräver att du anger inloggnings uppgifter för att visa sidan använder du [URL-avkodning](https://www.urldecoder.org/) för att avkoda test slut punkten. URL-avkodning returnerar en URL i formatet "https://\<användar namn >:\<lösen ord > @\<kluster namn >. test. azureapps. io/Gateway/grönt".  Använd det här formuläret för att få åtkomst till din slut punkt.

>[!NOTE]    
> Konfigurations Server inställningarna gäller både för din mellanlagrings miljö och produktion. Om du till exempel anger kontext Sök vägen (`server.servlet.context-path`) för din app-gateway i config server som *somepath*ändras sökvägen till din gröna distribution till "https://\<username >:\<lösen ord > @\<kluster namn >. test. azureapps. io/Gateway/grönt/somepath/...".
 
 Om du besöker den offentliga app-gatewayen bör du se den gamla sidan utan din nya ändring.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Ange den gröna distributionen som produktions miljö

1. När du har verifierat din ändring i din mellanlagrings miljö kan du skicka den till produktion. Gå tillbaka till **distributions hantering**och markera kryss rutan **Gateway** -program.

2. Välj **Ange distribution**.
3. I listan **produktions distribution** väljer du **grönt**och väljer sedan **Använd**.
4. Gå till **översikts** sidan för gateway-program. Om du redan har tilldelat en domän för ditt Gateway-program visas URL: en i fönstret **Översikt** . Om du vill visa den ändrade PiggyMetrics-sidan väljer du webb adressen och går till webbplatsen.

>[!NOTE]
> När du har angett den gröna distributionen som produktions miljö blir den tidigare distributionen den mellanlagrings distributionen.

## <a name="modify-the-staging-deployment"></a>Ändra mellanlagrings distributionen

Om du inte är nöjd med ändringen kan du ändra program koden, bygga ett nytt jar-paket och ladda upp det till din gröna distribution med hjälp av Azure CLI.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Ta bort mellanlagrings distributionen

Om du vill ta bort din mellanlagrings distribution från Azure-porten går du till sidan för mellanlagrings distribution och väljer sedan knappen **ta bort** .

Du kan också ta bort mellanlagrings distributionen från Azure CLI genom att köra följande kommando:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

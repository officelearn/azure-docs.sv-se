---
title: Konfigurera en mellanlagringsmiljö i Azure Spring Cloud | Microsoft-dokument
description: Lär dig hur du använder blågrön distribution med Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 2e29f6a75b303518ac34ecf9b570bd7638cf0c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471038"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Konfigurera en mellanlagringsmiljö i Azure Spring Cloud

I den här artikeln beskrivs hur du konfigurerar en mellanlagringsdistribution med hjälp av det blågröna distributionsmönstret i Azure Spring Cloud. Blå/grön distribution är ett mönster för kontinuerlig leverans för Azure DevOps förlitar sig på att hålla en befintlig (blå) version live, medan en ny (grön) distribueras. Den här artikeln visar hur du placerar den mellanlagringsdistributionen i produktion utan att ändra produktionsdistributionen direkt.

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du redan har distribuerat PiggyMetrics-programmet från vår [handledning om att starta ett Azure Spring Cloud-program](spring-cloud-quickstart-launch-app-portal.md). PiggyMetrics består av tre program: "gateway", "account-service" och "auth-service".  

Om du vill använda ett annat program för det här exemplet måste du göra en enkel ändring i en offentlig del av programmet.  Den här ändringen skiljer distributionen mellan mellanlagringen från produktionen.

>[!TIP]
> Azure Cloud Shell är ett kostnadsfritt interaktivt skal som du kan använda för att köra instruktionerna i den här artikeln.  Den har vanliga, förinstallerade Azure-verktyg, inklusive de senaste versionerna av Git, JDK, Maven och Azure CLI. Om du är inloggad på din Azure-prenumeration startar du ditt [Azure Cloud Shell](https://shell.azure.com).  Mer information finns i [Översikt över Azure Cloud Shell](../cloud-shell/overview.md).

Om du vill konfigurera en mellanlagringsmiljö i Azure Spring Cloud följer du instruktionerna i nästa avsnitt.

## <a name="install-the-azure-cli-extension"></a>Installera Azure CLI-tillägget

Installera Azure Spring Cloud-tillägget för Azure CLI med hjälp av följande kommando:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Visa alla distributioner

Gå till din tjänstinstans i Azure-portalen och välj **Distributionshantering** för att visa alla distributioner. Om du vill visa mer information kan du välja varje distribution.

## <a name="create-a-staging-deployment"></a>Skapa en mellanlagringsdistribution

1. I din lokala utvecklingsmiljö gör du en liten ändring av PiggyMetrics gateway-programmet. Ändra till exempel färgen i filen *gateway/src/main/resources/static/css/launch.css.* På så sätt kan du enkelt skilja de två distributionerna. Om du vill skapa jar-paketet kör du följande kommando: 

    ```console
    mvn clean package
    ```

1. Skapa en ny distribution i Azure CLI och ge distributionsnamnet "grön".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. När distributionen har slutförts öppnar du gatewaysidan från **programinstrumentpanelen**och visar alla dina instanser på fliken **Appinstanser** till vänster.
  
> [!NOTE]
> Identifieringsstatusen *är OUT_OF_SERVICE* så att trafiken inte dirigeras till den här distributionen innan verifieringen är klar.

## <a name="verify-the-staging-deployment"></a>Verifiera mellanlagringsdistributionen

1. Gå tillbaka till sidan **Distributionshantering** och välj din nya distribution. Distributionsstatusen ska visa *Kör*. Knappen **Tilldela/Ta bort domän** ska visas nedtonad eftersom miljön är en mellanlagringsmiljö.

1. I **fönstret Översikt** bör du se en **testslutpunkt**. Kopiera och klistra in den i ett nytt webbläsarfönster, och den nya PiggyMetrics-sidan ska visas.

>[!TIP]
> * Bekräfta att testslutpunkten slutar med ett snedstreck (/) för att säkerställa att CSS-filen läses in korrekt.  
> * Om din webbläsare kräver att du anger inloggningsuppgifter för att visa sidan använder du [URL-avkoda](https://www.urldecoder.org/) för att avkoda testslutpunkten. URL-avkodning returnerar en URL\<i formuläret "https://\<användarnamn>:\<lösenord>@ klusternamn>.test.azureapps.io/gateway/green".  Använd det här formuläret för att komma åt slutpunkten.

>[!NOTE]    
> Konfigurationsserverinställningar gäller både mellanlagringsmiljön och produktionen. Om du till exempel anger`server.servlet.context-path`kontextsökvägen ( ) för appgatewayen i konfigurationsservern som *en viss sökväg*ändras sökvägen till den gröna distributionen till "https://\<användarnamn>:\<lösenord>@\<klusternamn>.test.azureapps.io/gateway/green/somepath/...".
 
 Om du besöker din offentliga appgateway just nu bör du se den gamla sidan utan din nya ändring.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Ange den gröna distributionen som produktionsmiljö

1. När du har verifierat ändringen i mellanlagringsmiljön kan du skicka den till produktion. Återgå till **distributionshantering**och markera kryssrutan **gateway-program.**

2. Välj **Ange distribution**.
3. Välj **Grön**i listan **Produktionsdistribution** och välj sedan **Använd**.
4. Gå till sidan **Översikt** för gateway-program. Om du redan har tilldelat en domän för gateway-programmet visas URL:en i **fönstret Översikt.** Om du vill visa den ändrade PiggyMetrics-sidan markerar du WEBBADRESSEN och går till webbplatsen.

>[!NOTE]
> När du har angett den gröna distributionen som produktionsmiljö blir den tidigare distributionen mellanlagringsdistributionen.

## <a name="modify-the-staging-deployment"></a>Ändra mellanlagringsdistributionen

Om du inte är nöjd med ändringen kan du ändra programkoden, skapa ett nytt jar-paket och ladda upp den till din gröna distribution med hjälp av Azure CLI.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Ta bort mellanlagringsdistributionen

Om du vill ta bort distributionen av mellanlagring från Azure-porten går du till distributionssidan för mellanlagring och väljer sedan knappen **Ta bort.**

Du kan också ta bort distributionen för mellanlagring från Azure CLI genom att köra följande kommando:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

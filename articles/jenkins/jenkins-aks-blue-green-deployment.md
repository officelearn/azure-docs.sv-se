---
title: Distribuera till Azure Kubernetes Service (AKS) med Jenkins och blå/grön distribution mönster
description: Lär dig hur du distribuerar till Azure Kubernetes Service (AKS) med Jenkins och blå/grön distribution mönster
services: app-service\web
documentationcenter: ''
author: tomarcher
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 07/23/2018
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 472622f78303593b7a4d5d5136aa47f34a1f44b1
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39228190"
---
# <a name="deploy-to-azure-kubernetes-service-aks-using-jenkins-and-bluegreen-deployment-pattern"></a>Distribuera till Azure Kubernetes Service (AKS) med Jenkins och blå/grön distribution mönster

Azure Kubernetes Service (AKS) hanterar din värdmiljö för Kubernetes, vilket gör det enkelt att snabbt distribuera och hantera containerbaserade program utan kunskaper om orkestrering av containrar. AKS eliminerar också belastningen för pågående åtgärder och underhåll genom att etablera, uppgradera och skala resurser på begäran, utan att dina program försätts offline. Mer information om AKS ser du att den [dokumentationen om AKS](/azure/aks/).

Blå/grön distribution är en DevOps kontinuerlig leverans (CD)-mönster som förlitar sig på att hålla en befintlig (blå) version live när en ny (grön) har distribuerats. Det här mönstret används vanligtvis för belastningsutjämning för att dirigera ökad mängd trafik till grön distribution. Om övervakning identifierar en incident, kan trafik dirigeras om till den blå distributionen fortfarande körs. Mer information om kontinuerlig leverans finns i artikeln [vad är kontinuerlig leverans](/azure/devops/what-is-continuous-delivery).

I den här självstudien får du lära dig hur du utför följande uppgifter i lära dig hur du distribuerar till AKS med Jenkins och mönstret blå/grön distribution:

> [!div class="checklist"]
> * Lär dig mer om mönstret blå/grön distribution
> * Skapa ett hanterat Kubernetes-kluster.
> * Kör ett exempelskript för att konfigurera ett Kubernetes-kluster
> * Manuellt konfigurera ett Kubernetes-kluster
> * Skapa och köra ett Jenkins-jobb

## <a name="prerequisites"></a>Förutsättningar
- [GitHub-konto](https://github.com) : du behöver ett GitHub-konto för att klona lagringsplatsexemplet.
- [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) : The Azure CLI 2.0 används för att skapa Kubernetes-kluster.
- [Chocolatey](https://chocolatey.org) -en pakethanterare som används för att installera kubectl.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) : ett kommandoradsgränssnitt för att köra kommandon mot Kubernetes-kluster.
- [jq](https://stedolan.github.io/jq/download/) : en enkel kommandoradsverktyget JSON-processor.

## <a name="clone-the-sample-app-from-github"></a>Klona exempelappen från GitHub

En exempelapp som visar hur du distribuerar till AKS med Jenkins och blå/grön mönstret finns på Microsoft-lagringsplatsen i GitHub. I det här avsnittet ska du skapar en förgrening av den lagringsplatsen i din GitHub och klona appen till ditt lokala system.

1. Bläddra till GitHub-lagringsplatsen för den [todo-app-java-från azure](https://github.com/microsoft/todo-app-java-on-azure.git) exempelappen.

    ![Exempelappen på Microsoft GitHub-lagringsplatsen.](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. Förgrena lagringsplatsen genom att välja **förgrening** uppe till höger på sidan, och följ anvisningarna för att Förgrena lagringsplatsen i ditt GitHub-konto.

    ![Förgrena sample-appen till ditt GitHub-konto.](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. När du Förgrena lagringsplatsen du se att namnet på kontot ändras till namnet på ditt konto och en kommentar som anger från där lagringsplatsen har Förgrenat (Microsoft).

    ![Exempelappen efter att förgrenade till ett annat GitHub-konto.](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Att välja **klona eller ladda ned**.

    ![GitHub kan du snabbt klona eller ladda ned en lagringsplats.](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. I den **klona med HTTPS** fönstret väljer du ikonen för kopiera.

    ![Kopiera klon-URL till Urklipp.](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Öppna en terminal eller Bash-fönstret.

1. Ändra kataloger till önskad plats där du vill lagra den lokala kopian (klonar) av lagringsplatsen.

1. Med hjälp av den `git clone` kommandot, klona den URL som du kopierade tidigare.

    ![Skriv ”git-klonen” och URL-klonen kan skapa en klon av lagringsplatsen.](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Välj den &lt;RETUR > tangent för att starta Kloningen.

    ![”Git-klonen” kommandot skapar en personlig kopia av lagringsplatsen där du kan testa](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Ändra sökvägen till den nyligen skapade katalog som innehåller kloning av app-källan.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Skapa och konfigurera en hanterad Kubernetes-kluster

I det här avsnittet ska utföra du följande steg:

- Använda Azure CLI 2.0 för att skapa en hanterad Kubernetes-kluster.
- Lär dig hur du ställer in ett kluster som antingen genom att använda installationsskriptet eller manuellt.
- Skapa Azure Container Registry.

> [!NOTE]   
> AKS förhandsvisas just nu. Information om hur du aktiverar förhandsversionen för din Azure-prenumeration. Se [Snabbstart: distribuera ett kluster i Azure Kubernetes Service (AKS) ](/azure/aks/kubernetes-walkthrough#enabling-aks-preview-for-your-azure-subscription) för mer information.

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Skapa en hanterad Kubernetes-kluster med hjälp av Azure CLI 2.0
För att skapa en hanterad Kubernetes-kluster med [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest), se till att du använder Azure CLI version 2.0.25 eller senare.

1. Logga in på ditt Azure-konto. När du anger följande `az login` kommandot instruktioner som förklarar hur du slutför inloggning. 
    
    ```bash
    az login
    ```

1. När du kör den `az login` kommandot i det föregående steget, en lista över alla dina Azure-prenumerationer som visar (tillsammans med deras prenumerations-ID). I det här steget ska ange du standard Azure-prenumeration. Ersätt den &lt;your-subscription-id > med önskade Azure prenumerations-ID. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. Skapa en resursgrupp. Ersätt den &lt;your-resource-group-name > med namnet på din nya resursgrupp och Ersätt den &lt;din plats > med platsen. Kommandot `az account list-locations` visar alla Azure-platser. I förhandsversionen AKS är inte alla platser tillgängliga. Om du anger en plats som inte är giltig just nu ett felmeddelande visas en lista över tillgängliga platser.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Skapa Kubernetes-klustret. Ersätt den &lt;your-resource-group-name > med namnet på resursgruppen som skapades i föregående steg och Ersätt den &lt;du-kubernetes-kluster-name > med namnet på det nya klustret. (Den här processen kan ta flera minuter att slutföra.)

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Ställ in Kubernetes-kluster

Konfigurera en blå/grön distribution i AKS kan göras antingen med ett installationsskript enligt exemplet klonas tidigare eller manuellt. I det här avsnittet lär du dig att göra både och.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>Kubernetes-klustret via installationsprogrammet exempelskriptet
1. Redigera den **deploy/aks/setup/setup.sh** filen och ersätta följande platshållare med lämpliga värden för din miljö: 

    - **&lt;din-resource-group-name >**
    - **&lt;din-kubernetes-kluster-name >**
    - **&lt;din plats >**
    - **&lt;din dns-namn-suffix >**

    ![Setup.sh skriptet innehåller flera platshållare som kan ändras för din miljö.](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. Kör installationsskriptet.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Konfigurera ett Kubernetes-kluster manuellt 
1. Ladda ned Kubernetes-konfigurationen till profilmappen.

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. Gå till katalogen för den **distribuera/aks/setup** directory. 

1. Kör följande **kubectl** kommandon för att konfigurera tjänster för den offentliga slutpunkten och två test-slutpunkter.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Uppdatera DNS-namnet för allmänheten och test-slutpunkter. När ett Kubernetes-kluster skapas en [ytterligare resursgrupp](https://github.com/Azure/AKS/issues/3) skapas med namngivning patter av **MC_&lt;your-resource-group-name > _&lt; din-kubernetes-kluster-name >_&lt;din plats >**.

    Leta upp den offentliga ip i resursgruppen.

    ![Offentlig IP-adress i resursgruppen](./media/jenkins-aks-blue-green-deployment/publicip.png)

    För varje tjänst hittar du den externa IP-adressen genom att köra följande kommando:
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    Uppdatera DNS-namnet för motsvarande IP-adress med följande kommando:

    ```bash
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Upprepa anropet för `todoapp-test-blue` och `todoapp-test-green`:

    ```bash
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    DNS-namnet måste vara unikt i din prenumeration. `<your-dns-name-suffix>` kan användas för att se till att unikt.

### <a name="create-azure-container-registry"></a>Skapa Azure Container Registry

1. Kör den `az acr create` kommando för att skapa ett Azure Container Registry. När du skapar Azure Container Registry, använda `login server` som URL för Docker-register i nästa avsnitt.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Kör den `az acr credential` kommando för att visa dina autentiseringsuppgifter för Azure Container Registry. Observera Docker registreringsanvändarnamn och lösenord när de används i nästa avsnitt.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Förbereda Jenkins-servern

I det här avsnittet lär du dig att förbereda Jenkins-servern och kör en version som är bra för testning. Dock som beskrivs i artikeln Jenkins på den [säkerhetsriskerna med att skapa på master](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master), är det bäst att använda en [Virtuella Azure-datoragenten](https://plugins.jenkins.io/azure-vm-agents) eller [Azure Container-agenten](https://plugins.jenkins.io/azure-container-agents) till Skapa en agent i Azure för att köra dina versioner. 

1. Distribuera en [Jenkins-original på Azure](https://aka.ms/jenkins-on-azure).

1. Anslut till servern via SSH och installera build-hanteringsverktygen på den server där du kör din version.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Installera Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Se till att användaren `jenkins` har behörighet att köra den `docker` kommandon.

1. [Installera kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [Ladda ned jq](https://stedolan.github.io/jq/download/).

1. Installera jq med följande kommando:

   ```bash
   sudo apt-get install jq
   ```
   
1. Installera den plugin-program i Jenkins genom att utföra följande steg i Jenkins-instrumentpanelen:

    1. Välj **hantera Jenkins > Hantera plugin-program > tillgängliga**.
    1. Sök efter och installera plugin-programmet för Azure Container Service.

1. Du måste lägga till autentiseringsuppgifter som används för att hantera resurser i Azure. Om du inte redan har plugin-programmet, installera den **Azure Credential** plugin-programmet.

1. Lägg till din Azure Service Principal-autentiseringsuppgift som typen/typ **Microsoft Azure Service Principal**.

1. Lägg till ditt Azure Docker Registry användarnamn och lösenord (som erhålls under **skapa Azure Container Registry**) som typ/typ **användarnamn med lösenord**.

## <a name="edit-the-jenkinsfile"></a>Redigera Jenkinsfile

1. Navigera till i din egen lagringsplats `/deploy/aks/` och öppna `Jenkinsfile`

2. Uppdatera filen på följande sätt:

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    Och uppdatera ACR uppgifts-ID:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>Skapa jobbet
1. Lägg till ett nytt jobb i typen **Pipeline**.

1. Välj **Pipeline > Definition > Pipeline-skriptet från SCM**.

1. Ange URL: en för SCM-lagringsplats med din &lt;your-förgrenade-lagringsplatsen >

1. Ange skriptets sökväg som `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>Kör jobbet

1. Kontrollera att du kan köra projektet har i din lokala miljö. [Kör projektet på den lokala datorn](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it)

1. Kör Jenkins-jobbet. När Jenkins-jobbet körs första gången, distribuerar Jenkins todo-appen till blå-miljön, vilket är inaktiv standardmiljön. 

1. Gå till URL: er för att kontrollera att jobbet kördes:
    - Offentlig slutpunkt: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Blå slutpunkt- `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Grön slutpunkt- `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

Allmänheten och blå test-slutpunkterna har samma uppdatering och grön slutpunkten visar tomcat standardavbildningen. 

Om du kör versionen mer än en gång, går den igenom blå och grön distributioner. Med andra ord om den aktuella miljön är blå, ska jobbet distribuera och testning till grön miljön och uppdatera sedan den offentliga slutpunkten för programmet att dirigera trafik till den gröna miljön om allt är bra med testning.

## <a name="additional-information"></a>Ytterligare information

Mer information om stilleståndstid distribution finns i denna [snabbstartsmall](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment). 

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort Azure-resurserna du skapade i den här självstudien.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>Felsökning

Om du stöter på buggar med Jenkins plugin-program kan du rapportera problemet i den [Jenkins JIRA](https://issues.jenkins-ci.org/) för en viss komponent.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att distribuera till Azure Kubernetes Service (AKS) med Jenkins och blå/grön distribution mönster. Mer information om Azure Jenkins-providern finns Jenkins på Azure webbplats.

> [!div class="nextstepaction"]
> [Jenkins på Azure](/azure/jenkins/)
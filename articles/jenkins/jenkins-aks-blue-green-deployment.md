---
title: Distribuera till Azure Kubernetes Service (AKS) med hjälp av Jenkins och mönstret blå/grön distribution
description: Lär dig hur du distribuerar till Azure Kubernetes Service (AKS) med Jenkins och mönstret blå/grön distribution.
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
ms.openlocfilehash: 384681ae0ba212b485022ac81743528f96075ec8
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716470"
---
# <a name="deploy-to-azure-kubernetes-service-aks-by-using-jenkins-and-the-bluegreen-deployment-pattern"></a>Distribuera till Azure Kubernetes Service (AKS) med hjälp av Jenkins och mönstret blå/grön distribution

Azure Kubernetes Service (AKS) hanterar din värdbaserade Kubernetes-miljö, vilket gör det snabbt och enkelt att distribuera och hantera behållarbaserade program. Du behöver inte kunskap om orkestrering av behållare. AKS eliminerar också belastningen för pågående åtgärder och underhåll genom att etablering, uppgradering och skalning resurser på begäran. Du behöver inte koppla från dina program. Läs mer om AKS, den [dokumentationen om AKS](/azure/aks/).

Blå/grön distribution är en kontinuerlig leverans för Azure DevOps-mönster som förlitar sig på att hålla en befintlig (blå) version live, medan en ny (grön) distribueras. Det här mönstret används vanligtvis för belastningsutjämning för att dirigera ökad mängd trafik till grön distribution. Om övervakningen upptäcker en incident, kan trafik dirigeras om till blå distributionen, som är igång. Läs mer om kontinuerlig leverans, [vad är kontinuerlig leverans](/azure/devops/what-is-continuous-delivery).

I den här självstudien får du lära dig hur du utför följande uppgifter:

> [!div class="checklist"]
> * Lär dig mer om mönstret blå/grön distribution
> * Skapa en hanterad Kubernetes-kluster
> * Kör ett exempelskript för att konfigurera ett Kubernetes-kluster
> * Manuellt konfigurera ett Kubernetes-kluster
> * Skapa och köra ett Jenkins-jobb

## <a name="prerequisites"></a>Förutsättningar
- [GitHub-konto](https://github.com) : du behöver ett GitHub-konto för att klona lagringsplatsexemplet.
- [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) : du använder Azure CLI 2.0 för att skapa Kubernetes-klustret.
- [Chocolatey](https://chocolatey.org): en pakethanterare som du använder för att installera kubectl.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/): ett kommandoradsgränssnitt som du använder för att köra kommandon mot Kubernetes-kluster.
- [jq](https://stedolan.github.io/jq/download/): en enkel, kommandoradsverktyg JSON-processor.

## <a name="clone-the-sample-app-from-github"></a>Klona exempelappen från GitHub

Du kan hitta en exempelapp som visar hur du distribuerar till AKS med hjälp av Jenkins och blå/grön mönstret på Microsoft-lagringsplatsen i GitHub. I det här avsnittet ska du skapar en förgrening av den lagringsplatsen i din GitHub och klona appen till ditt lokala system.

1. Bläddra till GitHub-lagringsplatsen för den [todo-app-java-från azure](https://github.com/microsoft/todo-app-java-on-azure.git) exempelappen.

    ![Skärmbild av exempelappen på Microsoft GitHub-lagringsplatsen](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. Förgrena lagringsplatsen genom att välja **förgrening** uppe till höger på sidan, och följ anvisningarna för att Förgrena lagringsplatsen i ditt GitHub-konto.

    ![Skärmbild av GitHub kan förgrening](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. När du Förgrena lagringsplatsen du se att namnet på kontot ändras till namnet på ditt konto och en kommentar som anger från där lagringsplatsen har Förgrenat (Microsoft).

    ![Skärmbild av GitHub-kontonamn och Obs!](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Välj **klona eller ladda ned**.

    ![Skärmbild av GitHub-alternativet för att klona eller ladda ned en lagringsplats](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. I den **klona med HTTPS** väljer den **kopia** ikon.

    ![Skärmbild av GitHub-alternativet för att kopiera klon-URL till Urklipp](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Öppna en terminal eller Git Bash-fönstret.

1. Ändra kataloger till önskad plats där du vill lagra den lokala kopian (klonar) av lagringsplatsen.

1. Med hjälp av den `git clone` kommandot, klona den URL som du kopierade tidigare.

    ![Skärmbild av Git Bash git kloningskommandot](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Tryck på RETUR för att börja Kloningen.

    ![Skärmbild av Git Bash git klon-kommandoresultat](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Ändra sökvägen till den nyligen skapade katalog som innehåller kloning av app-källan.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Skapa och konfigurera en hanterad Kubernetes-kluster

I det här avsnittet ska utföra du följande steg:

- Använda Azure CLI 2.0 för att skapa en hanterad Kubernetes-kluster.
- Lär dig hur du konfigurerar ett kluster, antingen med hjälp av installationsskriptet eller manuellt.
- Skapa en instans av Azure Container Registry-tjänsten.

> [!NOTE]   
> AKS förhandsvisas just nu. Information om hur du aktiverar förhandsversionen för din Azure-prenumeration finns i [Snabbstart: distribuera ett kluster i Azure Kubernetes Service (AKS)](/azure/aks/kubernetes-walkthrough#enabling-aks-preview-for-your-azure-subscription).

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Skapa en hanterad Kubernetes-kluster med hjälp av Azure CLI 2.0
För att skapa en hanterad Kubernetes-kluster med den [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), se till att du använder Azure CLI version 2.0.25 eller senare.

1. Logga in på ditt Azure-konto. När du har angett följande kommando får du instruktioner som förklarar hur du slutför inloggningen. 
    
    ```bash
    az login
    ```

1. När du kör den `az login` kommandot i det föregående steget, visas en lista över alla dina Azure-prenumerationer (tillsammans med deras prenumerations-ID). I det här steget ska ange du standard Azure-prenumeration. Ersätt den &lt;your-subscription-id > med önskade Azure prenumerations-ID. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. Skapa en resursgrupp. Ersätt den &lt;your-resource-group-name > med namnet på din nya resursgrupp och Ersätt den &lt;din plats > med platsen. Kommandot `az account list-locations` visar alla Azure-platser. I förhandsversionen AKS är inte alla platser tillgängliga. Om du anger en plats som inte är giltig just nu, visas ett felmeddelande tillgängliga platser.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Skapa Kubernetes-klustret. Ersätt den &lt;your-resource-group-name > med namnet på resursgruppen som skapades i föregående steg och Ersätt den &lt;your-kubernetes-kluster-name > med namnet på det nya klustret. (Den här processen kan ta flera minuter att slutföra.)

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Ställ in Kubernetes-kluster

Du kan ställa in en blå/grön distribution i AKS manuellt eller med en konfiguration skript som finns i exemplet klonas tidigare. I det här avsnittet lär du dig att göra både och.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>Kubernetes-klustret via installationsprogrammet exempelskriptet
1. Redigera den **deploy/aks/setup/setup.sh** filen och ersätta följande platshållare med lämpliga värden för din miljö: 

    - **&lt;din-resource-group-name >**
    - **&lt;din-kubernetes-kluster-name >**
    - **&lt;din plats >**
    - **&lt;din dns-namn-suffix >**

    ![Skärmbild setup.sh skriptet i bash, med flera platshållare markerat](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. Kör installationsskriptet.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Konfigurera ett Kubernetes-kluster manuellt 
1. Ladda ned Kubernetes-konfigurationen till profilmappen.

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. Ändra katalogen till den **distribuera/aks/setup** directory. 

1. Kör följande **kubectl** kommandon för att konfigurera tjänster för den offentliga slutpunkten och två test-slutpunkter.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Uppdatera DNS-namnet för allmänheten och test-slutpunkter. När du skapar ett Kubernetes-kluster kan du också skapa en [ytterligare resursgrupp](https://github.com/Azure/AKS/issues/3), med namngivningsmönstret av **MC_&lt;your-resource-group-name >_ &lt; din-kubernetes-kluster-name >_&lt;din plats >**.

    Leta upp den offentliga IP-adresser i resursgruppen.

    ![Skärmbild av den offentliga IP-adresser i resursgruppen](./media/jenkins-aks-blue-green-deployment/publicip.png)

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

    DNS-namnet måste vara unikt i din prenumeration. Du kan använda för att säkerställa unik `<your-dns-name-suffix>`.

### <a name="create-an-instance-of-container-registry"></a>Skapa en instans av Container Registry

1. Kör den `az acr create` kommando för att skapa en instans av Container Registry. I nästa avsnitt, kan du sedan använda `login server` som Docker registry-URL.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Kör den `az acr credential` kommando för att visa dina autentiseringsuppgifter för Container Registry. Observera Docker registreringsanvändarnamn och lösenord, som du behöver dem i nästa avsnitt.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Förbereda Jenkins-servern

I det här avsnittet lär du dig att förbereda Jenkins-servern och kör en version som är bra för testning. Dock bör du använda en [Virtuella Azure-datoragenten](https://plugins.jenkins.io/azure-vm-agents) eller [Azure Container-agenten](https://plugins.jenkins.io/azure-container-agents) att skapa en agent i Azure för att köra dina versioner. Mer information finns i Jenkins-artikeln på den [säkerhetsriskerna med att skapa på master](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

1. Distribuera en [Jenkins-original på Azure](https://aka.ms/jenkins-on-azure).

1. Anslut till servern via SSH och installera build-hanteringsverktygen på den server där du kör din version.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Installera Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Kontrollera att användaren `jenkins` har behörighet att köra den `docker` kommandon.

1. [Installera kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [Ladda ned jq](https://stedolan.github.io/jq/download/).

1. Installera jq med följande kommando:

   ```bash
   sudo apt-get install jq
   ```
   
1. Installera den plugin-program i Jenkins genom att utföra följande steg i Jenkins-instrumentpanelen:

    1. Välj **hantera Jenkins > Hantera plugin-program > tillgängliga**.
    1. Sök efter och installera plugin-programmet för Azure Container Service.

1. Lägg till autentiseringsuppgifter för att hantera resurser i Azure. Om du inte redan har plugin-programmet, installera den **Azure Credential** plugin-programmet.

1. Lägg till din Azure Service Principal-autentiseringsuppgift som typ av **Microsoft Azure Service Principal**.

1. Lägg till ditt Azure Docker registreringsanvändarnamn och lösenord (som erhålls i avsnittet ”Skapa en instans av Container Registry”) som typ av **användarnamn med lösenord**.

## <a name="edit-the-jenkinsfile"></a>Redigera Jenkinsfile

1. Gå till i din egen lagringsplats `/deploy/aks/`, och öppna `Jenkinsfile`.

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
    
    Uppdatera Behållarregister uppgifts-ID:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>Skapa jobbet
1. Lägg till ett nytt jobb i typen **Pipeline**.

1. Välj **Pipeline** > **Definition** > **Pipeline-skriptet från SCM**.

1. Ange URL: en för SCM-lagringsplats med din &lt;your-förgrenade-lagringsplatsen >.

1. Ange skriptets sökväg som `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>Kör jobbet

1. Kontrollera att du kan köra projektet har i din lokala miljö. Här är hur: [kör projektet på den lokala datorn](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it).

1. Kör Jenkins-jobbet. Första gången du kör jobbet distribuerar Jenkins todo-appen till blå-miljön, vilket är inaktiv standardmiljön. 

1. Kontrollera att jobbet kördes, gå till dessa URL: er:
    - Offentlig slutpunkt: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Blå slutpunkt- `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Grön slutpunkt- `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

Allmänheten och blå test-slutpunkter har samma uppdatering, medan grön slutpunkten visas tomcat standardavbildningen. 

Om du kör versionen mer än en gång, går den igenom blå och grön distributioner. Med andra ord, om den aktuella miljön är blå jobbet distribuerar och testar till grön-miljön. Sedan, om tester är bra, jobbet uppdaterar den offentliga slutpunkten för programmet att dirigera trafik till den gröna miljön.

## <a name="additional-information"></a>Ytterligare information

Mer information om stilleståndstid distribution finns i det här [snabbstartsmall](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment). 

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade i den här självstudien kan du ta bort dem.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>Felsökning

Om du stöter på buggar med Jenkins-plugin-programmet kan du rapportera problemet i [Jenkins JIRA](https://issues.jenkins-ci.org/) för en viss komponent.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att distribuera till AKS med hjälp av Jenkins och mönstret blå/grön distribution. Mer information om Azure Jenkins-providern finns Jenkins på Azure webbplats.

> [!div class="nextstepaction"]
> [Jenkins på Azure](/azure/jenkins/)
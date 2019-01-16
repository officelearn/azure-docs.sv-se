---
title: Distribuera till Azure Kubernetes Service (AKS) med hjälp av Jenkins och distributionsmönstret blå/grön
description: Lär dig att distribuera till Azure Kubernetes Service (AKS) med hjälp av Jenkins och distributionsmönstret blå/grön.
ms.service: jenkins
keywords: jenkins, azure, devops, kubernetes, k8s, aks, blue green deployment, kontinuerlig leverans, cd
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/11/2018
ms.openlocfilehash: 9bd601aee87ab0776069c80bfdeffb70b06c3c86
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073891"
---
# <a name="deploy-to-azure-kubernetes-service-aks-by-using-jenkins-and-the-bluegreen-deployment-pattern"></a>Distribuera till Azure Kubernetes Service (AKS) med hjälp av Jenkins och distributionsmönstret blå/grön

Azure Kubernetes Service (AKS) hanterar din värdmiljö för Kubernetes, vilket gör det enkelt att snabbt distribuera och hantera containerbaserade program. Du behöver inte ha expertkunskaper om behållarorkestrering. AKS eliminerar också problem med kontinuerliga åtgärder och underhåll genom att etablering, uppgradering och skalning av resurser på begäran. Du behöver inte ta dina program offline. Mer information om AKS finns i [AKS-dokumentationen](/azure/aks/).

Blå/grön distribution är ett mönster för kontinuerlig leverans för Azure DevOps förlitar sig på att hålla en befintlig (blå) version live, medan en ny (grön) distribueras. Det här mönstret används vanligtvis för belastningsutjämning för att dirigera ökade trafikmängder till grön distribution. Om övervakningen upptäcker en incident kan trafiken dirigeras om till den blå distributionen, som fortfarande är igång. Läs mer om kontinuerlig leverans i [Vad är Kontinuerlig leverans](/azure/devops/what-is-continuous-delivery).

I den här självstudien utför du följande åtgärder:

> [!div class="checklist"]
> * Läs om distributionsmönstret blå/grön
> * Skapa ett hanterat Kubernetes-kluster
> * Kör ett exempelskript för att konfigurera ett Kubernetes-kluster
> * Konfigurera ett Kubernetes-kluster manuellt
> * Skapa och kör ett Jenkins-jobb

## <a name="prerequisites"></a>Nödvändiga komponenter
- [GitHub-konto](https://github.com) : Du behöver ett GitHub-konto för att klona exempellagringsplatsen.
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) : Du använder Azure CLI 2.0 för att skapa Kubernetes-klustret.
- [Chocolatey](https://chocolatey.org): En pakethanterare som du använder för att installera kubectl.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/): Ett kommandoradsgränssnitt som du använder för att köra kommandon mot Kubernetes-kluster.
- [jq](https://stedolan.github.io/jq/download/): En enkel kommandoradsbaserad JSON-processor.

## <a name="clone-the-sample-app-from-github"></a>Klona exempelappen från GitHub

På Microsoft-lagringsplatsen i GitHub finns en exempelapp som visar hur du distribuerar till AKS med Jenkins och distributionsmönstret blå/grön. I det här avsnittet skapar du en förgrening av den här lagringsplatsen i din GitHub och klonar appen till ditt lokala system.

1. Bläddra till GitHub-lagringsplatsen för exempelappen [todo-app-java-on-azure](https://github.com/microsoft/todo-app-java-on-azure.git).

    ![Skärmbild av exempelappen på Microsoft GitHub-lagringsplatsen](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. Förgrena lagringsplatsen genom att välja **Förgrening** överst till höger på sidan, och följ anvisningarna för att förgrena lagringsplatsen i ditt GitHub-konto.

    ![Skärmbild av GitHub-alternativet för förgrening](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. När du har förgrenat lagringsplatsen ser du att kontonamnet ändras till namnet på ditt konto, och en kommentar anger var lagringsplatsen förgrenades från (Microsoft).

    ![Skärmbild av kontonamn och anteckning i GitHub](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Välj **Klona eller ladda ned**.

    ![Skärmbild av GitHub-alternativet för att klona eller ladda ned en lagringsplats](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. I fönstret **Klona med HTTPS** väljer du ikonen för **kopia**.

    ![Skärmbild av GitHub-alternativet för att kopiera klon-URL:en till Urklipp](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Öppna en terminal eller ett Git Bash-fönster.

1. Ändra sökväg till den plats där du vill lagra den lokala kopian (klonen) av lagringsplatsen.

1. Använd kommandot `git clone` och klona den URL som du nyss kopierade.

    ![Skärmbild av Git Bash-kommandot för git-kloning](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Tryck på RETUR för att påbörja kloningen.

    ![Skärmbild av resultat av Git Bash-kommandot för git-kloning](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Ändra sökvägen till den nyligen skapade katalog där klonen av app-källan finns.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Skapa och konfigurera ett hanterat Kubernetes-kluster

I det här avsnittet gör du följande:

- Använder Azure CLI 2.0 för att skapa ett hanterat Kubernetes-kluster.
- Lär dig att konfigurera ett kluster, antingen med hjälp av installationsskriptet eller manuellt.
- Skapar en instans av Azure Container Registry-tjänsten.

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Använder Azure CLI 2.0 för att skapa ett hanterat Kubernetes-kluster
Se till att du använder Azure CLI version 2.0.25 eller senare för att skapa ett hanterat Kubernetes-kluster med [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

1. Logga in på ditt Azure-konto. När du har angett följande kommando får du instruktioner som förklarar hur du slutför inloggningen. 
    
    ```bash
    az login
    ```

1. När du kör kommandot `az login` i föregående steg visas en lista över alla dina Azure-prenumerationer (tillsammans med tillhörande prenumerations-ID). I det här steget anger du Azure-standardprenumerationen. Byt ut platshållaren &lt;ditt-prenumerations-id > mot ID för önskad Azure-prenumeration. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. Skapa en resursgrupp. Byt ut platshållaren &lt;ditt-resursgruppsnamn> mot namnet på din nya resursgrupp, och byt ut platshållaren &lt;din-plats > mot platsen. Kommandot `az account list-locations` visar alla Azure-platser. I förhandsversionen AKS är inte alla platser tillgängliga. Om du anger en plats som inte är giltig just nu, visas ett felmeddelande med tillgängliga platser.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Skapa Kubernetes-klustret. Byt ut &lt;ditt-resursgruppnamn> mot namnet på resursgruppen som skapades i föregående steg, och byt ut &lt;ditt-kubernetes-klusternamn> mot namnet på det nya klustret. (Det kan ta flera minuter att slutföra.)

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Konfigurera Kubernetes-klustret

Du kan ställa in en blå/grön distribution i AKS manuellt eller med ett konfigurationsskript som finns i exemplet som klonades tidigare. I det här avsnittet visas hur du gör bådadera.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>Konfigurera Kubernetes-klustret via exempelskriptet för konfiguration
1. Redigera filen **deploy/aks/setup/setup.sh** och ersätta följande platshållare med lämpliga värden för din miljö: 

    - **&lt;ditt-resursgruppnamn>**
    - **&lt;ditt-kubernetes-klusternamn>**
    - **&lt;din-plats>**
    - **&lt;ditt-dns-namnssuffix>**

    ![Skärmbild av skriptet setup.sh i bash, med flera platshållare markerade](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. Kör konfigurationsskriptet.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Konfigurera ett Kubernetes-kluster manuellt 
1. Ladda ned Kubernetes-konfigurationen till din profilmapp.

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. Ändra sökvägen till katalogen **distribuera/aks/setup**. 

1. Kör följande **kubectl**-kommandon för att konfigurera tjänsterna för den offentliga slutpunkten och de två testslutpunkterna.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Uppdatera DNS-namnet för den offentliga slutpunkten och testslutpunkterna. När du skapar ett Kubernetes-kluster skapar du också en [ytterligare resursgrupp](https://github.com/Azure/AKS/issues/3) med namngivningsmönstret **MC_&lt;ditt-resursgruppnamn>_&lt; ditt-kubernetes-klusternamn>_&lt;din-plats>**.

    Leta rätt på de offentliga IP-adresserna i resursgruppen.

    ![Skärmbild av de offentliga IP-adresserna i resursgruppen](./media/jenkins-aks-blue-green-deployment/publicip.png)

    För varje tjänst letar du rätt på den externa IP-adressen genom att köra följande kommando:
    
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

    DNS-namnet måste vara unikt i din prenumeration. Du kan använda `<your-dns-name-suffix>` för att fastställa att det är unikt.

### <a name="create-an-instance-of-container-registry"></a>Skapa en Container Registry-instans

1. Kör kommandot `az acr create` för att skapa en Container Registry-instans. I nästa avsnitt kan du sedan använda `login server` som URL för Docker-registret.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Kör kommandot `az acr credential` för att visa dina autentiseringsuppgifter för Container Registry. Skriv ner användarnamn och lösenord för Docker-registret eftersom du behöver dem i nästa avsnitt.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Förbered Jenkins-servern

I det här avsnittet lär du dig att förbereda Jenkins-servern för kompileringskörning, vilket går bra under testning. Men du bör använda en [Azure VM-agent](https://plugins.jenkins.io/azure-vm-agents) eller [Azure Container-agent](https://plugins.jenkins.io/azure-container-agents) för att skapa en agent i Azure för körning av dina versioner. Mer information finns i Jenkins-artikeln om [säkerhetsriskerna med att kompilera på ett original](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

1. Distribuera ett [Jenkins-original i Azure](https://aka.ms/jenkins-on-azure).

1. Anslut till servern via SSH och installera kompileringsverktygen på den server där du kör din version.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Installera Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Kontrollera att användaren `jenkins` har behörighet att köra `docker`-kommandona.

1. [Installera kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [Ladda ned jq](https://stedolan.github.io/jq/download/).

1. Installera jq med följande kommando:

   ```bash
   sudo apt-get install jq
   ```
   
1. Installera plugin-programmen i Jenkins med följande steg på Jenkins-instrumentpanelen:

    1. Välj **Hantera Jenkins > Hantera plugin-program > Tillgängligt**.
    1. Leta reda på och installera plugin-programmet Azure Container Service.

1. Lägg till autentiseringsuppgifter för att hantera resurser i Azure. Om du inte redan har det installerar du plugin-programmet **Azure Credential**.

1. Lägg till dina autentiseringsuppgifter för Azure Service Principal som typen **Microsoft Azure Service Principal**.

1. Lägg till ditt användarnamn för Azure Docker-registret (som du fick i avsnittet ”Skapa en instans av Container Registry”) som typen **Användarnamn med lösenord**.

## <a name="edit-the-jenkinsfile"></a>Redigera Jenkins-filen

1. I din egen lagringsplats går du till `/deploy/aks/` och öppnar `Jenkinsfile`.

2. Uppdatera filen så här:

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    Uppdatera autentiseringsuppgifts-ID för Container Registry:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>Skapa jobbet
1. Lägg till ett nytt jobb i typen **Pipeline**.

1. Välj **Pipeline** > **Definition** > **Pipeline-skript från SCM**.

1. Ange URL-adressen för SCM-lagringsplatsen med din &lt;din-förgrenade-lagringsplats>.

1. Ange skriptets sökväg som `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>Kör jobbet

1. Kontrollera att du kan köra projektet i din lokala miljö. Så här gör du: [Kör projektet på den lokala datorn](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it).

1. Kör Jenkins-jobbet. Första gången du kör jobbet distribuerar Jenkins todo-appen till den blå miljön, det vill säga den inaktiva standardmiljön. 

1. Kontrollera att jobbet kördes genom att gå till de här webbadresserna:
    - Offentlig slutpunkt: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Blå slutpunkt – `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Grön slutpunkt – `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

Den offentliga slutpunkten och den blå testslutpunkten har samma uppdatering, medan den gröna slutpunkten visar tomcat-standardavbildningen. 

Om du kör kompileringen mer än en gång, går den igenom de blå och gröna distributionerna i tur och ordning. Om den aktuella miljön är blå, distribuerar alltså jobbet och testerna till den gröna miljön. Om testerna är bra uppdaterar jobbet den offentliga slutpunkten för programmet att dirigera trafik till den gröna miljön.

## <a name="additional-information"></a>Ytterligare information

Mer information om distribution utan stilleståndstid finns i det här [snabbstartsmallen](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment). 

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som skapades i den här självstudien kan du ta bort dem.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>Felsökning

Om du stöter på buggar med Jenkins-plugin-programmet kan du rapportera problemet i [Jenkins JIRA](https://issues.jenkins-ci.org/) för en viss komponent.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att distribuera till AKS med hjälp av Jenkins och distributionsmönstret blå/grön. Mer information om Azure Jenkins-providern finns på webbplatsen för Jenkins på Azure.

> [!div class="nextstepaction"]
> [Jenkins på Azure](/azure/jenkins/)
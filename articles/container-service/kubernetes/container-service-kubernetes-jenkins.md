---
title: Jenkins CI/CD med Kubernetes i Azure Container Service
description: "Automatisera en CI/CD-process med Jenkins att distribuera och uppgradera en av app på Kubernetes i Azure Container Service"
services: container-service
author: chzbrgr71
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/23/2017
ms.author: briar
ms.custom: mvc
ms.openlocfilehash: cd8f7ae584b6b1afd357cc585df28dedd3c1f70e
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="jenkins-integration-with-azure-container-service-and-kubernetes"></a>Jenkins integrering med Azure Container Service och Kubernetes 

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

I den här självstudiekursen kommer vi att gå igenom processen för att ställa in kontinuerlig integration av ett program för flera behållare i Azure Container Service Kubernetes med Jenkins platform. Arbetsflödet uppdateringar bilden behållare i Docker-hubb och uppgraderingar Kubernetes-skida med hjälp av en distribution för distribution. 

## <a name="high-level-process"></a>Hög nivå process
De grundläggande stegen som beskrivs i den här artikeln är: 
- Installera ett Kubernetes kluster i Container Service
- Konfigurera Jenkins och konfigurera åtkomst till Container Service
- Skapa ett arbetsflöde för Jenkins
- Testa processen CI/CD-slutpunkt till slutpunkt

## <a name="install-a-kubernetes-cluster"></a>Installera ett Kubernetes kluster
    
Distribuera Kubernetes kluster i Azure Container Service med följande steg. Fullständig dokumentation finns [här](container-service-kubernetes-walkthrough.md).

### <a name="step-1-create-a-resource-group"></a>Steg 1: Skapa en resursgrupp
```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus

az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="step-2-deploy-the-cluster"></a>Steg 2: Distribuera klustret
> [!NOTE]
> Följande steg krävs en lokal offentlig SSH-nyckel som lagras i mappen ~/.ssh.
>

```azurecli
RESOURCE_GROUP=my-resource-group
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name

az acs create \
--orchestrator-type=kubernetes \
--resource-group $RESOURCE_GROUP \
--name=$CLUSTER_NAME \
--dns-prefix=$DNS_PREFIX \
--ssh-key-value ~/.ssh/id_rsa.pub \
--admin-username=azureuser \
--master-count=1 \
--agent-count=5 \
--agent-vm-size=Standard_D1_v2
```

## <a name="set-up-jenkins-and-configure-access-to-container-service"></a>Konfigurera Jenkins och konfigurera åtkomst till Container Service

### <a name="step-1-install-jenkins"></a>Steg 1: Installera Jenkins
1. Skapa en virtuell dator i Azure med Ubuntu 16.04 LTS.  Eftersom senare i steg behöver du ansluta till den här virtuella datorn med hjälp av bash på den lokala datorn, ange ”autentiseringstyp” till SSH offentlig nyckel och klistra in den offentliga SSH-nyckeln som lagras lokalt i mappen ~/.ssh.  Dessutom anteckna 'användarnamn' som du anger sedan användarnamnet som behövs för att visa instrumentpanelen för Jenkins och för att ansluta till Jenkins VM i senare steg.
2. Installera Jenkins via dessa [instruktioner](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+on+Ubuntu). En mer detaljerad genomgång finns på [howtoforge.com](https://www.howtoforge.com/tutorial/how-to-install-jenkins-with-apache-on-ubuntu-16-04).
3. Om du vill visa infopanelen Jenkins på den lokala datorn, uppdatera säkerhetsgruppen Azure-nätverk för att tillåta port 8080 genom att lägga till en regel för inkommande trafik som tillåter åtkomst till port 8080.  Du kan också installera vidarebefordrade portar genom att köra det här kommandot:`ssh -i ~/.ssh/id_rsa -L 8080:localhost:8080 <your_jenkins_user>@<your_jenkins_public_ip`
4. Ansluta till servern Jenkins i webbläsaren genom att navigera till den offentliga IP-Adressen (http:// < your_jenkins_public_ip >: 8080) och låsa upp Jenkins instrumentpanelen för första gången med inledande administratörslösenordet.  Administratörslösenordet lagras på /var/lib/jenkins/secrets/initialAdminPassword på Jenkins VM.  Ett enkelt sätt att hämta lösenordet är att SSH till Jenkins VM: `ssh <your_jenkins_user>@<your_jenkins_public_ip>`.  Kör därefter: `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.
5. Installera Docker på Jenkins dator via dessa [instruktioner](https://docs.docker.com/cs-engine/1.13/#install-on-ubuntu-1404-lts-or-1604-lts). Detta ger Docker-kommandon för att köras i Jenkins jobb.
6. Konfigurera Docker-behörigheter för att tillåta Jenkins till Docker-slutpunkten.

    ```bash
    sudo chmod 777 /run/docker.sock
    ```
8. Installera `kubectl` CLI på Jenkins. Mer information finns på [installation och konfigurering av kubectl](https://kubernetes.io/docs/tasks/kubectl/install/).  Jenkins jobb använder 'kubectl' för att hantera och distribuera till Kubernetes klustret.

    ```bash
    curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

    chmod +x ./kubectl

    sudo mv ./kubectl /usr/local/bin/kubectl
    ```

### <a name="step-2-set-up-access-to-the-kubernetes-cluster"></a>Steg 2: Konfigurera åtkomst till klustret Kubernetes

> [!NOTE]
> Det finns flera sätt att utföra följande steg. Använd den metod som är enklast för dig.
>

1. Kopiera den `kubectl` config-filen till den Jenkins datorn så att Jenkins jobb har åtkomst till klustret Kubernetes. Dessa instruktioner förutsätter att du använder bash från en annan dator än Jenkins VM och en lokal offentlig SSH-nyckel som lagras i datorns ~/.ssh mapp.

```bash
export KUBE_MASTER=<your_cluster_master_fqdn>
export JENKINS_USER=<your_jenkins_user>
export JENKINS_SERVER=<your_jenkins_public_ip>
sudo ssh $JENKINS_USER@$JENKINS_SERVER sudo mkdir -m 777 /home/$JENKINS_USER/.kube/ \
&& sudo ssh $JENKINS_USER@$JENKINS_SERVER sudo mkdir /var/lib/jenkins/.kube/ \
&& sudo scp -3 -i ~/.ssh/id_rsa azureuser@$KUBE_MASTER:.kube/config $JENKINS_USER@$JENKINS_SERVER:~/.kube/config \
&& sudo ssh -i ~/.ssh/id_rsa $JENKINS_USER@$JENKINS_SERVER sudo cp /home/$JENKINS_USER/.kube/config /var/lib/jenkins/.kube/config \
```
        
2. Validera att klustret Kubernetes är tillgänglig från Jenkins.  Gör SSH till Jenkins VM: `ssh <your_jenkins_user>@<your_jenkins_public_ip>`.  Kontrollera Jenkins kan ansluta till klustret: `kubectl cluster-info`.
    

## <a name="create-a-jenkins-workflow"></a>Skapa ett arbetsflöde för Jenkins

### <a name="prerequisites"></a>Krav

- GitHub-konto för koden lagringsplatsen.
- Docker-hubb-konto för att lagra och uppdatera avbildningar.
- Av program som kan byggas och uppdateras. Du kan använda den här exempelappen behållare som skrivits i Golang: https://github.com/chzbrgr71/go-web 

> [!NOTE]
> Följande steg måste utföras i ditt GitHub-konto. Passa på att klona lagringsplatsen ovan men du måste använda ditt eget konto för att konfigurera webhooks och Jenkins komma åt.
>

### <a name="step-1-deploy-initial-v1-of-application"></a>Steg 1: Distribuera inledande v1 för program
1. Bygga appen från developer-dator med följande kommandon. Ersätt `myrepo` med dina egna.
    
    ```bash
    git clone https://github.com/chzbrgr71/go-web.git
    cd go-web
    docker build -t myrepo/go-web .
    ```

2. Push-avbildning till Docker-hubben.

    ```bash
    docker login
    docker push myrepo/go-web
    ```

3. Distribuera till Kubernetes klustret.
    
    > [!NOTE] 
    > Redigera den `go-web.yaml` fil att uppdatera behållaren avbildningen och lagringsplatsen.
    >
        
    ```bash
    kubectl create -f ./go-web.yaml --record
    ```
### <a name="step-2-configure-jenkins-system"></a>Steg 2: Konfigurera Jenkins system
1. Klicka på **hantera Jenkins** > **konfigurera systemet**.
2. Under **GitHub**väljer **Lägg till GitHub-Server**.
3. Lämna **API-URL** som standard.
4. Under **autentiseringsuppgifter**, lägga till en Jenkins autentiseringsuppgifter med hjälp av **hemliga text**. Vi rekommenderar att du använder GitHub personlig åtkomst-token har konfigurerats i din GitHub inställningar. Mer information om detta [här.](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)
5. Klicka på **Anslutningstestet** så konfigureras korrekt.
6. Under **globala egenskaper**, lägger du till ett miljövariabel `DOCKER_HUB` och ange lösenordet Docker-hubb. (Detta är användbart i den här demon, men en form av produktionsscenario kräver ett säkrare sätt.)
7. Spara.

![Jenkins GitHub-åtkomst](./media/container-service-kubernetes-jenkins/jenkins-github-access.png)

### <a name="step-3-create-the-jenkins-workflow"></a>Steg 3: Skapa Jenkins-arbetsflöde
1. Skapa ett Jenkins-objekt.
2. Ange ett namn (till exempel ”gå web”) och välj **Freestyle projekt**. 
3. Kontrollera **GitHub projekt** och ange en URL till dina GitHub-lagringsplatsen.
4. I **källa kod Management**, ange GitHub-repo-URL och autentiseringsuppgifter. 
5. Lägg till en **skapa steg** av typen **köra shell** och Använd följande text:

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME .
    docker login -u <your-dockerhub-username> -p ${DOCKER_HUB}
    docker push $WEB_IMAGE_NAME
    ```

6. Lägga till en annan **skapa steg** av typen **köra shell** och Använd följande text:

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    kubectl set image deployment/go-web go-web=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

![Jenkins skapa steg](./media/container-service-kubernetes-jenkins/jenkins-build-steps.png)
    
7. Spara objektet Jenkins och testa med **skapa nu**.

### <a name="step-4-connect-github-webhook"></a>Steg 4: Anslut GitHub-webhook
1. Klicka på det Jenkins-objekt som du skapade **konfigurera**.
2. Under **Skapa utlösare**väljer **GitHub hook utlösare för GITScm avsökning** och **spara**. GitHub-webhook konfigureras automatiskt.
3. Klicka på ditt GitHub-lagringsplatsen för gå webb **Inställningar > Webhooks**.
4. Kontrollera att Webhooksadressen Jenkins lades till. URL: en måste sluta med ”github-webhook”.

![Jenkins webhook-konfiguration](./media/container-service-kubernetes-jenkins/jenkins-webhook.png)

## <a name="test-the-cicd-process-end-to-end"></a>Testa processen CI/CD-slutpunkt till slutpunkt

1. Uppdatera koden för lagringsplatsen och push-synk med GitHub-lagringsplatsen.
2. Från konsolen Jenkins Kontrollera den **skapa historik** och verifiera att jobbet har körts. Visa konsolen utdata visas detaljerad information.
3. Kubernetes, se information om den uppgraderade distributionen:

    ```bash
    kubectl rollout history deployment/go-web
    ```

## <a name="next-steps"></a>Nästa steg

- Distribuera Azure-behållare registret och lagra avbildningar i en säker databas. Se [Azure Container registret docs](https://docs.microsoft.com/azure/container-registry).
- Skapa en mer komplex arbetsflöde som innehåller sida-vid-sida-distribution och testerna i Jenkins.
- Mer information om CI/CD-skiva med Jenkins och Kubernetes finns i [Jenkins blogg](https://jenkins.io/blog/2015/07/24/integrating-kubernetes-and-jenkins/).

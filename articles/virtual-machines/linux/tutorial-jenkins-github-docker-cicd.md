---
title: Skapa en utveckling pipeline i Azure med Jenkins | Microsoft Docs
description: "Lär dig hur du skapar en Jenkins virtuell dator i Azure som tar emot från GitHub på varje kod genomförande och skapar en ny dockerbehållare om du vill köra appen"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 1426b7331b320397184805a6642fe6a57ca6ccb1
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="how-to-create-a-development-infrastructure-on-a-linux-vm-in-azure-with-jenkins-github-and-docker"></a>Så här skapar du en infrastruktur för utveckling på en Linux-VM i Azure med Jenkins, GitHub och Docker
Du kan använda en kontinuerlig integrering och distribution (CI/CD) pipeline för att automatisera fasen bygg- och för programutveckling. I den här självstudiekursen skapar du en CI/CD-pipeline på en Azure VM att:

> [!div class="checklist"]
> * Skapa en virtuell dator Jenkins
> * Installera och konfigurera Jenkins
> * Skapa webhook integrering mellan GitHub och Jenkins
> * Skapa och genomför utlösaren Jenkins skapa jobb från GitHub
> * Skapa en Docker-avbildning för din app
> * Kontrollera GitHub incheckningar Skapa ny Docker-avbildning och uppdateringar som kör appen


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.22 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-jenkins-instance"></a>Skapa en instans av Jenkins
I en tidigare självstudiekurs om [hur du anpassar en Linux-dator vid den första starten](tutorial-automate-vm-deployment.md), du lärt dig hur du automatiserar VM anpassning med molnet initiering. Den här kursen använder en moln-init-fil för att installera Jenkins och Docker på en virtuell dator. Jenkins är en populär öppen källkod automation-server som smidigt kan integreras med Azure för att aktivera kontinuerlig integration (KO) och kontinuerlig leverans (CD). Självstudier om hur du använder Jenkins finns i [Jenkins i Azure-hubb](https://docs.microsoft.com/azure/jenkins/).

Skapa en fil med namnet i din aktuella shell *moln-init-jenkins.txt* och klistra in följande konfiguration. Till exempel skapa filen i molnet Shell inte på den lokala datorn. Ange `sensible-editor cloud-init-jenkins.txt` att skapa filen och se en lista över tillgängliga redigerare. Se till att hela molnet init-filen har kopierats korrekt, särskilt den första raden:

```yaml
#cloud-config
package_upgrade: true
write_files:
  - path: /etc/systemd/system/docker.service.d/docker.conf
    content: |
      [Service]
        ExecStart=
        ExecStart=/usr/bin/dockerd
  - path: /etc/docker/daemon.json
    content: |
      {
        "hosts": ["fd://","tcp://127.0.0.1:2375"]
      }
runcmd:
  - wget -q -O - https://jenkins-ci.org/debian/jenkins-ci.org.key | apt-key add -
  - sh -c 'echo deb http://pkg.jenkins-ci.org/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  - apt-get update && apt-get install jenkins -y
  - curl -sSL https://get.docker.com/ | sh
  - usermod -aG docker azureuser
  - usermod -aG docker jenkins
  - service jenkins restart
```

Innan du kan skapa en virtuell dator, skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#create). I följande exempel skapas en resursgrupp med namnet *myResourceGroupJenkins* i den *eastus* plats:

```azurecli-interactive 
az group create --name myResourceGroupJenkins --location eastus
```

Nu skapa en virtuell dator med [az vm skapa](/cli/azure/vm#create). Använd den `--custom-data` parametern för att skicka in din moln-init-konfigurationsfilen. Ange den fullständiga sökvägen till *moln-init-jenkins.txt* om du har sparat filen utanför arbetskatalogen finns.

```azurecli-interactive 
az vm create --resource-group myResourceGroupJenkins \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
```

Det tar några minuter för den virtuella datorn skapas och konfigureras.

För att Internet-trafik till den virtuella datorn ska använda [az vm öppna port](/cli/azure/vm#open-port) öppna port *8080* för Jenkins trafik och port *1337* för Node.js-app som används för att köra en exempelapp:

```azurecli-interactive 
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 8080 --priority 1001
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 1337 --priority 1002
```


## <a name="configure-jenkins"></a>Konfigurera Jenkins
Hämta den offentliga IP-adressen på den virtuella datorn för att komma åt din Jenkins-instans:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Du måste ange inledande administratörens som lagras i en textfil på den virtuella datorn att starta Jenkins installationen av säkerhetsskäl. Använd den offentliga IP-adressen som hämtades i föregående steg till SSH till den virtuella datorn:

```bash
ssh azureuser@<publicIps>
```

Visa den `initialAdminPassword` för din Jenkins installera och kopiera den:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Om filen är inte tillgängligt ännu, Vänta några minuter för moln-init att slutföra installationen Jenkins och Docker.

Öppna en webbläsare och gå till `http://<publicIps>:8080`. Slutför det inledande Jenkins på följande sätt:

- Ange användarnamnet **admin**, ange den *initialAdminPassword* hämtas från den virtuella datorn i föregående steg.
- Välj **hantera Jenkins**, sedan **hantera plugin-program**.
- Välj **tillgänglig**, Sök sedan efter *GitHub* i textrutan längst upp. Markera kryssrutan för *GitHub-plugin-programmet*och välj **nu hämta och installera efter omstart**.
- Markera kryssrutan för **starta om Jenkins när installationen är klar och inga jobb körs**, och sedan vänta tills plugin-programmet installera processen är klar.


## <a name="create-github-webhook"></a>Skapa GitHub-webhook
För att konfigurera integrationen med GitHub, öppna den [Node.js Hello World exempelapp](https://github.com/Azure-Samples/nodejs-docs-hello-world) från lagringsplatsen för Azure-exempel. Om du vill duplicera lagringsplatsen till ditt GitHub-konto, Välj den **Återställningsförgreningar** -knappen i det övre högra hörnet.

Skapa en webhook inuti förgrening som du skapade:

- Välj **inställningar**och välj **integreringar & tjänster** på vänster sida.
- Välj **lägga till tjänst**, ange *Jenkins* i filterfältet.
- Välj *Jenkins (GitHub-plugin-programmet)*
- För den **Jenkins koppla URL**, ange `http://<publicIps>:8080/github-webhook/`. Kontrollera att du inkluderar avslutande /
- Välj **lägga till tjänst**

![Lägg till GitHub-webhook till din andelen vridvuxna lagringsplatsen](media/tutorial-jenkins-github-docker-cicd/github_webhook.png)


## <a name="create-jenkins-job"></a>Skapa Jenkins jobb
Om du vill att Jenkins svara på en händelse i GitHub, till exempel när koden, skapar du ett Jenkins-jobb. 

Välj i webbplatsen Jenkins **skapa nya jobb** från startsidan:

- Ange *HelloWorld* som jobbnamn. Välj **Freestyle projektet**och välj **OK**.
- Under den **allmänna** väljer **GitHub** projektet och ange din andelen vridvuxna lagringsplatsen URL, exempelvis *https://github.com/iainfoulds/nodejs-docs-hello-world*
- Under den **datakällan kod management** väljer **Git**, ange din andelen vridvuxna lagringsplatsen *.git* -URL som *https://github.com/iainfoulds/nodejs-docs-hello-world.git*
- Under den **Skapa utlösare** väljer **GitHub hook utlösare för GITscm avsökning**.
- Under den **skapa** väljer **Lägg till build steg**. Välj **köra shell**, ange `echo "Testing"` i kommandofönstret.
- Välj **spara** längst ned i fönstret jobb.


## <a name="test-github-integration"></a>Testa GitHub-integrering
Om du vill testa GitHub-integrering med Jenkins, bekräfta att en ändring i din förgrening. 

Tillbaka i GitHub-webbgränssnitt, Välj din andelen vridvuxna lagringsplatsen och välj sedan den **index.js** fil. Välj pennikonen att redigera filen så läser rad 6:

```nodejs
response.end("Hello World!");
```

Om du vill spara ändringarna genom att markera den **genomför ändringar** längst ned.

I Jenkins, startar en ny version den **skapa historik** avsnittet längst ned till vänster på sidan jobb. Att välja länken för bygga och **konsolen utdata** på vänster sida. Du kan visa de steg Jenkins utförs som koden hämtas från GitHub och build-åtgärd matar ut meddelandet `Testing` till konsolen. Varje gång ett genomförande görs i GitHub webhooken når till Jenkins och utlöser en ny version på detta sätt.


## <a name="define-docker-build-image"></a>Definiera Docker build-bild
Se Node.js-app som körs baserat på ditt GitHub-incheckningar kan skapa en Docker-avbildning för att köra appen. Avbildningen skapas från en Dockerfile som definierar hur du konfigurerar den behållare som kör appen. 

Ändra till katalogen Jenkins arbetsytan med namnet efter jobb som du skapade i föregående steg från SSH-anslutningen till den virtuella datorn. I det här exemplet som kallades *HelloWorld*.

```bash
cd /var/lib/jenkins/workspace/HelloWorld
```

Skapa en fil i den här arbetsytan katalogen med `sudo sensible-editor Dockerfile` och klistra in följande innehåll. Se till att hela Dockerfile kopierats på rätt sätt, särskilt den första raden:

```yaml
FROM node:alpine

EXPOSE 1337

WORKDIR /var/www
COPY package.json /var/www/
RUN npm install
COPY index.js /var/www/
```

Denna Dockerfile använder Node.js basavbildningen med Alpine Linux, visar port 1337 som Hello World-appen körs på, sedan kopierar appfilerna och initierar den.


## <a name="create-jenkins-build-rules"></a>Skapa Jenkins build-regler
I föregående steg skapa Jenkins build regel som resulterar i ett meddelande till konsolen. Kan skapa build steg för att använda våra Dockerfile och köra appen.

Välj det jobb som du skapade i föregående steg tillbaka i din Jenkins-instans. Välj **konfigurera** på vänster sida och rulla ned till den **skapa** avsnitt:

- Ta bort den befintliga `echo "Test"` skapa steg. Markera rött kryss på det övre högra hörnet av den befintliga build steg rutan.
- Välj **Lägg till build steg**och välj **köra shell**
- I den **kommandot** rutan, Skriv följande kommandon för Docker och välj sedan **spara**:

  ```bash
  docker build --tag helloworld:$BUILD_NUMBER .
  docker stop helloworld && docker rm helloworld
  docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
  ```

Docker build steg skapa en avbildning och tagg den med Jenkins build-nummer så att du kan hantera en historik över bilder. Alla befintliga behållare som kör appen stoppas och sedan har tagits bort. En ny behållare har startats sedan med hjälp av avbildningen och kör Node.js-appen baserat på senaste incheckningar i GitHub.


## <a name="test-your-pipeline"></a>Testa din pipeline
Om du vill se hela pipeline i åtgärden Redigera den *index.js* filen i din GitHub-repo-andelen vridvuxna igen och välj **spara ändringen**. Ett nytt jobb startar i Jenkins baserat på webhooken för GitHub. Det tar några sekunder att skapa Docker-avbildning och starta appen i en ny behållare.

Hämta den offentliga IP-adressen på den virtuella datorn igen om det behövs:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Öppna en webbläsare och ange `http://<publicIps>:1337`. Node.js-appen visas och visar de senaste incheckningar i din GitHub-förgrening på följande sätt:

![Node.js-app som körs](media/tutorial-jenkins-github-docker-cicd/running_nodejs_app.png)

Nu göra en annan redigeringen av den *index.js* filen i GitHub och tillämpa ändringen. Vänta några sekunder för jobbet skulle bli klart i Jenkins och sedan uppdatera webbläsaren om du vill visa den uppdaterade versionen av appen körs i en ny behållare på följande sätt:

![Node.js-app som körs efter en annan GitHub genomförande](media/tutorial-jenkins-github-docker-cicd/another_running_nodejs_app.png)


## <a name="next-steps"></a>Nästa steg
Du har konfigurerat GitHub för att köra ett Jenkins build-jobb på varje kod genomförande och sedan distribuera en dockerbehållare för att testa din app i den här självstudiekursen. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en virtuell dator Jenkins
> * Installera och konfigurera Jenkins
> * Skapa webhook integrering mellan GitHub och Jenkins
> * Skapa och genomför utlösaren Jenkins skapa jobb från GitHub
> * Skapa en Docker-avbildning för din app
> * Kontrollera GitHub incheckningar Skapa ny Docker-avbildning och uppdateringar som kör appen

Gå vidare till nästa kurs lära dig mer om hur du integrerar Jenkins med Visual Studio Team Services.

> [!div class="nextstepaction"]
> [Distribuera appar med Jenkins och Team Services](tutorial-build-deploy-jenkins.md)

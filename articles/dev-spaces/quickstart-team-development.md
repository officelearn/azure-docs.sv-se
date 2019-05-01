---
title: Grupputveckling på Kubernetes med Azure Dev blanksteg
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 04/25/2019
ms.topic: quickstart
description: Kubernetes-utveckling i grupp med behållare och mikrotjänster på Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, tjänsten nät, tjänsten nät routning, kubectl, k8s
manager: jeconnoc
ms.openlocfilehash: 1414cc56809263a90a4bfea16db332e2f6a5e0bc
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/25/2019
ms.locfileid: "64514084"
---
# <a name="quickstart-team-development-on-kubernetes-using-azure-dev-spaces"></a>Snabbstart: Grupputveckling på Kubernetes med Azure Dev blanksteg

I den här guiden får du lära dig hur du:

- Konfigurera Azure Dev blanksteg på en hanterad Kubernetes-kluster i Azure.
- Distribuera ett stort program med flera mikrotjänster en dev-adressutrymme.
- Testa en enda mikrotjänst i en isolerad dev utrymme inom ramen för den kompletta appen.

![Azure Dev blanksteg cykel delning exempelprogrammet](media/quickstart-team-development/bikeshare.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

- En Azure-prenumeration. Om du inte har en Azure-prenumeration, kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
- [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Helm 2,13 eller senare installerat](https://github.com/helm/helm/blob/master/docs/install.md).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes Service-kluster

Du måste skapa ett AKS-kluster i en [region som stöds](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams). Den kommandona nedan skapar du en resursgrupp med namnet *MyResourceGroup* och ett AKS-kluster som heter *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
```

Den *MyAKS* kluster skapas också med en nod med hjälp av den *Standard_DS2_v2* storlek, och med RBAC inaktiverats.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Aktivera Azure Dev blanksteg på AKS-kluster

Använd den `use-dev-spaces` kommando för att aktivera Dev blanksteg på AKS-klustret och följa anvisningarna. I nedanstående kommando aktiverar Dev blanksteg på den *MyAKS* -kluster i den *MyResourceGroup* gruppen och skapar ett utrymme för utveckling som kallas *dev*.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Exempelkod för program

I den här artikeln använder du den [Azure Dev blanksteg cykel delning exempelprogrammet](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) att visa hur man använder Azure Dev blanksteg.

Klona programmet från GitHub och gå till programmets katalog:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Hämta HostSuffix för *utveckling*

Använd den `azds show-context` kommando för att visa HostSuffix för *dev*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Uppdatera Helm-diagrammet med din HostSuffix

Öppna [charts/values.yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) och Ersätt alla förekomster av `<REPLACE_ME_WITH_HOST_SUFFIX>` med HostSuffix-värde som du hämtade tidigare. Spara ändringarna och stäng filen.

## <a name="run-the-sample-application-in-kubernetes"></a>Kör exempelprogrammet i Kubernetes

Kommandon för att köra exempelprogrammet på Kubernetes är en del av en befintlig process och har inget beroende på Azure Dev blanksteg verktyg. I det här fallet Helm är verktyg som används för att köra det här exempelprogrammet, men andra verktyg kan användas för att köra hela programmet i ett namnområde i ett kluster. Kommandona Helm riktar in sig på området utveckling med namnet *dev* du skapade tidigare, men här dev är också ett Kubernetes-namnområde. Därför kan dev blanksteg riktas av andra verktyg på samma sätt som andra namnområden.

Du kan använda Azure Dev blanksteg för utveckling i grupp när ett program körs i ett kluster, oavsett de verktyg som används för att distribuera den.

Använd den `helm init` och `helm install` kommandon för att konfigurera och installera exempelprogrammet i klustret.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic --wait
```

Den `helm install` kommandot kan ta flera minuter att slutföra. Utdata från kommandot visar status för alla tjänster som den distribueras till klustret när du är klar:

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic --wait

Hang tight while we grab the latest from your chart repositories...
...
NAME               READY  UP-TO-DATE  AVAILABLE  AGE
bikes              1/1    1           1          4m32s
bikesharingweb     1/1    1           1          4m32s
billing            1/1    1           1          4m32s
gateway            1/1    1           1          4m32s
reservation        1/1    1           1          4m32s
reservationengine  1/1    1           1          4m32s
users              1/1    1           1          4m32s
```

Exemplet programmet är installerat på ditt kluster och eftersom du har Dev blanksteg aktiverad på ditt kluster kan använda den `azds list-uris` kommando för att visa URL: er för exempelprogrammet i *dev* som för närvarande är markerad.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Navigera till den *bikesharingweb* tjänsten genom att öppna en offentlig URL från den `azds list-uris` kommando. I exemplet ovan, en offentlig URL för den *bikesharingweb* tjänsten är *http://dev.bikesharingweb.fedcab0987.eus.azds.io/*. Välj *Aurelia Briggs (kund)* som användaren. Kontrollera att du ser texten *Hej Aurelia Briggs | Logga ut* högst upp.

## <a name="create-child-dev-spaces"></a>Skapa underordnade dev blanksteg

Använd den `azds space select` kommando för att skapa två underordnade blanksteg under *dev*:

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

Ovanstående kommandon skapar två underordnade blanksteg under *dev* med namnet *azureuser1* och *azureuser2*. Dessa två underordnade blanksteg representerar olika dev blanksteg för utvecklare *azureuser1* och *azureuser2* ska användas för att göra ändringar i exempelprogrammet.

Använd den `azds space list` kommando för att ange dev-adressutrymmen och bekräfta *dev/azureuser2* har valts.

```cmd
$ azds space list
Name            Selected
--------------  --------
default         False
dev             False
dev/azureuser1  False
dev/azureuser2  True
```

Använd den `azds list-uris` att visa URL: er för exempelprogrammet i den markerade adressutrymmet som är *dev/azureuser2*.

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Kontrollera att URL: er som visas av den `azds list-uris` kommando har den *azureuser2.s.dev* prefix. Det här prefixet bekräftar att det utrymme som valts är *azureuser2*, vilket är en underordnad *dev*.

Navigera till den *bikesharingweb* för den *dev/azureuser2* dev utrymme genom att öppna en offentlig URL från den `azds list-uris` kommando. I exemplet ovan, en offentlig URL för den *bikesharingweb* tjänsten är *http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/*. Välj *Aurelia Briggs (kund)* som användaren. Kontrollera att du ser texten *Hej Aurelia Briggs | Logga ut* högst upp.

## <a name="update-code"></a>Uppdatera kod

Öppna *BikeSharingWeb/components/Header.js* med en textredigerare och ändra texten i den [span element med den `userSignOut` className](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16).

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Spara ändringarna och stäng filen.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>Skapa och köra tjänsten uppdaterade bikesharingweb den *dev/azureuser2* dev utrymme

Navigera till den *BikeSharingWeb /* katalogen och kör den `azds up` kommando.

```cmd
$ cd ../BikeSharingWeb/
$ azds up

Using dev space 'dev/azureuser2' with target 'MyAKS'
Synchronizing files...2s
...
Service 'bikesharingweb' port 'http' is available at http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/
Service 'bikesharingweb' port 80 (http) is available at http://localhost:54256
...
```

Det här kommandot bygger och kör den *bikesharingweb* tjänsten på den *dev/azureuser2* dev utrymme. Den här tjänsten körs förutom den *bikesharingweb* tjänsten som körs i *dev* och används endast för begäranden med den *azureuser2.s* URL-prefix. Läs mer om hur routning fungerar mellan överordnade och underordnade dev blanksteg, [hur Azure Dev blanksteg fungerar och är konfigurerad](how-dev-spaces-works.md).

Navigera till den *bikesharingweb* för den *dev/azureuser2* dev utrymme genom att öppna en offentlig URL som visas i utdata från den `azds up` kommando. Välj *Aurelia Briggs (kund)* som användaren. Kontrollera att du ser uppdaterade texten i det övre högra hörnet. Du kan behöva uppdatera sidan eller rensa webbläsarens cacheminne om du inte omedelbart ser den här ändringen.

![Azure Dev blanksteg cykel delning exempelprogrammet uppdateras](media/quickstart-team-development/bikeshare-update.png)

## <a name="verify-other-dev-spaces-are-unchanged"></a>Kontrollera andra Dev blanksteg har inte ändrats

Om den `azds up` kommando körs genom att trycka på *Ctrl + c*.

```cmd
$ azds list-uris --all
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser1.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser1.s.dev.gateway.fedcab0987.eus.azds.io/         Available
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
http://dev.bikesharingweb.fedcab0987.eus.azds.io/               Available
http://dev.gateway.fedcab0987.eus.azds.io/                      Available
```

Navigera till den *dev* version av *bikesharingweb* i webbläsaren väljer *Aurelia Briggs (kund)* som användare, och kontrollera att du ser den ursprungliga texten i det övre högra hörnet hörnet. Upprepa dessa steg med den *dev/azureuser1* URL: en. Observera ändringarna tillämpas endast på den *dev/azureuser2* version av *bikesharingweb*. Denna isolering av ändringar av *dev/azureuser2* tillåter *azureuser2* göra ändringar utan att påverka *azureuser1*.

Att ha dessa ändringar återspeglas i *dev* och *dev/azureuser1*, bör du följa teamets befintliga arbetsflöden eller CI/CD-pipeline. Det här arbetsflödet kan exempelvis omfatta arrangerar din ändring din-versionens kontrollsystem och distribuera uppdateringen med hjälp av en CI/CD-pipeline eller verktyg, till exempel Helm.

## <a name="clean-up-your-azure-resources"></a>Rensa dina Azure-resurser

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur Azure Dev Spaces hjälper dig att utveckla mer komplexa appar över flera containrar och hur du kan förenkla utvecklingen i team genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Arbeta med flera containrar och utveckling i team](multi-service-nodejs.md)
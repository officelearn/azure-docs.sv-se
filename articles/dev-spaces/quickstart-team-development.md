---
title: Grupp utveckling på Kubernetes med hjälp av Azure dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 04/25/2019
ms.topic: quickstart
description: Team Kubernetes-utveckling med behållare och mikrotjänster på Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s
manager: gwallace
ms.openlocfilehash: f4a0a4858c890c94e3983c3dc9b10d739dc1352d
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772571"
---
# <a name="quickstart-team-development-on-kubernetes-using-azure-dev-spaces"></a>Snabbstart: Grupp utveckling på Kubernetes med hjälp av Azure dev Spaces

I den här guiden får du lära dig hur du:

- Konfigurera Azure dev Spaces i ett hanterat Kubernetes-kluster i Azure.
- Distribuera ett stort program med flera mikrotjänster till ett dev-utrymme.
- Testa en enskild mikrotjänst i ett isolerat dev-utrymme inom ramen för det fullständiga programmet.

![Grupp utveckling för Azure dev Spaces](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har en Azure-prenumeration, kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
- [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Helm 2,13 eller senare installerad](https://github.com/helm/helm/blob/master/docs/install.md).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes service-kluster

Du måste skapa ett AKS-kluster i en [region som stöds][supported-regions]. Kommandona nedan skapar en resurs grupp med namnet *MyResourceGroup* och ett AKS-kluster som kallas *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

*MyAKS* -klustret skapas också med en nod med hjälp av *Standard_DS2_v2* -storlek och med RBAC inaktiverat.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Aktivera Azure dev Spaces i ditt AKS-kluster

`use-dev-spaces` Använd kommandot för att aktivera dev Spaces på ditt AKS-kluster och följ anvisningarna. Kommandot nedan aktiverar dev Spaces i *MyAKS* -klustret i gruppen *MyResourceGroup* och skapar ett dev-utrymme som heter *dev*.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Hämta exempel program kod

I den här artikeln använder du [exempel programmet Azure dev Spaces Bike sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) för att demonstrera användningen av Azure dev Spaces.

Klona programmet från GitHub och gå till programmets katalog:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Hämta HostSuffix för *utveckling*

Använd kommandot för att Visa HostSuffix för *dev.* `azds show-context`

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Uppdatera Helm-diagrammet med din HostSuffix

Öppna [Charts/Values. yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) och Ersätt alla `<REPLACE_ME_WITH_HOST_SUFFIX>` instanser av med HostSuffix-värdet som du hämtade tidigare. Spara ändringarna och Stäng filen.

## <a name="run-the-sample-application-in-kubernetes"></a>Kör exempel programmet i Kubernetes

Kommandona för att köra exempel programmet på Kubernetes är en del av en befintlig process och har inget beroende av Azure dev Spaces-verktyg. I det här fallet är Helm det verktyg som används för att köra det här exempel programmet, men andra verktyg kan användas för att köra hela programmet i ett namn område i ett kluster. Helm-kommandona är riktade mot dev-utrymmet som heter *dev* som du skapade tidigare, men detta dev-utrymme är också ett Kubernetes-namnområde. Detta innebär att dev-rymder kan riktas mot andra verktyg på samma sätt som andra namn områden.

Du kan använda Azure dev Spaces för grupp utveckling när ett program körs i ett kluster oavsett vilka verktyg som används för att distribuera det.

`helm init` Använd och `helm install` -kommandona för att konfigurera och installera exempel programmet i klustret.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic 
```
> [!Note]
> **Om du använder ett RBAC-aktiverat kluster måste du**konfigurera [ett tjänst konto för till en](https://helm.sh/docs/using_helm/#role-based-access-control). Annars fungerar inte kommandona. `helm`

Det `helm install` kan ta flera minuter att slutföra kommandot. Kommandots utdata visar status för alla tjänster som distribuerats till klustret när de är slutförda:

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic

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

När exempel programmet har installerats på klustret och eftersom du har aktiverat dev Spaces i klustret, kan du använda `azds list-uris` kommandot för att visa URL: erna för exempel programmet i den valda *dev* -gruppen.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Navigera till *bikesharingweb* -tjänsten genom att öppna den offentliga URL: `azds list-uris` en från kommandot. I exemplet ovan är `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`den offentliga URL: en för *bikesharingweb* -tjänsten. Välj *Aurelia Briggs (kund)* som användare. Kontrol lera att du ser texten *Hi-Aurelia Briggs | Logga ut* överst.

![Exempel program för Azure dev Spaces-cykel delning](media/quickstart-team-development/bikeshare.png)

## <a name="create-child-dev-spaces"></a>Skapa underordnade dev-utrymmen

Använd kommandot för att skapa två underordnade utrymmen under *dev:* `azds space select`

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

Kommandona ovan skapar två underordnade utrymmen under *dev* med namnet *azureuser1* och *azureuser2*. Dessa två underordnade utrymmen representerar distinkta dev Spaces för utvecklarna " *azureuser1* och *azureuser2* " som ska användas för att göra ändringar i exempel programmet.

Använd kommandot för att lista alla dev Spaces och Confirm *dev/azureuser2* är markerat. `azds space list`

```cmd
$ azds space list
Name            Selected
--------------  --------
default         False
dev             False
dev/azureuser1  False
dev/azureuser2  True
```

Använd för att visa URL: erna för exempel programmet i det valda utrymmet som är *dev/azureuser2.* `azds list-uris`

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Bekräfta att URL: erna som visas `azds list-uris` med kommandot har *azureuser2. s. dev* -prefixet. Det här prefixet bekräftar att det aktuella utrymmet som valts är *azureuser2*, vilket är underordnat en *dev*.

Navigera till *bikesharingweb* -tjänsten för *dev/azureuser2* dev-utrymmet genom att öppna den offentliga `azds list-uris` URL: en från kommandot. I exemplet ovan är `http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/`den offentliga URL: en för *bikesharingweb* -tjänsten. Välj *Aurelia Briggs (kund)* som användare. Kontrol lera att du ser texten *Hi-Aurelia Briggs | Logga ut* överst.

## <a name="update-code"></a>Uppdatera kod

Öppna *BikeSharingWeb/Components/header. js* med en text redigerare och ändra texten i [span-elementet `userSignOut` med className](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16).

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Spara ändringarna och Stäng filen.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>Skapa och kör den uppdaterade bikesharingweb-tjänsten i dev */azureuser2* -dev-rymden

Navigera till *BikeSharingWeb/* Directory och kör `azds up` kommandot.

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

Det här kommandot skapar och kör *bikesharingweb* -tjänsten i dev */azureuser2 dev-* ytan. Den här tjänsten körs utöver *bikesharingweb* -tjänsten som körs i *dev* och används endast för begär Anden med URL-prefixet *azureuser2. s* . Mer information om hur routning fungerar mellan över-och underordnade dev-Spaces finns i [så här fungerar Azure dev Spaces och har kon figurer ATS](how-dev-spaces-works.md).

Navigera till *bikesharingweb* -tjänsten för *dev/azureuser2* dev-utrymmet genom att öppna den offentliga URL: en som visas i `azds up` kommandots utdata. Välj *Aurelia Briggs (kund)* som användare. Kontrol lera att den uppdaterade texten finns i det övre högra hörnet. Du kan behöva uppdatera sidan eller rensa webbläsarens cacheminne om du inte omedelbart ser den här ändringen.

![Azure dev Spaces cykel delning exempel program uppdaterat](media/quickstart-team-development/bikeshare-update.png)

## <a name="verify-other-dev-spaces-are-unchanged"></a>Kontrol lera att andra dev-rymder är oförändrade

Tryck på *CTRL + c*om kommandotfortfarandekörs.`azds up`

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

Navigera till *dev* -versionen av *bikesharingweb* i webbläsaren, Välj *Aurelia Briggs (kund)* som användare och kontrol lera att du ser den ursprungliga texten i det övre högra hörnet. Upprepa dessa steg med *utvecklings-/azureuser1-* URL: en. Observera att ändringarna endast tillämpas på *dev/azureuser2-* versionen av *bikesharingweb*. Den här isoleringen av förändringar i *dev/azureuser2* gör att *azureuser2* kan göra ändringar utan att påverka *azureuser1*.

Om du vill att dessa ändringar ska reflekteras i *utvecklings* *-och dev-/azureuser1*bör du följa teamets befintliga arbets flöde eller CI/CD-pipeline. Det här arbets flödet kan till exempel innebära att du genomför din ändring av versions kontroll systemet och distribuerar uppdateringen med hjälp av en CI/CD-pipeline eller verktyg som Helm.

## <a name="clean-up-your-azure-resources"></a>Rensa dina Azure-resurser

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur Azure Dev Spaces hjälper dig att utveckla mer komplexa appar över flera containrar och hur du kan förenkla utvecklingen i team genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Arbeta med flera containrar och utveckling i team](multi-service-nodejs.md)


[supported-regions]: about.md#supported-regions-and-configurations

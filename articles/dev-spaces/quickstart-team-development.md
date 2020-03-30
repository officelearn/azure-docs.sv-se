---
title: Teamutveckling på Kubernetes
services: azure-dev-spaces
ms.date: 01/22/2020
ms.topic: quickstart
description: Den här snabbstarten visar hur du gör team Kubernetes utveckling med behållare och mikrotjänster med Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, servicenät, routning av tjänstnät, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 0fe177db420913e5d68807dd803df791653c0914
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78244947"
---
# <a name="quickstart-team-development-on-kubernetes---azure-dev-spaces"></a>Snabbstart: Grupputveckling på Kubernetes - Azure Dev Spaces

I den här guiden får du lära dig hur du:

- Konfigurera Azure Dev Spaces på ett hanterat Kubernetes-kluster i Azure.
- Distribuera ett stort program med flera mikrotjänster till ett utvecklingsutrymme.
- Testa en enda mikrotjänst i ett isolerat utvecklingsutrymme inom ramen för det fullständiga programmet.

![Utveckling av Azure Dev Spaces-team](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
- [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Helm 3 installerat][helm-installed].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes-tjänstkluster

Du måste skapa ett AKS-kluster i en [region som stöds][supported-regions]. Kommandona nedan skapar en resursgrupp som heter *MyResourceGroup* och ett AKS-kluster som heter *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Aktivera Azure Dev Spaces i AKS-klustret

Använd `use-dev-spaces` kommandot för att aktivera dev spaces i AKS-klustret och följ anvisningarna. Kommandot nedan aktiverar Dev Spaces i *MyAKS-klustret* i gruppen *MyResourceGroup* och skapar ett dev-utrymme som kallas *dev*.

> [!NOTE]
> Kommandot `use-dev-spaces` kommer också att installera Azure Dev Spaces CLI om det inte redan är installerat. Du kan inte installera AZURE Dev Spaces CLI i Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Hämta exempelprogramkod

I den här artikeln använder du [exempelprogrammet Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) för att demonstrera med Azure Dev Spaces.

Klona programmet från GitHub och gå till programmets katalog:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Hämta HostSuffix för *utveckling*

Använd `azds show-context` kommandot för att visa HostSuffix för *dev*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Uppdatera Helm-diagrammet med ditt HostSuffix

Öppna [diagram/values.yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) och ersätt `<REPLACE_ME_WITH_HOST_SUFFIX>` alla förekomster av med värdet HostSuffix som du hämtade tidigare. Spara ändringarna och stäng filen.

## <a name="run-the-sample-application-in-kubernetes"></a>Kör exempelprogrammet i Kubernetes

Kommandona för att köra exempelprogrammet på Kubernetes är en del av en befintlig process och har inget beroende av Azure Dev Spaces-verktyg. I det här fallet är Helm det verktyg som används för att köra det här exempelprogrammet, men andra verktyg kan användas för att köra hela programmet i ett namnområde i ett kluster. Helm-kommandona riktar in sig på det dev-utrymme som heter *dev-området* som du skapade tidigare, men det här utvecklingsutrymmet är också ett Kubernetes-namnområde. Därför kan utvecklingsutrymmen vara inriktade på andra verktyg som är samma som andra namnområden.

Du kan använda Azure Dev Spaces för teamutveckling när ett program körs i ett kluster oavsett vilket verktyg som används för att distribuera det.

Använd `helm install` kommandot för att konfigurera och installera exempelprogrammet i klustret.

```cmd
cd charts/
helm install bikesharingsampleappsampleapp . --dependency-update --namespace dev --atomic
```

Kommandot `helm install` kan ta flera minuter att slutföra. När exempelprogrammet har installerats i klustret och eftersom du har `azds list-uris` dev spaces aktiverat i klustret använder du kommandot för att visa URL:erna för exempelprogrammet i *dev* som för närvarande är markerat.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Navigera till *bikesharingweb-tjänsten* genom att `azds list-uris` öppna den offentliga webbadressen från kommandot. I exemplet ovan är `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`den offentliga URL:en för *bikesharingweb-tjänsten* . Välj *Aurelia Briggs (kund)* som användare. Kontrollera att du ser texten *Hej Aurelia Briggs | Logga ut* högst upp.

![Exempelprogram för azure dev spaces-cykeldelning](media/quickstart-team-development/bikeshare.png)

## <a name="create-child-dev-spaces"></a>Skapa underordnade utvecklingsutrymmen

Använd `azds space select` kommandot för att skapa två underordnade blanksteg under *dev:*

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

Ovanstående kommandon skapar två underordnade utrymmen under *dev* med namnet *azureuser1* och *azureuser2*. Dessa två underordnade utrymmen representerar olika utvecklingsutrymmen för utvecklare *azureuser1* och *azureuser2* att använda för att göra ändringar i exempelprogrammet.

Använd `azds space list` kommandot för att lista alla dev spaces och bekräfta *dev/azureuser2* har valts.

```cmd
$ azds space list
   Name            DevSpacesEnabled
-  --------------  ----------------
   default         False
   dev             True
   dev/azureuser1  True
*  dev/azureuser2  True
```

Använd `azds list-uris` för att visa URL:er för exempelprogrammet i det markerade utrymmet som är *dev/azureuser2*.

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Bekräfta att url:erna `azds list-uris` som visas av kommandot har prefixet *azureuser2.s.dev.* Det här prefixet bekräftar att det valda aktuella utrymmet är *azureuser2*, vilket är underordnad *dev*.

Navigera till *bikesharingweb-tjänsten* för *dev/azureuser2-utvecklingsutrymmet* genom `azds list-uris` att öppna den offentliga URL:en från kommandot. I exemplet ovan är `http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/`den offentliga URL:en för *bikesharingweb-tjänsten* . Välj *Aurelia Briggs (kund)* som användare. Kontrollera att du ser texten *Hej Aurelia Briggs | Logga ut* högst upp.

## <a name="update-code"></a>Uppdatera kod

Öppna *BikeSharingWeb/components/Header.js* med en textredigerare och ändra texten i [span-elementet med `userSignOut` className](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16).

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Spara ändringarna och stäng filen.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>Skapa och kör den uppdaterade bikesharingweb-tjänsten i *dev/azureuser2* dev space

Navigera till *bikesharingweb/-katalogen* `azds up` och kör kommandot.

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

Det här kommandot skapar och kör *bikesharingweb-tjänsten* i *dev/azureuser2* dev space. Den här tjänsten körs utöver *bikesharingweb-tjänsten* som körs i *dev* och används endast för begäranden med *azureuser2.s* URL-prefix. Mer information om hur routning fungerar mellan överordnade och underordnade utvecklingsutrymmen finns i [Så här fungerar Azure Dev Spaces och konfigureras](how-dev-spaces-works.md).

Navigera till *bikesharingweb-tjänsten* för *dev/azureuser2-utvecklingsutrymmet* genom att öppna `azds up` den offentliga URL:en som visas i kommandots utdata. Välj *Aurelia Briggs (kund)* som användare. Kontrollera att du ser den uppdaterade texten i det övre högra hörnet. Du kan behöva uppdatera sidan eller rensa webbläsarens cacheminne om du inte omedelbart ser den här ändringen.

![Exempelprogrammet Azure Dev Spaces Bike Sharing har uppdaterats](media/quickstart-team-development/bikeshare-update.png)

> [!NOTE]
> När du navigerar till `azds up`tjänsten när du kör visas http-begäran `azds up` spårningar också i utdata från kommandot. Dessa spår kan hjälpa dig att felsöka och felsöka din tjänst. Du kan inaktivera dessa `--disable-http-traces` spårningar när du kör `azds up`.

## <a name="verify-other-dev-spaces-are-unchanged"></a>Kontrollera att andra dev spaces är oförändrade

Om `azds up` kommandot fortfarande körs trycker du på *Ctrl+c*.

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

Navigera till *dev-versionen* av *bikesharingweb* i din webbläsare, välj *Aurelia Briggs (kund)* som användare och kontrollera att du ser den ursprungliga texten i det övre högra hörnet. Upprepa dessa steg med *dev/azureuser1-URL:en.* Observera att ändringarna endast tillämpas på *dev/azureuser2-versionen* av *bikesharingweb*. Den här isoleringen av ändringar *i dev/azureuser2* gör det möjligt för *azureuser2* att göra ändringar utan att påverka *azureuser1*.

Om du vill att dessa ändringar ska återspeglas i *dev* och *dev/azureuser1*bör du följa teamets befintliga arbetsflöde eller CI/CD-pipeline. Det här arbetsflödet kan till exempel innebära att du ändrar din versionskontrollsystem och distribuerar uppdateringen med hjälp av en CI/CD-pipeline eller verktyg, till exempel Helm.

## <a name="clean-up-your-azure-resources"></a>Rensa dina Azure-resurser

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur Azure Dev Spaces hjälper dig att utveckla mer komplexa appar över flera containrar och hur du kan förenkla utvecklingen i team genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Arbeta med flera containrar och utveckling i team](multi-service-nodejs.md)

[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

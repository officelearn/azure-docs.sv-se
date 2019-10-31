---
title: GitHub åtgärder & Azure Kubernetes service
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 10/24/2019
ms.topic: conceptual
description: Granska och testa ändringar från en pull-begäran direkt i Azure Kubernetes-tjänsten med hjälp av GitHub-åtgärder och Azure dev Spaces.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, GitHub Actions, Helm, service nät, service mask-routning, kubectl, K8s
manager: gwallace
ms.openlocfilehash: 2638fe2cd12407e43d3b3b698cdfca5231362db4
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065943"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub-åtgärder & Azure Kubernetes service (för hands version)

Azure dev Spaces innehåller ett arbets flöde med GitHub-åtgärder som gör att du kan testa ändringar från en pull-begäran direkt i AKS innan pull-begäran slås samman i huvud grenen för din databas. Att ha ett program som körs för att granska ändringar av en pull-begäran kan öka säkerheten både för utvecklare och grupp medlemmar. Detta program som körs kan också hjälpa grupp medlemmar, till exempel produkt chefer och designers, att bli en del av gransknings processen under de första utvecklings faserna.

I den här guiden får du lära dig hur du:

- Konfigurera Azure dev Spaces i ett hanterat Kubernetes-kluster i Azure.
- Distribuera ett stort program med flera mikrotjänster till ett dev-utrymme.
- Konfigurera CI/CD med GitHub-åtgärder.
- Testa en enskild mikrotjänst i ett isolerat dev-utrymme inom ramen för det fullständiga programmet.

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
- [Azure CLI installerat][azure-cli-installed].
- [Helm 2,13 eller senare installerad][helm-installed].
- Ett GitHub-konto med [aktiverade GitHub-åtgärder][github-actions-beta-signup].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes service-kluster

Du måste skapa ett AKS-kluster i en [region som stöds][supported-regions]. Kommandona nedan skapar en resurs grupp med namnet *MyResourceGroup* och ett AKS-kluster som kallas *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Aktivera Azure dev Spaces i ditt AKS-kluster

Använd kommandot `use-dev-spaces` för att aktivera dev Spaces i ditt AKS-kluster och följa anvisningarna. Kommandot nedan aktiverar dev Spaces i *MyAKS* -klustret i gruppen *MyResourceGroup* och skapar ett dev-utrymme som heter *dev*.

> [!NOTE]
> Kommandot `use-dev-spaces` installerar även Azure dev Spaces CLI om det inte redan är installerat. Det går inte att installera Azure dev Spaces CLI i Azure Cloud Shell.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry

Skapa en Azure Container Registry (ACR):

```cmd
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Namnet ACR måste vara unikt i Azure och innehålla 5-50 alfanumeriska tecken. Alla bokstäver som du använder måste vara gemena.

Spara *namnet* -värdet från utdata eftersom det används i ett senare steg.

## <a name="create-a-service-principal-for-authentication"></a>Skapa ett huvud namn för tjänsten för autentisering

Använd [AZ AD SP Create-for-RBAC][az-ad-sp-create-for-rbac] för att skapa ett huvud namn för tjänsten. Exempel:

```cmd
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Spara JSON-utdata eftersom det används i ett senare steg.


Använd [AZ AKS show][az-aks-show] för att visa *ID: t* för ditt AKS-kluster:

```cmd
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Använd [AZ ACR show][az-acr-show] för att visa *ID: t* för ACR:

```cmd
az acr show --name <acrName> --query id
```

Använd [AZ roll tilldelning skapa][az-role-assignment-create] för att ge *deltagar* åtkomst till ditt AKS-kluster och *AcrPush* åtkomst till din ACR.

```cmd
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Du måste vara ägare till både ditt AKS-kluster och ACR för att ge tjänstens huvud namn åtkomst till dessa resurser.

## <a name="get-sample-application-code"></a>Hämta exempel program kod

I den här artikeln använder du [exempel programmet Azure dev Spaces Bike sharing][bike-sharing-gh] för att demonstrera användningen av Azure dev Spaces med GitHub-åtgärder.

Förgrening i exempel databasen för Azure dev Spaces och navigera sedan till din förgrenade lagrings plats. Klicka på fliken *åtgärder* och välj att aktivera åtgärder för den här lagrings platsen.

Klona din förgrenade lagrings plats och navigera till katalogen:

```cmd
git clone https://github.com/USERNAME/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Hämta HostSuffix för *utveckling*

Använd kommandot `azds show-context` för att Visa HostSuffix för *dev*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Uppdatera Helm-diagrammet med din HostSuffix

Öppna [Charts/Values. yaml][bike-sharing-values-yaml] och Ersätt alla instanser av `<REPLACE_ME_WITH_HOST_SUFFIX>` med HostSuffix-värdet som du hämtade tidigare. Spara ändringarna och Stäng filen.

## <a name="run-the-sample-application-in-kubernetes"></a>Kör exempel programmet i Kubernetes

Kommandona för att köra exempel programmet på Kubernetes är en del av en befintlig process och har inget beroende av Azure dev Spaces-verktyg. I det här fallet är Helm det verktyg som används för att köra det här exempel programmet, men andra verktyg kan användas för att köra hela programmet i ett namn område i ett kluster. Helm-kommandona är riktade mot dev-utrymmet som heter *dev* som du skapade tidigare, men detta dev-utrymme är också ett Kubernetes-namnområde. Detta innebär att dev-rymder kan riktas mot andra verktyg på samma sätt som andra namn områden.

Du kan använda Azure dev Spaces för utveckling när ett program körs i ett kluster oavsett vilka verktyg som används för att distribuera det.

Använd kommandona `helm init` och `helm install` för att konfigurera och installera exempel programmet i klustret.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

> [!Note]
> **Om du använder ett RBAC-aktiverat kluster måste du**konfigurera [ett tjänst konto för till en][tiller-rbac]. Annars fungerar inte `helm` kommandon.

Det kan ta flera minuter att slutföra kommandot `helm install`. Kommandots utdata visar status för alla tjänster som distribuerats till klustret när de är slutförda:

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

När exempel programmet har installerats på klustret och eftersom du har aktiverat dev Spaces i klustret, använder du kommandot `azds list-uris` för att visa URL: erna för exempel programmet i den aktuella *utvecklings* gruppen.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Navigera till *bikesharingweb* -tjänsten genom att öppna den offentliga URL: en från kommandot `azds list-uris`. I exemplet ovan är den offentliga URL: en för *bikesharingweb* -tjänsten `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Välj *Aurelia Briggs (kund)* som användare och välj sedan en cykel att hyra. Kontrol lera att du ser en platshållarbild för cykeln.

## <a name="configure-your-github-action"></a>Konfigurera din GitHub-åtgärd

Navigera till din förgrenade lagrings plats och klicka på *Inställningar*. Klicka på *hemligheter* i den vänstra panelen. Klicka på *Lägg till en ny hemlighet* för att lägga till varje ny hemlighet nedan:

1. *AZURE_CREDENTIALS*: hela utdata från tjänstens huvud namn skapas.
1. *RESOURCE_GROUP*: resurs gruppen för ditt AKS-kluster, som i det här exemplet är *MyResourceGroup*.
1. *CLUSTER_NAME*: namnet på ditt AKS-kluster, som i det här exemplet är *MyAKS*.
1. *CONTAINER_REGISTRY*: *namnet* för ACR.
1. *Värd*: värden för ditt dev Space, som tar formuläret *< MASTER_SPACE >. < APP_NAME >. < HOST_SUFFIX >* , som i det här exemplet är *dev.bikesharingweb.fedcab0987.EUs.azds.io*.
1. *IMAGE_PULL_SECRET*: namnet på hemligheten som du vill använda, till exempel *demo-Secret*.
1. *MASTER_SPACE*: namnet på det överordnade dev-utrymmet, som i det här exemplet är *dev*.
1. *REGISTRY_USERNAME*: *clientId* från JSON-utdata från det att tjänstens huvud namn skapas.
1. *REGISTRY_PASSWORD*: *clientSecret* från JSON-utdata från det att tjänstens huvud namn skapas.

> [!NOTE]
> Alla dessa hemligheter används av GitHub-åtgärden och konfigureras i [. GitHub/arbets flöden/Bikes. yml][github-action-yaml].

## <a name="create-a-new-branch-for-code-changes"></a>Skapa en ny gren för kod ändringar

Gå tillbaka till `BikeSharingApp/` och skapa en ny gren med namnet *Bikes-bilder*.

```cmd
cd ..
git checkout -b bike-images
```

Redigera [cyklar/Server. js][bikes-server-js] för att ta bort raderna 232 och 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Avsnittet bör nu se ut så här:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Spara filen och Använd sedan `git add` och `git commit` för att mellanlagra ändringarna.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Skicka dina ändringar

Använd `git push` för att skicka din nya gren till din förgrenade lagrings plats:

```cmd
git push origin bike-images
```

När push-åtgärden är klar navigerar du till din förgrenade lagrings plats på GitHub för att skapa en pull-begäran med *huvud* gruppen i det förgrenade lagrings lagret som bas gren jämfört med *cykel-image-* grenen.

När din pull-begäran har öppnats navigerar du till fliken *åtgärder* . kontrol lera att en ny åtgärd har startats och att tjänsten *Bikes* skapas.

## <a name="view-the-child-space-with-your-changes"></a>Visa det underordnade utrymmet med dina ändringar

När åtgärden har slutförts visas en kommentar med en URL till ditt nya underordnade utrymme baserat på ändringarna i pull-begäran.

> [!div class="mx-imgBorder"]
> ![åtgärds-URL för GitHub](../media/github-actions/github-action-url.png)

Navigera till *bikesharingweb* -tjänsten genom att öppna URL: en från kommentaren. Välj *Aurelia Briggs (kund)* som användare och välj sedan en cykel att hyra. Kontrol lera att du inte längre ser platshållarbilden för cykeln.

## <a name="clean-up-your-azure-resources"></a>Rensa dina Azure-resurser

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur Azure dev Spaces hjälper dig att utveckla mer komplexa program över flera behållare och hur du kan förenkla samarbets utveckling genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Grupp utveckling i Azure dev Spaces][team-quickstart]

[azure-cli-installed]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[tiller-rbac]: https://helm.sh/docs/using_helm/#role-based-access-control
[supported-regions]: ../about.md#supported-regions-and-configurations
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md
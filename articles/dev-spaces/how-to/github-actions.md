---
title: GitHub-åtgärder & Azure Kubernetes-tjänsten (förhandsversion)
services: azure-dev-spaces
ms.date: 02/04/2020
ms.topic: conceptual
description: Granska och testa ändringar från en pull-begäran direkt i Azure Kubernetes-tjänsten med GitHub-åtgärder och Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, GitHub-åtgärder, Helm, servicenät, routning av tjänstnät, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 49715e38f36d4421b7327640ec8392a83b3c2996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252380"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub-åtgärder & Azure Kubernetes-tjänsten (förhandsversion)

Azure Dev Spaces tillhandahåller ett arbetsflöde med GitHub-åtgärder som gör att du kan testa ändringar från en pull-begäran direkt i AKS innan pull-begäran slås samman till databasens huvudgren. Att ha ett program som körs för att granska ändringar av en pull-begäran kan öka förtroendet för både utvecklaren och gruppmedlemmarna. Detta program som körs kan också hjälpa gruppmedlemmar som, produktchefer och designers, att bli en del av granskningsprocessen under tidiga utvecklingsstadier.

I den här guiden får du lära dig hur du:

* Konfigurera Azure Dev Spaces på ett hanterat Kubernetes-kluster i Azure.
* Distribuera ett stort program med flera mikrotjänster till ett utvecklingsutrymme.
* Konfigurera CI/CD med GitHub-åtgärder.
* Testa en enda mikrotjänst i ett isolerat utvecklingsutrymme inom ramen för det fullständiga programmet.

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
* [Azure CLI installerat][azure-cli-installed].
* [Helm 3 installerat][helm-installed].
* Ett GitHub-konto med [GitHub-åtgärder aktiverade][github-actions-beta-signup].
* [Exempelprogrammet För Azure Dev Spaces-cykeldelning](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) som körs på ett AKS-kluster.

## <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry

Skapa ett Azure-behållarregister (ACR):

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Namnet som din ACR måste vara unikt i Azure och innehålla 5-50 alfanumeriska tecken. Alla bokstäver du använder måste vara gemener.

Spara *loginServer-värdet* från utdata eftersom det används i ett senare steg.

## <a name="create-a-service-principal-for-authentication"></a>Skapa ett huvudnamn för tjänsten för autentisering

Använd [az ad sp create-for-rbac för][az-ad-sp-create-for-rbac] att skapa ett huvudnamn för tjänsten. Ett exempel:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Spara JSON-utdata eftersom den används i ett senare steg.

Använd [az aks-visning][az-aks-show] för att visa *ID:n* för AKS-klustret:

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Använd [az acr-reglaget][az-acr-show] för att visa *ID:n* för ACR:

```azurecli
az acr show --name <acrName> --query id
```

Använd [az-rolltilldelning skapa][az-role-assignment-create] för att ge *Contributor* åtkomst till AKS-klustret och *AcrPush-åtkomst* till din ACR.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Du måste vara ägare till både AKS-klustret och ACR för att ge tjänstens huvudåtkomst till dessa resurser.

## <a name="configure-your-github-action"></a>Konfigurera din GitHub-åtgärd

> [!IMPORTANT]
> Du måste ha GitHub-åtgärder aktiverade för din databas. Om du vill aktivera GitHub-åtgärder för databasen navigerar du till databasen på GitHub, klickar på fliken Åtgärder och väljer att aktivera åtgärder för den här databasen.

Navigera till den kluslade databasen och klicka på *Inställningar*. Klicka på *Hemligheter* i det vänstra sidofältet. Klicka på *Lägg till en ny hemlighet* om du vill lägga till varje ny hemlighet nedan:

1. *AZURE_CREDENTIALS*: hela utdata från den tjänste- rektorn som skapas.
1. *RESOURCE_GROUP:* resursgruppen för AKS-klustret, som i det här exemplet är *MyResourceGroup*.
1. *CLUSTER_NAME:* namnet på AKS-klustret, som i det här exemplet är *MyAKS*.
1. *CONTAINER_REGISTRY:* *loginServer* för ACR.
1. *VÄRD:* Värden för ditt dev space, som tar formen *<MASTER_SPACE>.<APP_NAME>.<HOST_SUFFIX>*, som i det här exemplet är *dev.bikesharingweb.fedcab0987.eus.azds.io*.
1. *IMAGE_PULL_SECRET*: namnet på den hemlighet du vill använda, till exempel *demohemlig*.
1. *MASTER_SPACE:* namnet på ditt överordnade dev space, som i det här exemplet *är dev*.
1. *REGISTRY_USERNAME*: *clientId* från JSON-utdata från tjänstens huvudprincip.
1. *REGISTRY_PASSWORD*: *clientSecret* från JSON-utdata från tjänstens huvudskapande.

> [!NOTE]
> Alla dessa hemligheter används av GitHub-åtgärden och är konfigurerade i [.github/workflows/bikes.yml][github-action-yaml].

Om du vill uppdatera huvudutrymmet när din PR har slagits samman lägger du till *GATEWAY_HOST* hemlighet, som tar formen *<MASTER_SPACE>.gateway.<HOST_SUFFIX>*, som i det här exemplet är *dev.gateway.fedcab0987.eus.azds.io*. När du har sammanfogat ändringarna till huvudgrenen i gaffeln körs en annan åtgärd för att återskapa och köra hela programmet i huvuddelegeringsutrymmet. I det här exemplet är *huvudutrymmet utåt*. Den här åtgärden har konfigurerats i [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

## <a name="create-a-new-branch-for-code-changes"></a>Skapa en ny gren för kodändringar

Navigera `BikeSharingApp/` till och skapa en ny gren som kallas *cykelbilder*.

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

Redigera [cyklar/server.js][bikes-server-js] om du vill ta bort raderna 232 och 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Avsnittet ska nu se ut så här:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Spara filen och `git add` `git commit` använd sedan och för att iscensätta ändringarna.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Tryck på dina ändringar

Används `git push` för att skicka den nya grenen till den kluvna databasen:

```cmd
git push origin bike-images
```

När push är klar navigerar du till den kluvna databasen på GitHub för att skapa en pull-begäran med *huvudgrenen* i den kluvna databasen som basgren jämfört med *cykelavbildningsgrenen.*

När pull-begäran har öppnats navigerar du till fliken *Åtgärder.* Kontrollera att en ny åtgärd har startats och bygger tjänsten *Cyklar.*

## <a name="view-the-child-space-with-your-changes"></a>Visa det underordnade utrymmet med dina ändringar

När åtgärden har slutförts visas en kommentar med en URL till det nya underordnade utrymmet baserat på ändringarna i pull-begäran.

> [!div class="mx-imgBorder"]
> ![Åtgärdsadress för GitHub](../media/github-actions/github-action-url.png)

Navigera till *bikesharingweb-tjänsten* genom att öppna webbadressen från kommentaren. Välj *Aurelia Briggs (kund)* som användare och välj sedan en cykel att hyra. Kontrollera att du inte längre ser platshållarbilden för cykeln.

Om du sammanfogar ändringarna till *huvudgrenen* i gaffeln körs en annan åtgärd för att återskapa och köra hela programmet i det överordnade utvecklingsutrymmet. I det här exemplet är det överordnade utrymmet *dev*. Den här åtgärden har konfigurerats i [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

## <a name="clean-up-your-azure-resources"></a>Rensa dina Azure-resurser

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur Azure Dev Spaces hjälper dig att utveckla mer komplexa program över flera behållare och hur du kan förenkla samarbete genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Teamutveckling i Azure Dev Spaces][team-quickstart]

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
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md

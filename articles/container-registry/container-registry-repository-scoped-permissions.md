---
title: Behörigheter till databaser i Azure Container Registry
description: Skapa en token med behörigheter begränsade till specifika databaser i ett register för att hämta eller skicka avbildningar eller utföra andra åtgärder
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 7d390bf4d97561e374c70f184534ac4f98a40611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444335"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Skapa en token med behörigheter för databasscope

I den här artikeln beskrivs hur du skapar token och scopemappningar för att hantera behörigheter för databasscope i behållarregistret. Genom att skapa token kan en registerägare ge användare eller tjänster begränsad åtkomst till databaser för att hämta eller skicka avbildningar eller utföra andra åtgärder. En token ger fler detaljerade behörigheter än andra [registerautentiseringsalternativ](container-registry-authentication.md), som scopebehörigheter till ett helt register. 

Scenarier för att skapa en token inkluderar:

* Tillåt IoT-enheter med enskilda token att hämta en bild från en databas
* Ge en extern organisation behörighet till en viss databas 
* Begränsa databasåtkomsten till olika användargrupper i organisationen. Ge till exempel skriv- och läsåtkomst till utvecklare som skapar avbildningar som riktar sig till specifika databaser och läs åtkomst till team som distribuerar från dessa databaser.

> [!IMPORTANT]
> Den här funktionen är för närvarande i förhandsversion och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* Den här funktionen är **Premium** endast tillgänglig i ett Premium-behållarregister. Information om registertjänstnivåer och -begränsningar finns i [Azure Container Registry SKU: er](container-registry-skus.md).
* Du kan för närvarande inte tilldela behörigheter för databasscope till en Azure Active Directory-identitet, till exempel ett tjänsthuvudnamn eller hanterad identitet.

## <a name="concepts"></a>Begrepp

Om du vill konfigurera behörigheter för databasscope skapar du en *token* med en associerad *scope-mappning*. 

* En **token** tillsammans med ett genererat lösenord gör att användaren autentiserar med registret. Du kan ange ett utgångsdatum för ett tokenlösenord eller inaktivera en token när som helst.  

  När du har autentiserat med en token kan användaren eller tjänsten utföra en eller flera *åtgärder* som begränsas till en eller flera databaser.

  |Åtgärd  |Beskrivning  | Exempel |
  |---------|---------|--------|
  |`content/delete`    | Ta bort data från databasen  | Ta bort en databas eller ett manifest |
  |`content/read`     |  Läsa data från databasen |  Dra en artefakt |
  |`content/write`     |  Skriva data till databasen     | Använda `content/read` med för att driva en artefakt |
  |`metadata/read`    | Läsa metadata från databasen   | Lista taggar eller manifest |
  |`metadata/write`     |  Skriva metadata till databasen  | Aktivera eller inaktivera läs-, skriv- eller borttagningsåtgärder |

* En **scopemappning** grupperar de databasbehörigheter du tillämpar på en token och kan använda på andra token igen. Varje token är associerad med en enda scope-karta. 

   Med en scopekarta:

    * Konfigurera flera token med identiska behörigheter till en uppsättning databaser
    * Uppdatera tokenbehörigheter när du lägger till eller tar bort databasåtgärder i scopemappningen, eller tillämpa en annan scopemappning 

  Azure Container Registry innehåller också flera systemdefinierade scope-mappningar som du kan använda, med fasta behörigheter i alla databaser.

Följande bild visar förhållandet mellan token och scopekartor. 

![Registertokens och scopekartor](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Krav

* **Azure CLI** - Azure CLI-kommandon för att skapa och hantera token är tillgängliga i Azure CLI version 2.0.76 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).
* **Docker** - Om du vill autentisera med registret för att hämta eller pusha avbildningar behöver du en lokal Docker-installation. I Docker finns installationsanvisningar för [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Behållarregister** - Om du inte har ett, skapa ett Premium-behållarregister i din Azure-prenumeration eller uppgradera ett befintligt register. Använd till exempel [Azure-portalen](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Skapa token - CLI

### <a name="create-token-and-specify-repositories"></a>Skapa token och ange databaser

Skapa en token med kommandot [az acr token create.][az-acr-token-create] När du skapar en token kan du ange en eller flera databaser och associerade åtgärder i varje databas. Databaserna behöver inte vara i registret ännu. Information om hur du skapar en token genom att ange en befintlig scopemappning finns i nästa avsnitt.

I följande exempel skapas en token i registret *mitt* register `samples/hello-world` med `content/write` följande `content/read`behörigheter för repo: och . Som standard anger kommandot standardtokenstatus till `enabled`, men `disabled` du kan uppdatera statusen till när som helst.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

Utdata visar information om token, inklusive två genererade lösenord. Vi rekommenderar att du sparar lösenorden på ett säkert ställe som du kan använda senare för autentisering. Lösenorden kan inte hämtas igen, men nya kan genereras.

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

Utdata innehåller information om scopemappningen som kommandot har skapat. Du kan använda scope-kartan, här med namnet, `MyToken-scope-map`för att tillämpa samma databasåtgärder på andra token. Du kan också uppdatera scopemappningen senare för att ändra behörigheterna för de associerade token.

### <a name="create-token-and-specify-scope-map"></a>Skapa token och ange scopemappning

Ett alternativt sätt att skapa en token är att ange en befintlig scopemappning. Om du inte redan har en scope-karta skapar du först en genom att ange databaser och associerade åtgärder. Ange sedan scope-kartan när du skapar en token. 

Om du vill skapa en scope-karta använder du kommandot [az acr scope-map create.][az-acr-scope-map-create] Följande kommando skapar en scope-mappning med `samples/hello-world` samma behörigheter för den databas som tidigare använts. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Kör [az acr token skapa][az-acr-token-create] för att skapa en token, ange *MyScopeMap* scope kartan. Precis som i föregående exempel anger kommandot `enabled`standardtokenstatusen till .

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

Utdata visar information om token, inklusive två genererade lösenord. Vi rekommenderar att du sparar lösenorden på ett säkert ställe som du kan använda senare för autentisering. Lösenorden kan inte hämtas igen, men nya kan genereras.

## <a name="create-token---portal"></a>Skapa token - portal

Du kan använda Azure-portalen för att skapa token och scopemappningar. Precis som `az acr token create` med KOMMANDOT CLI kan du använda en befintlig scopemappning eller skapa en scopemappning när du skapar en token genom att ange en eller flera databaser och associerade åtgärder. Databaserna behöver inte vara i registret ännu. 

I följande exempel skapas en token och en scopemappning `samples/hello-world` skapas `content/write` `content/read`med följande behörigheter för databasen: och .

1. Navigera till behållarregistret i portalen.
1. Under **Tjänster**väljer du **Token (Förhandsversion) > +Lägg till**.
  ![Skapa token i portalen](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. Ange ett tokennamn.
1. Under **Scope-kartan**väljer du **Skapa ny**.
1. Konfigurera scopekartan:
    1. Ange ett namn och en beskrivning för scopekartan. 
    1. Under **Databaser**anger du `samples/hello-world`och under **Behörigheter** `content/write`väljer och `content/read` . Välj sedan **+Lägg till**.  
    ![Skapa scopekarta i portalen](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. När du har lagt till databaser och behörigheter väljer du **Lägg till** för att lägga till scopekartan.
1. Acceptera **standardtokenstatus** för **aktiverad** och välj sedan **Skapa**.

När token har validerats och skapats visas tokeninformation på **tokenskärmen.**

### <a name="add-token-password"></a>Lägg till tokenlösenord

Generera ett lösenord när du har skapat en token. Token måste vara aktiverad och ha ett giltigt lösenord för att kunna autentiseras med registret.

Du kan generera ett eller två lösenord och ange ett utgångsdatum för var och en. 

1. Navigera till behållarregistret i portalen.
1. Under **Tjänster**väljer du **Token (Förhandsversion)** och väljer en token.
1. I tokendetaljer väljer du **lösenord1** eller **lösenord2**och väljer ikonen Generera.
1. Ange ett utgångsdatum för lösenordet på lösenordsskärmen och välj **Generera**.
1. När du har genererat ett lösenord kopierar och sparar du det på en säker plats. Du kan inte hämta ett genererat lösenord när du har stängt skärmen, men du kan skapa ett nytt.

    ![Skapa tokenlösenord i portalen](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>Autentisera med token

När en användare eller tjänst använder en token för att autentisera med målregistret, innehåller den tokennamnet som användarnamn och ett av dess genererade lösenord. Autentiseringsmetoden beror på den konfigurerade åtgärden eller de åtgärder som är associerade med token.

|Åtgärd  |Så här autentiserar du  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete`i Azure CLI |
  |`content/read`     |  `docker login`<br/><br/>`az acr login`i Azure CLI  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login`i Azure CLI     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests`i Azure CLI   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update`i Azure CLI |

## <a name="examples-use-token"></a>Exempel: Använd token

I följande exempel används den token som skapats tidigare i den här artikeln för att utföra vanliga åtgärder i en databas: push och hämta bilder, ta bort bilder och listdatabastaggar. Token har konfigurerats inledningsvis med`content/write` push-behörigheter ( och `content/read` åtgärder) i `samples/hello-world` databasen.

### <a name="pull-and-tag-test-images"></a>Dra och tagga testbilder

Om du vill ha `hello-world` `alpine` följande exempel hämtar du och avbildningar från Docker Hub och taggar dem för registret och databasen.

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>Autentisera med token

Kör `docker login` för att autentisera med registret, Ange tokennamnet som användarnamn och ange ett av dess lösenord. Token måste ha `Enabled` status.

Följande exempel är formaterat för bash-skalet och anger värdena med hjälp av miljövariabler.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Utdata ska visa lyckad autentisering:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Push-överför avbildningar till registret

Efter lyckad inloggning, försök att skicka taggade avbildningar till registret. Eftersom token har behörighet att skicka `samples/hello-world` bilder till databasen lyckas följande push:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Token har inte behörighet till `samples/alpine` repo, så följande push-försök misslyckas `requested access to the resource is denied`med ett fel som liknar:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>Ändra push-/pull-behörigheter

Om du vill uppdatera behörigheterna för en token uppdaterar du behörigheterna i den associerade scope-kartan. Den uppdaterade scope-kartan tillämpas omedelbart på alla associerade token. 

Uppdatera till `MyToken-scope-map` exempel `content/write` `content/read` med och `samples/alpine` åtgärder i `content/write` databasen och `samples/hello-world` ta bort åtgärden i databasen.  

Om du vill använda Azure CLI kör du [uppdatering av az acr scope-map][az-acr-scope-map-update] för att uppdatera scope-kartan:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

På Azure Portal:

1. Navigera till behållarregistret.
1. Under **Tjänster**väljer du **Scope-kartor (förhandsversion)** och väljer den scopemappning som ska uppdateras.
1. Under **Databaser**anger du `samples/alpine`och under **Behörigheter** `content/write`väljer och `content/read` . Välj sedan **+Lägg till**.
1. Avmarkera och **avmarkera** `content/write`under `samples/hello-world` **Databaser**. Välj sedan **Spara**.

När du har uppdaterat scopekartan lyckas följande push:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

Eftersom scopemappningen bara `content/read` har `samples/hello-world` behörighet för databasen misslyckas nu ett push-försök till `samples/hello-world` repo:a-sidan.
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Om du drar bilder från båda databaserna `content/read` lyckas det, eftersom scopeöversikten innehåller behörigheter för båda databaserna:

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Ta bort avbildningar

Uppdatera scope-kartan genom `content/delete` att `alpine` lägga till åtgärden i databasen. Med den här åtgärden kan bilder tas bort i databasen eller ta bort hela databasen.

För enkelhetens skull visar vi bara kommandot [az acr scope-map update][az-acr-scope-map-update] för att uppdatera scopekartan:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

Om du vill uppdatera scopemappningen med hjälp av portalen finns i föregående avsnitt.

Använd följande kommando för ta bort `samples/alpine` az [acr-databas][az-acr-repository-delete] för att ta bort databasen. Om du vill ta bort bilder eller databaser autentiseras inte token via `docker login`. Skicka i stället tokens namn och lösenord till kommandot. I följande exempel används de miljövariabler som skapats tidigare i artikeln:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Visa repotaggar 

Uppdatera scope-kartan genom `metadata/read` att `hello-world` lägga till åtgärden i databasen. Med den här åtgärden kan du läsa manifest- och taggdata i databasen.

För enkelhetens skull visar vi bara kommandot [az acr scope-map update][az-acr-scope-map-update] för att uppdatera scopekartan:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

Om du vill uppdatera scopemappningen med hjälp av portalen finns i föregående avsnitt.

Om du vill `samples/hello-world` läsa metadata i databasen kör du kommandot [visa manifest för az acr-databas][az-acr-repository-show-manifests] eller az [acr-databasvisningskommando.][az-acr-repository-show-tags] 

För att läsa metadata autentiseras `docker login`inte token via . Skicka i stället tokens namn och lösenord till något av kommandona. I följande exempel används de miljövariabler som skapats tidigare i artikeln:

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

Exempel på utdata:

```console
[
  "v1"
]
```
## <a name="manage-tokens-and-scope-maps"></a>Hantera token och scopekartor

### <a name="list-scope-maps"></a>Kartor över listomfattning

Använd kommandot [az acr scope-map list,][az-acr-scope-map-list] eller skärmen **Scope maps (Preview)** i portalen, för att lista alla scopekartor som konfigurerats i ett register. Ett exempel:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

Utdata visar de scopekartor som du har definierat och flera systemdefinierade scopemappningar som du kan använda för att konfigurera token:

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Visa tokendetaljer

Om du vill visa information om en token, till exempel dess status och lösenordsförfallodatum, kör kommandot [az acr token show][az-acr-token-show] eller väljer token på skärmen **Tokens (Preview)** i portalen. Ett exempel:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Använd kommandot [az acr token list,][az-acr-token-list] eller **tokens (Preview)** på portalen, för att lista alla token som konfigurerats i ett register. Ett exempel:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>Generera lösenord för token

Om du inte har ett tokenlösenord, eller om du vill generera nya lösenord, kör du kommandot [az acr token autentiseringsuppgifter][az-acr-token-credential-generate] generera. 

I följande exempel genereras ett nytt värde för lösenord1 för *MyToken-token,* med en förfallotid på 30 dagar. Den lagrar lösenordet i `TOKEN_PWD`miljövariabeln . Det här exemplet är formaterat för bash-skalet.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Om du vill använda Azure-portalen för att generera ett tokenlösenord läser du stegen i [Skapa token - portal tidigare](#create-token---portal) i den här artikeln.

### <a name="update-token-with-new-scope-map"></a>Uppdatera token med ny scopekarta

Om du vill uppdatera en token med en annan scope-karta kör du [az acr-tokenuppdatering][az-acr-token-update] och anger den nya scope-kartan. Ett exempel:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

På skärmen **Tokens (förhandsgranska)** i portalen väljer du token och väljer en annan scopekarta under **Scope-karta.**

> [!TIP]
> När du har uppdaterat en token med en ny scope-karta kanske du vill generera nya tokenlösenord. Använd kommandot [az acr token autentiseringsuppgifter generera][az-acr-token-credential-generate] eller återskapa ett tokenlösenord i Azure-portalen.

## <a name="disable-or-delete-token"></a>Inaktivera eller ta bort token

Du kan behöva tillfälligt inaktivera användningen av tokenautentiseringsuppgifterna för en användare eller tjänst. 

Med hjälp av Azure CLI kör du kommandot [az acr token update][az-acr-token-update] för att ställa in `status` till: `disabled`

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

I portalen väljer du token på skärmen **Tokens (Preview)** och väljer **Inaktiverad** under **Status**.

Om du vill ta bort en token för att permanent ogiltigförklara åtkomsten för alla som använder dess autentiseringsuppgifter kör du kommandot [az acr token delete.][az-acr-token-delete] 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

I portalen väljer du token på skärmen **Tokens (Preview)** och väljer **Ignorera**.

## <a name="next-steps"></a>Nästa steg

* Om du vill hantera scopemappningar och token använder du ytterligare kommandon i kommandogrupperna [az acr scope-map][az-acr-scope-map] och [az acr token.][az-acr-token]
* Se [autentiseringsöversikten](container-registry-authentication.md) för andra alternativ för att autentisera med ett Azure-behållarregister, inklusive användning av en Azure Active Directory-identitet, ett tjänsthuvudnamn eller ett administratörskonto.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az-acr-repository-show-manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az-acr-repository-delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-list]: /cli/azure/acr/token/#az-acr-token-list
[az-acr-token-delete]: /cli/azure/acr/token/#az-acr-token-delete
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate

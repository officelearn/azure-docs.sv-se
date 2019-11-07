---
title: Behörigheter till databaser i Azure Container Registry
description: Skapa en token med behörigheter som är begränsade till vissa databaser i ett register för att hämta eller skicka avbildningar
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 10/31/2019
ms.author: danlep
ms.openlocfilehash: dbfadea9bc05614108333ef5a046e11b5280cc68
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588439"
---
# <a name="repository-scoped-permissions-in-azure-container-registry"></a>Databas – begränsade behörigheter i Azure Container Registry 

Azure Container Registry stöder flera [autentiseringsalternativ](container-registry-authentication.md) med identiteter som har [rollbaserad åtkomst](container-registry-roles.md) till ett helt register. För vissa scenarier kan du dock behöva ge åtkomst till specifika *databaser* i ett register. 

Den här artikeln visar hur du skapar och använder en åtkomsttoken som har behörighet att utföra åtgärder på endast vissa databaser i ett register. Med en åtkomsttoken kan du tillhandahålla användare eller tjänster med begränsad, tidsbegränsad åtkomst till databaser för att hämta eller skicka avbildningar eller utföra andra åtgärder. 

Mer information finns i [om behörigheter för databas omfång](#about-repository-scoped-permissions), längre fram i den här artikeln, för bakgrunds information om token och scenarier.

> [!IMPORTANT]
> Den här funktionen är för närvarande en för hands version och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* Den här funktionen är endast tillgänglig i ett **Premium** container Registry. Information om register tjänst nivåer finns i [Azure Container Registry SKU: er](container-registry-skus.md).
* Du kan för närvarande inte tilldela databasens begränsade behörigheter till ett Azure Active Directory-objekt, till exempel ett tjänst huvud namn eller en hanterad identitet.
* Ett register tillåter högst 20 000 omfångs kartor och 20 000-token.

## <a name="prerequisites"></a>Nödvändiga komponenter

* **Azure CLI** – den här artikeln kräver en lokal installation av Azure CLI (version 2.0.76 eller senare). Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).
* **Docker** -för att autentisera med registret behöver du också en lokal Docker-installation. I Docker finns installationsanvisningar för [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Behållar register med databaser** – om du inte har ett kan du skapa ett behållar register i din Azure-prenumeration. Använd till exempel [Azure Portal](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md). 

  I test syfte, [push](container-registry-get-started-docker-cli.md) eller [Importera](container-registry-import-images.md) en eller flera exempel bilder till registret. Exemplen i den här artikeln hänvisar till följande bilder i två databaser: `samples/hello-world:v1` och `samples/nginx:v1`. 

## <a name="create-an-access-token"></a>Skapa en åtkomsttoken

Skapa en token med kommandot [AZ ACR token Create][az-acr-token-create] . När du skapar en token anger du en eller flera databaser och associerade åtgärder på varje lagrings plats eller anger en befintlig omfattnings karta med dessa inställningar.

### <a name="create-access-token-and-specify-repositories"></a>Skapa åtkomsttoken och ange databaser

I följande exempel skapas en åtkomsttoken med behörighet att utföra `content/write` och `content/read` åtgärder på `samples/hello-world`-lagringsplatsen och `content/read` åtgärden på `samples/nginx`-lagringsplatsen. Som standard genererar kommandot två lösen ord. 

I det här exemplet anges token-status till `enabled` (standardinställningen), men du kan när som helst uppdatera token och ange status till `disabled`.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read --status enabled
```

Utdata visar information om token, inklusive skapade lösen ord och omfångs karta. Vi rekommenderar att du sparar lösen orden på ett säkert ställe för att använda senare med `docker login`. Det går inte att hämta lösen orden igen men nya kan genereras.

Utdata visar också att en omfångs karta skapas automatiskt, med namnet `MyToken-scope-map`. Du kan använda omfångs kartan för att tillämpa samma databas åtgärder på andra tokens. Eller så kan du uppdatera omfångs kartan senare för att ändra behörigheterna för token.

```console
{
  "creationDate": "2019-10-22T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
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

### <a name="create-a-scope-map-and-associated-token"></a>Skapa en omfångs karta och associerad token

Du kan också ange en omfattnings karta med databaser och associerade åtgärder när du skapar en token. Om du vill skapa en omfångs karta använder du kommandot [AZ ACR scope-Map Create][az-acr-scope-map-create] .

Följande exempel kommando skapar en omfångs karta med samma behörigheter som användes i föregående exempel. Den tillåter `content/write` och `content/read` åtgärder på `samples/hello-world`-lagringsplatsen och `content/read` åtgärden på `samples/nginx`-lagringsplatsen:

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read \
  --description "Sample scope map"
```

De utdata som genereras liknar följande:

```console
{
  "actions": [
    "repositories/samples/hello-world/content/write",
    "repositories/samples/nginx/content/read"
  ],
  "creationDate": "2019-10-22T05:07:35.194413+00:00",
  "description": "Sample scope map.",
  "id": "/subscriptions/fxxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyScopeMap",
  "name": "MyScopeMap",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapType": "UserDefined",
  "type": "Microsoft.ContainerRegistry/registries/scopeMaps"
```

Kör [AZ ACR token Create][az-acr-token-create] för att skapa en token som är associerad med *MyScopeMap* -omfångs kartan. Som standard genererar kommandot två lösen ord. I det här exemplet anges token-status till `enabled` (standardinställningen), men du kan när som helst uppdatera token och ange status till `disabled`.

```azurecli
az acr token create --name MyToken --registry myregistry --scope-map MyScopeMap --status enabled
```

Utdata visar information om token, inklusive genererade lösen ord och omfångs karta som du har använt. Vi rekommenderar att du sparar lösen orden på ett säkert ställe för att använda senare med `docker login`. Det går inte att hämta lösen orden igen men nya kan genereras.

## <a name="generate-passwords-for-token"></a>Skapa lösen ord för token

Om lösen ord skapades när du skapade token, fortsätter du [med att autentisera med registret](#authenticate-using-token).

Om du inte har ett token-lösenord, eller om du vill generera nya lösen ord, kör du kommandot [AZ ACR token Credential generate][az-acr-token-credential-generate] .

I följande exempel skapas ett nytt lösen ord för den token som du skapade, med en förfallo period på 30 dagar. Lösen ordet lagras i miljövariabeln TOKEN_PWD. Det här exemplet är formaterat för bash-gränssnittet.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

## <a name="authenticate-using-token"></a>Autentisera med token

Kör `docker login` för att autentisera med registret med hjälp av autentiseringsuppgifter för token. Ange namnet på token som användar namn och ange ett lösen ord. Följande exempel är formaterat för bash-gränssnittet och innehåller värdena med miljövariabler.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Utdata ska Visa lyckad autentisering:

```console
Login Succeeded
```

## <a name="verify-scoped-access"></a>Verifiera begränsad åtkomst

Du kan kontrol lera att token tillhandahåller begränsade behörigheter till databaserna i registret. I det här exemplet slutförs följande `docker pull` kommandon för att hämta avbildningar som är tillgängliga i `samples/hello-world` och `samples/nginx`-databaser:

```console
docker pull myregistry.azurecr.io/samples/hello-world:v1
docker pull myregistry.azurecr.io/samples/nginx:v1
```

Eftersom exempel-token bara tillåter `content/write` åtgärden på `samples/hello-world`-lagringsplatsen, `docker push` lyckas med lagrings platsen, men det går inte att `samples/nginx`:

```console
# docker push succeeds
docker pull myregistry.azurecr.io/samples/hello-world:v1

# docker push fails
docker pull myregistry.azurecr.io/samples/nginx:v1
```

## <a name="update-scope-map-and-token"></a>Uppdatera omfångs karta och token

Om du vill uppdatera behörigheterna för token uppdaterar du behörigheterna i den associerade omfångs mappningen med hjälp av [AZ ACR scope-Map Update][az-acr-scope-map-update]. Om du till exempel vill uppdatera *MyScopeMap* för att ta bort åtgärden `content/write` på `samples/hello-world`-lagringsplatsen:

```azurecli
az acr scope-map update --name MyScopeMap --registry myregistry \
  --remove samples/hello-world content/write
```

Om omfångs kartan är associerad med mer än en token uppdaterar kommandot behörigheten för alla associerade tokens.

Om du vill uppdatera en token med en annan omfångs karta kör du [AZ ACR token Update][az-acr-token-update]. Till exempel:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

När du har uppdaterat en token eller en omfångs karta som är associerad med en token börjar behörighets ändringarna gälla vid nästa `docker login` eller annan autentisering med hjälp av token.

När du har uppdaterat en token kanske du vill generera nya lösen ord för att få åtkomst till registret. Kör [AZ för ACR token generate][az-acr-token-credential-generate]. Till exempel:

```azurecli
az acr token credential generate \
  --name MyToken --registry myregistry --days 30
```

## <a name="about-repository-scoped-permissions"></a>Om behörigheter för databas omfång

### <a name="concepts"></a>Koncept

Om du vill konfigurera behörigheter för databasens omfång skapar du en *åtkomsttoken* och en associerad *omfattnings mappning* med hjälp av kommandon i Azure CLI.

* En **åtkomsttoken är en** autentiseringsuppgift som används med ett lösen ord för att autentisera med registret. Kopplade till varje token tillåts *åtgärder* som är begränsade till en eller flera databaser. Du kan ange en förfallo tid för varje token. 

* **Åtgärder** på varje angiven lagrings plats innehåller en eller flera av följande.

  |Åtgärd  |Beskrivning  |
  |---------|---------|
  |`content/read`     |  Läs data från lagrings platsen. Hämta till exempel en artefakt.  |
  |`metadata/read`    | Läsa metadata från lagrings platsen. Du kan till exempel Visa taggar eller visa metadata för manifestet.   |
  |`content/write`     |  Skriv data till lagrings platsen. Använd med `content/read` för att skicka en artefakt.    |
  |`metadata/write`     |  Skriv metadata till lagrings platsen. Du kan till exempel uppdatera manifest-attribut.  |
  |`content/delete`    | Ta bort data från lagrings platsen. Du kan till exempel ta bort en lagrings plats eller ett manifest. |

* En **omfattnings karta** är ett register objekt som grupperar lagrings plats behörigheter som du tillämpar på en token eller som kan tillämpas på andra tokens. Om du inte använder en omfångs karta när du skapar en token skapas en omfångs karta automatiskt åt dig, för att spara behörighets inställningarna. 

  En omfångs karta hjälper dig att konfigurera flera användare med identisk åtkomst till en uppsättning databaser. Azure Container Registry tillhandahåller också systemdefinierade omfångs kartor som du kan använda när du skapar åtkomsttoken.

Följande bild sammanfattar relationen mellan tokens och omfångs kartor. 

![Register omfångs kartor och tokens](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

### <a name="scenarios"></a>Scenarier

Scenarier för att använda en åtkomsttoken är:

* Tillhandahålla IoT-enheter med enskilda token för att hämta en avbildning från en lagrings plats
* Ange en extern organisation med behörigheter till en speciell lagrings plats 
* Begränsa lagrings plats åtkomst till vissa användar grupper i din organisation. Du kan till exempel ge Skriv-och Läs behörighet till utvecklare som skapar avbildningar som är riktade till specifika lagrings platser och Läs åtkomst till team som distribuerar från dessa databaser.

### <a name="authentication-using-token"></a>Autentisering med token

Använd ett token-namn som användar namn och ett av dess associerade lösen ord för att autentisera med mål registret. Autentiseringsmetoden beror på vilka åtgärder som har kon figurer ATS.

### <a name="contentread-or-contentwrite"></a>innehåll/läsa eller innehåll/skriva

Om token endast tillåter `content/read` eller `content/write` åtgärder anger du autentiseringsuppgifter för token i något av följande autentiseringsscheman:

* Autentisera med Docker med hjälp av `docker login`
* Autentisera med registret med hjälp av kommandot [AZ ACR login][az-acr-login] i Azure CLI

Efter autentiseringen tillåter token de konfigurerade åtgärderna för lagrings platsen eller databaserna. Om exempelvis token tillåter `content/read` åtgärden på en lagrings plats, tillåts `docker pull` åtgärder på avbildningar i lagrings platsen.

#### <a name="metadataread-metadatawrite-or-contentdelete"></a>metadata/läsning, metadata/skrivning eller innehåll/ta bort

Om token tillåter `metadata/read`, `metadata/write`eller `content/delete` åtgärder på en lagrings plats, måste token-autentiseringsuppgifter tillhandahållas som parametrar med de relaterade [AZ ACR-databas][az-acr-repository] kommandona i Azure CLI.

Om `metadata/read` åtgärder till exempel tillåts på en lagrings plats skickar du token-autentiseringsuppgifterna när du kör kommandot [AZ ACR-lagringsplatsen show-Tags][az-acr-repository-show-tags] för att Visa taggar.

## <a name="next-steps"></a>Nästa steg

* Om du vill hantera områdes kartor och åtkomsttoken använder du ytterligare kommandon i kommando grupperna [AZ ACR scope-Map][az-acr-scope-map] och [AZ ACR-token][az-acr-token] .
* I [översikten över autentisering](container-registry-authentication.md) finns scenarier för att autentisera med ett Azure Container Registry med ett administratörs konto eller en Azure Active Directory identitet.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate

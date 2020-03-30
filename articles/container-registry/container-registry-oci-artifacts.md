---
title: Push och dra OCI-artefakt
description: Push and pull Open Container Initiative (OCI) artefakter med hjälp av ett privat behållarregister i Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 03/11/2020
ms.author: stevelas
ms.openlocfilehash: 2c6b66b635a2513ccc19e0352414d18d8389fef1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371060"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Pusha och hämta en OCI-artefakt med hjälp av ett Azure-behållarregister

Du kan använda ett Azure-behållarregister för att lagra och hantera [OCI-artefakter (Open Container Initiative)](container-registry-image-formats.md#oci-artifacts) samt Docker- och Docker-kompatibla behållaravbildningar.

För att demonstrera den här funktionen visar den här artikeln hur du använder [ORAS-verktyget (OCI Registry as Storage)](https://github.com/deislabs/oras) för att skicka en exempelartefakt - en textfil - till ett Azure-behållarregister. Dra sedan artefakten från registret. Du kan hantera en mängd olika OCI-artefakter i ett Azure-behållarregister med hjälp av olika kommandoradsverktyg som är lämpliga för varje artefakt.

## <a name="prerequisites"></a>Krav

* **Azure-containerregister** – Skapa ett containerregister i din Azure-prenumeration. Använd till exempel [Azure-portalen](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
* **ORAS-verktyg** - Ladda ner och installera en aktuell ORAS-version för ditt operativsystem från [GitHub-repo .](https://github.com/deislabs/oras/releases) Verktyget släpps som en komprimerad`.tar.gz` tarball (fil). Extrahera och installera filen med standardprocedurer för operativsystemet.
* **Azure Active Directory-tjänstens huvudnamn (valfritt)** – Om du vill autentisera direkt med ORAS skapar du ett [tjänsthuvudnamn](container-registry-auth-service-principal.md) för att komma åt registret. Kontrollera att tjänstens huvudnamn har tilldelats en roll som AcrPush så att den har behörighet att skicka och hämta artefakter.
* **Azure CLI (valfritt)** - Om du vill använda en enskild identitet behöver du en lokal installation av Azure CLI. Version 2.0.71 eller senare rekommenderas. Kör `az --version `för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).
* **Docker (valfritt)** - Om du vill använda en enskild identitet måste du också ha Docker installerat lokalt för att autentisera med registret. Docker innehåller paket som enkelt kan konfigurera Docker på ett [macOS][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-system.


## <a name="sign-in-to-a-registry"></a>Logga in i ett register

I det här avsnittet visas två föreslagna arbetsflöden för att logga in på registret, beroende på vilken identitet som används. Välj den metod som är lämplig för din miljö.

### <a name="sign-in-with-oras"></a>Logga in med ORAS

Med hjälp av ett [tjänsthuvudnamn](container-registry-auth-service-principal.md) med push-rättigheter kör du `oras login` kommandot för att logga in på registret med tjänstens huvudprogram-ID och lösenord. Ange det fullständigt kvalificerade registernamnet (alla gemener) i det här fallet *myregistry.azurecr.io*. Tjänstens huvudprogram-ID skickas i `$SP_APP_ID`miljövariabeln och `$SP_PASSWD`lösenordet i variabeln .

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Om du vill läsa lösenordet `--password-stdin`från Stdin använder du .

### <a name="sign-in-with-azure-cli"></a>Logga in med Azure CLI

[Logga in](/cli/azure/authenticate-azure-cli) på Azure CLI med din identitet för att skicka och hämta artefakter från behållarregistret.

Använd sedan Azure CLI-kommandot [az acr-inloggning](/cli/azure/acr?view=azure-cli-latest#az-acr-login) för att komma åt registret. Till exempel, för att autentisera till ett register med namnet *mitt register:*

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`använder Docker-klienten för att ange `docker.config` en Azure Active Directory-token i filen. Docker-klienten måste installeras och köras för att slutföra det enskilda autentiseringsflödet.

## <a name="push-an-artifact"></a>Driva en artefakt

Skapa en textfil i en lokal arbetskatalog med lite exempeltext. Till exempel i ett bash skal:

```bash
echo "Here is an artifact!" > artifact.txt
```

Använd `oras push` kommandot för att skicka den här textfilen till registret. I följande exempel skickar exempeltextfilen `samples/artifact` till reporäntan. Registret identifieras med det fullständigt kvalificerade registernamnet *myregistry.azurecr.io* (alla gemener). Artefakten `1.0`är taggad . Artefakten har som standard en odefinierad typ som identifieras `artifact.txt`av *medietypsträngen* efter filnamnet . Se [OCI-artefakter](https://github.com/opencontainers/artifacts) för ytterligare typer. 

**Linux**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

Utdata för en lyckad push liknar följande:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Om du vill hantera artefakter i registret kör du standardkommandon `az acr` för hantering av avbildningar om du använder Azure CLI. Hämta till exempel attributen för artefakten med kommandot [az acr repository show:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

De utdata som genereras liknar följande:

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>Dra en artefakt

Kör `oras pull` kommandot för att hämta artefakten från registret.

Ta först bort textfilen från den lokala arbetskatalogen:

```bash
rm artifact.txt
```

Kör `oras pull` för att hämta artefakten och ange vilken medietyp som används för att skicka artefakten:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Kontrollera att dragningen lyckades:

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>Ta bort artefakten (valfritt)

Om du vill ta bort artefakten från ditt Azure-behållarregister använder du kommandot [az acr-databasborttagning.][az-acr-repository-delete] I följande exempel tas artefakten som du lagrade där bort:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om [ORAS-biblioteket](https://github.com/deislabs/oras/tree/master/docs), inklusive hur du konfigurerar ett manifest för en artefakt
* Besök [OCI Artifacts](https://github.com/opencontainers/artifacts) repo för referensinformation om nya artefakttyper



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete

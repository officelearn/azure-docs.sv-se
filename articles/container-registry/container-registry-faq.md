---
title: Azure Container Registry – vanliga frågor och svar
description: Svar på vanliga frågor som rör Azure Container Registry-tjänsten
services: container-registry
author: sajayantony
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 5/13/2019
ms.author: sajaya
ms.openlocfilehash: 86efb6b655405500f994a5a5ec7acbd18c645004
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957857"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Vanliga frågor och svar om Azure Container Registry

Den här artikeln tar upp vanliga frågor och kända problem med Azure Container Registry.

## <a name="resource-management"></a>Resurshantering

- [Kan jag skapa ett Azure container registry med en Resource Manager-mallen?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Finns det säkerhetsproblem som söker efter bilder i ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Hur konfigurerar jag Kubernetes med Azure Container Registry?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Hur får jag administratörsautentiseringsuppgifter för ett container registry?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Hur får jag administratörsautentiseringsuppgifter i en Resource Manager-mall](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Borttagning av replikering misslyckas med förbjuden status, även om replikeringen tas bort med hjälp av Azure CLI eller Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Kan jag skapa ett Azure Container Registry med hjälp av en Resource Manager-mallen?

Ja. Här är [en mall](https://github.com/Azure/azure-cli/blob/master/src/command_modules/azure-cli-acr/azure/cli/command_modules/acr/template.json) att du kan använda för att skapa ett register.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Finns det säkerhetsproblem som söker efter bilder i ACR?

Ja. Finns i dokumentationen från [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) och [Aqua](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Hur konfigurerar jag Kubernetes med Azure Container Registry?

Se dokumentationen för [Kubernetes](http://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) och stegen för [Azure Kubernetes Service](container-registry-auth-aks.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Hur får jag administratörsautentiseringsuppgifter för ett container registry?

> [!IMPORTANT]
> Administratörsanvändarkontot har utformats för en enskild användare åtkomst till registret, främst i testsyfte. Vi rekommenderar inte att dela autentiseringsuppgifterna som administratör-konto med flera användare. Individuell identitet rekommenderas för användare och tjänsthuvudnamn för fjärradministrerad scenarier. Se [översikt över autentisering](container-registry-authentication.md).

Kontrollera att registrets administratörsanvändaren har aktiverats innan du hämtar administratörsautentiseringsuppgifter.

Hämta autentiseringsuppgifter med hjälp av Azure CLI:

```azurecli
az acr credential show -n myRegistry
```

Använda Azure Powershell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Hur får jag administratörsautentiseringsuppgifter i en Resource Manager-mall

> [!IMPORTANT]
> Administratörsanvändarkontot har utformats för en enskild användare åtkomst till registret, främst i testsyfte. Vi rekommenderar inte att dela autentiseringsuppgifterna som administratör-konto med flera användare. Individuell identitet rekommenderas för användare och tjänsthuvudnamn för fjärradministrerad scenarier. Se [översikt över autentisering](container-registry-authentication.md).

Kontrollera att registrets administratörsanvändaren har aktiverats innan du hämtar administratörsautentiseringsuppgifter.

Hämta det första lösenordet:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Att hämta lösenordet för andra:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Borttagning av replikering misslyckas med förbjuden status, även om replikeringen tas bort med hjälp av Azure CLI eller Azure PowerShell

Felet visas när användaren har behörighet för ett register men har inte behörighet för läsare på servernivå för prenumerationen. Lös problemet genom att tilldela Läsarbehörigheter för prenumerationen för användaren:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

## <a name="registry-operations"></a>Registeråtgärder

- [Hur kommer jag åt Docker Registry HTTP API V2?](#how-do-i-access-docker-registry-http-api-v2)
- [Hur tar jag bort alla manifest som inte refererar till en tagg i en databas?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Varför minskar inte kvotanvändning för registret när du tar bort avbildningar?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Hur jag för att validera ändringar av storage kvoten?](#how-do-i-validate-storage-quota-changes)
- [Hur autentiserar jag med min registret när du kör CLI i en behållare?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Erbjuder Azure Container Registry TLS version 1.2 endast konfiguration och hur du aktiverar TLS version 1.2?](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [Stöder Azure Container Registry lita på innehåll?](#does-azure-container-registry-support-content-trust)
- [Hur jag för att bevilja åtkomst till pull eller push bilder utan behörighet att hantera Registerresurser?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Hur aktiverar jag automatisk bild karantän för ett register](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Hur kommer jag åt Docker Registry HTTP API V2?

ACR har stöd för Docker Registry HTTP API V2. API: erna kan nås på `https://<your registry login server>/v2/`. Exempel: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Hur tar jag bort alla manifest som inte refererar till en tagg i en databas?

Om du är i bash:

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

För Powershell:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Anteckning: Du kan lägga till `-y` i kommandot delete för att hoppa över bekräftelse.

Mer information finns i [ta bort avbildningar i Azure Container Registry](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Varför minskar inte kvotanvändning för registret när du tar bort avbildningar?

Den här situationen kan inträffa om de underliggande lagren fortfarande som refereras av andra behållaravbildningar. Om du tar bort en avbildning med några referenser uppdaterar registret användningen på några få minuter.

### <a name="how-do-i-validate-storage-quota-changes"></a>Hur jag för att validera ändringar av storage kvoten?

Skapa en avbildning med en 1GB-lagret med hjälp av följande docker-filen. Detta säkerställer att avbildningen har ett lager som inte delas med andra bilder i registret.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Skapa och överför avbildningen till registret med hjälp av docker CLI.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Du bör kunna se att lagringsanvändningen har ökat i Azure-portalen eller du kan fråga efter användning med hjälp av CLI.

```bash
az acr show-usage -n myregistry
```

Ta bort avbildningen med hjälp av Azure CLI eller portalen och kontrollera om uppdaterade på några få minuter.

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Hur autentiserar jag med min registret när du kör CLI i en behållare?

Måste du köra Azure CLI-behållare genom att montera Docker-socket:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

I behållaren, installerar `docker`:

```bash
apk --update add docker
```

Sedan autentisera med ditt register:

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>Erbjuder Azure Container Registry TLS version 1.2 endast konfiguration och hur du aktiverar TLS version 1.2?

Ja. Aktivera TLS med hjälp av alla de senaste docker-klienten (version 18.03.0 och senare). 

### <a name="does-azure-container-registry-support-content-trust"></a>Stöder Azure Container Registry lita på innehåll?

Ja, du kan använda betrodda avbildningar i Azure Container Registry, eftersom den [Docker notarie](https://docs.docker.com/notary/getting_started/) har integrerats och kan aktiveras. Mer information finns i [innehåll förtroende i Azure Container Registry](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Var finns filen för tumavtrycket finns?

Under `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* Offentliga nycklar och certifikat för alla roller (utom delegering roller) lagras i den `root.json`.
* Offentliga nycklar och certifikat i rollen delegering lagras i JSON-fil med dess överordnade roll (till exempel `targets.json` för den `targets/releases` roll).

Vi rekommenderar för att verifiera de offentliga nycklar och certifikat efter den övergripande TUF kontroll görs av Docker och notarie klienten.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Hur jag för att bevilja åtkomst till pull eller push bilder utan behörighet att hantera Registerresurser?

Har stöd för ACR [anpassade roller](container-registry-roles.md) som ger olika behörighetsnivåer. Mer specifikt `AcrPull` och `AcrPush` roller Tillåt användare att pull och/eller push bilder utan behörighet att hantera Registerresurser i Azure.

* Azure-portalen: Ditt register -> åtkomstkontroll (IAM) -> Lägg till (Välj `AcrPull` eller `AcrPush` för rollen).
* Azure CLI: Hitta resurs-ID för registret genom att köra följande kommando:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Du kan koppla den `AcrPull` eller `AcrPush` roll till en användare (i följande exempel används `AcrPull`):

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  Alternativt kan du tilldela rollen till tjänstens huvudnamn identifieras av dess program-ID:

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

Denna person är sedan kan autentisera och komma åt avbildningarna i registret.

* Att autentisera till ett register:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Att lista databaser:

  ```azurecli
  az acr repository list -n myRegistry
  ```

 Att hämta en avbildning:
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

Med hjälp av endast den `AcrPull` eller `AcrPush` rollen, de tilldelad personen har inte behörighet att hantera Registerresurser i Azure. Till exempel `az acr list` eller `az acr show -n myRegistry` visas inte i registret.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Hur aktiverar jag automatisk bild karantän för ett register?

Bild karantän är för närvarande en förhandsversion av funktionen för ACR. Du kan aktivera läget för ett register i karantän så att dessa avbildningar som har godkänts security sökning visas för normal användare. Mer information finns i [ACR GitHub-lagringsplatsen](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

## <a name="diagnostics"></a>Diagnostik

- [docker pull misslyckas med felkoden: net/http: begäran avbröts under väntan anslutning (Client.Timeout överskred medan du väntar på rubriker)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker push lyckas men docker pull misslyckas med felkoden: obehörig: autentisering krävs](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [Aktivera och hämta felsökningsloggar av docker-daemon](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [Nya användarbehörigheter kan inte gälla omedelbart när du har uppdaterat](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Autentiseringsinformation ges inte i rätt format på direkta REST API-anrop](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Varför Azure-portalen inte innehåller alla mina databaser eller taggar?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull misslyckas med felkoden: net/http: begäran avbröts under väntan anslutning (Client.Timeout överskred medan du väntar på rubriker)

 - Om det här felet är ett tillfälligt fel, att försök lyckas. 
 - Om `docker pull` kontinuerligt misslyckas kan det vara ett problem med docker-daemon. Allmänt kan du minimera problemet genom att starta om docker-daemon. 
 - Om du ser det här problemet efter omstart docker-daemon, kan problemet vara vissa problem med nätverksanslutningen till datorn. Du kan kontrollera om Allmänt nätverk på datorn är felfri genom att prova ett kommando som `ping www.bing.com`.
 - Du bör alltid ha en mekanism för återförsök på alla docker-Klientåtgärder.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>docker push lyckas men docker pull misslyckas med felkoden: obehörig: autentisering krävs

Det här felet kan inträffa med Red Hat-version av docker-daemon där `--signature-verification` är aktiverat som standard. Du kan kontrollera alternativ för docker-daemon för Red Hat Enterprise Linux (RHEL) eller Fedora genom att köra följande kommando:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Exempel: Fedora 28-servern har följande alternativ för docker-daemon:

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

Med `--signature-verification=false` saknas, `docker pull` misslyckas med ett fel som liknar:

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Att lösa problemet:
1. Lägga till alternativet `--signature-verification=false` till konfigurationsfilen för docker-daemon `/etc/sysconfig/docker`. Exempel:

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. Starta om docker-daemon-tjänst genom att köra följande kommando:

  ```bash
  sudo systemctl restart docker.service
  ```

Information om `--signature-verification` kan hittas genom att köra `man dockerd`.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Aktivera och hämta felsökningsloggar av docker-daemon  

Starta `dockerd` med den `debug` alternativet. Börja med att skapa konfigurationsfil för docker-daemon (`/etc/docker/daemon.json`) om det inte finns, och lägga till den `debug` alternativet:

```json
{   
    "debug": true   
}
```

Starta om daemon. Till exempel med Ubuntu 14.04:

```bash
sudo service docker restart
```

Information finns i den [Docker-dokumentation](https://docs.docker.com/engine/admin/#enable-debugging). 

 * Loggarna kan skapas på olika platser, beroende på ditt system. Till exempel för Ubuntu 14.04 har `/var/log/upstart/docker.log`.   
Se [Docker-dokumentation](https://docs.docker.com/engine/admin/#read-the-logs) mer information.    

 * Loggarna genereras under % LOCALAPPDATA%/docker/ för Docker för Windows. Men det får inte innehålla alla felsökningsinformationen ännu.   

   Du kan behöva några extra steg för att komma åt fullständig daemon-loggen:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Nu har du åtkomst till alla filer för virtuell dator som kör `dockerd`. Loggen är på `/var/log/docker.log`.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Nya användarbehörigheter kan inte gälla omedelbart när du har uppdaterat

När du beviljar nya kanske behörigheter (nya roller) till tjänstens huvudnamn, ändringen inte träder i kraft omedelbart. Det finns två möjliga orsaker:

* Azure Active Directory rollen tilldelningens förskjutning. Det är normalt snabb, men det kan ta några minuter på grund av spridning fördröjning.
* Behörigheten fördröjning på token ACR-servern. Det kan ta upp till 10 minuter. För att lösa, kan du `docker logout` och sedan autentisera igen med samma användare efter 1 minut:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

ACR stöder för närvarande inte home replikering borttagning av användarna. Lösningen är att inkludera home replikeringen skapa i mallen, men hoppa över den har skapandet genom att lägga till `"condition": false` enligt nedan:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Autentiseringsinformation ges inte i rätt format på direkta REST API-anrop

Du kan stöta på ett `InvalidAuthenticationInfo` fel, särskilt med hjälp av den `curl` verktyget med alternativet `-L`, `--location` (för att följa omdirigeringar).
Till exempel hämtar en blob med hjälp av `curl` med `-L` alternativet och grundläggande autentisering:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

kan resultera i följande svar:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

Den grundläggande orsaken är att vissa `curl` implementeringar Följ omdirigeringar med rubriker från den ursprungliga begäran.

Du måste följa omdirigeringar manuellt utan rubrikerna för att lösa problemet. Skriva ut svarshuvuden med den `-D -` möjlighet att `curl` och sedan extrahera: den `Location` rubrik:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Varför Azure-portalen inte innehåller alla mina databaser eller taggar? 

Om du använder Microsoft Edge-webbläsaren, kan du se högst 100 databaser eller taggar som anges. Om ditt register har fler än 100 databaser eller taggar, rekommenderar vi att du använder antingen webbläsaren Firefox eller Chrome för att lista alla.

## <a name="tasks"></a>Aktiviteter

- [Hur jag för att batch Avbryt körningar?](#how-do-i-batch-cancel-runs)
- [Hur jag för att inkludera mappen .git i az acr build-kommandot?](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>Hur jag för att batch Avbryt körningar?

Följande kommandon avbryta alla aktiviteter som körs i det angivna registret.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Hur jag för att inkludera mappen .git i az acr build-kommandot?

Om du skickar en lokal källa mapp till den `az acr build` kommandot, den `.git` mappen är exkluderad från det överförda paketet som standard. Du kan skapa en `.dockerignore` fil med följande inställning. Kommandot för att återställa alla filer under meddelar `.git` i det överförda paketet. 

```
!.git/**
```

Den här inställningen gäller även för de `az acr run` kommando.

## <a name="cicd-integration"></a>Integration av CI/CD

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub-åtgärder](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)


## <a name="next-steps"></a>Nästa steg

* [Läs mer](container-registry-intro.md) om Azure Container Registry.

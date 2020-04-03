---
title: Vanliga frågor och svar
description: Svar på vanliga frågor som rör azure-behållarregistertjänsten
author: sajayantony
ms.topic: article
ms.date: 03/18/2020
ms.author: sajaya
ms.openlocfilehash: 7452b5dd3c952a13a28566914d2fe513689d4751
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618800"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Vanliga frågor och svar om Azure Container Registry

Den här artikeln tar upp vanliga frågor och kända problem om Azure Container Registry.

## <a name="resource-management"></a>Resurshantering

- [Kan jag skapa ett Azure-behållarregister med hjälp av en Resource Manager-mall?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Finns det säkerhetsproblem skanning för bilder i ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Hur konfigurerar jag Kubernetes med Azure Container Registry?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Hur får jag administratörsautentiseringsuppgifter för ett behållarregister?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Hur får jag administratörsautentiseringsuppgifter i en Resource Manager-mall?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Borttagning av replikering misslyckas med förbjuden status även om replikeringen tas bort med Azure CLI eller Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [Brandväggsreglerna uppdateras men de börjar inte gälla](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Kan jag skapa ett Azure Container-register med hjälp av en Resource Manager-mall?

Ja. Här är [en mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) som du kan använda för att skapa ett register.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Finns det säkerhetsproblem skanning för bilder i ACR?

Ja. Se dokumentationen från [Azure Security Center,](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration) [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) och [Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Hur konfigurerar jag Kubernetes med Azure Container Registry?

Se dokumentationen för [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) och stegen för [Azure Kubernetes Service](../aks/cluster-container-registry-integration.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Hur får jag administratörsautentiseringsuppgifter för ett behållarregister?

> [!IMPORTANT]
> Administratörsanvändarkontot är utformat för att en enskild användare ska komma åt registret, främst för testning. Vi rekommenderar inte att du delar autentiseringsuppgifterna för administratörskontot med flera användare. Individuell identitet rekommenderas för användare och tjänsthuvudnamn för huvudlösa scenarier. Se [Översikt över autentisering](container-registry-authentication.md).

Innan du skaffar administratörsautentiseringsuppgifter kontrollerar du att registrets administratörsanvändare är aktiverad.

Så här hämtar du autentiseringsuppgifter med Azure CLI:

```azurecli
az acr credential show -n myRegistry
```

Använda Azure Powershell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Hur får jag administratörsautentiseringsuppgifter i en Resource Manager-mall?

> [!IMPORTANT]
> Administratörsanvändarkontot är utformat för att en enskild användare ska komma åt registret, främst för testning. Vi rekommenderar inte att du delar autentiseringsuppgifterna för administratörskontot med flera användare. Individuell identitet rekommenderas för användare och tjänsthuvudnamn för huvudlösa scenarier. Se [Översikt över autentisering](container-registry-authentication.md).

Innan du skaffar administratörsautentiseringsuppgifter kontrollerar du att registrets administratörsanvändare är aktiverad.

Så här får du det första lösenordet:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Så här får du det andra lösenordet:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Borttagning av replikering misslyckas med förbjuden status även om replikeringen tas bort med Azure CLI eller Azure PowerShell

Felet visas när användaren har behörighet i ett register men inte har behörighet på läsarnivå för prenumerationen. Lös problemet genom att tilldela läsarbehörighet för prenumerationen till användaren:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>Brandväggsreglerna uppdateras men de börjar inte gälla

Det tar lite tid att sprida ändringar brandväggsregel. När du har ändrat brandväggsinställningarna väntar du några minuter innan du verifierar ändringen.


## <a name="registry-operations"></a>Registeråtgärder

- [Hur kommer jag åt Docker Registry HTTP API V2?](#how-do-i-access-docker-registry-http-api-v2)
- [Hur tar jag bort alla manifest som inte refereras av någon tagg i en databas?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Varför minskar inte användningen av registerkvoter när avbildningar har tagits bort?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Hur validerar jag ändringar av lagringskvoter?](#how-do-i-validate-storage-quota-changes)
- [Hur autentiserar jag med mitt register när jag kör CLI i en behållare?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Hur aktiverar du TLS 1.2?](#how-to-enable-tls-12)
- [Stöder Azure Container Registry Content Trust?](#does-azure-container-registry-support-content-trust)
- [Hur ger jag åtkomst till pull- eller push-avbildningar utan behörighet att hantera registerresursen?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Hur aktiverar jag automatisk avbildningskarantän för ett register?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [Hur aktiverar jag anonym pull-åtkomst?](#how-do-i-enable-anonymous-pull-access)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Hur kommer jag åt Docker Registry HTTP API V2?

ACR stöder Docker Registry HTTP API V2. API:erna kan nås `https://<your registry login server>/v2/`på . Exempel: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Hur tar jag bort alla manifest som inte refereras av någon tagg i en databas?

Om du är på bash:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

För Powershell:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Du kan lägga `-y` till i kommandot ta bort för att hoppa över bekräftelsen.

Mer information finns [i Ta bort behållaravbildningar i Azure Container Registry](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Varför minskar inte användningen av registerkvoter när avbildningar har tagits bort?

Den här situationen kan inträffa om de underliggande lagren fortfarande refereras av andra behållaravbildningar. Om du tar bort en avbildning utan referenser uppdateras registeranvändningen på några minuter.

### <a name="how-do-i-validate-storage-quota-changes"></a>Hur validerar jag ändringar av lagringskvoter?

Skapa en bild med ett 1 GB-lager med hjälp av följande dockerfil. Detta säkerställer att avbildningen har ett lager som inte delas av någon annan avbildning i registret.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Skapa och skicka avbildningen till registret med docker CLI.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Du bör kunna se att lagringsanvändningen har ökat i Azure-portalen eller så kan du fråga användning med CLI.

```azurecli
az acr show-usage -n myregistry
```

Ta bort avbildningen med Hjälp av Azure CLI eller portalen och kontrollera den uppdaterade användningen på några minuter.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Hur autentiserar jag med mitt register när jag kör CLI i en behållare?

Du måste köra Azure CLI-behållaren genom att montera Docker-socket:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

Installera i `docker`behållaren:

```bash
apk --update add docker
```

Autentisera sedan med registret:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>Hur aktiverar du TLS 1.2?

Aktivera TLS 1.2 med hjälp av en nyligen dockerklient (version 18.03.0 och senare). 

> [!IMPORTANT]
> Från och med den 13 januari 2020 kräver Azure Container Registry alla säkra anslutningar från servrar och program för att använda TLS 1.2. Stöd för TLS 1.0 och 1.1 kommer att dras tillbaka.

### <a name="does-azure-container-registry-support-content-trust"></a>Stöder Azure Container Registry Content Trust?

Ja, du kan använda betrodda avbildningar i Azure Container Registry, eftersom [Docker Notary](https://docs.docker.com/notary/getting_started/) har integrerats och kan aktiveras. Mer information finns [i Content Trust i Azure Container Registry](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Var finns filen för tumavtrycket?

Under `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* Offentliga nycklar och certifikat för alla roller (utom `root.json`delegeringsroller) lagras i .
* Offentliga nycklar och certifikat för delegeringsrollen lagras i JSON-filen för dess överordnade roll (till exempel `targets.json` för `targets/releases` rollen).

Det föreslås att dessa offentliga nycklar och certifikat kontrolleras efter den övergripande TUF-verifieringen som gjorts av Docker- och Notarieklienten.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Hur ger jag åtkomst till pull- eller push-avbildningar utan behörighet att hantera registerresursen?

ACR stöder [anpassade roller](container-registry-roles.md) som ger olika behörighetsnivåer. Specifikt och `AcrPull` `AcrPush` roller tillåter användare att hämta och/eller skicka avbildningar utan behörighet att hantera registerresursen i Azure.

* Azure-portal: Ditt register -> IAM (Access Control) -> Lägg `AcrPull` `AcrPush` till (Välj eller för rollen).
* Azure CLI: Hitta registrets resurs-ID genom att köra följande kommando:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Sedan kan du `AcrPull` `AcrPush` tilldela en användare rollen eller `AcrPull`rollen (följande exempel används):

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  Du kan också tilldela rollen till en tjänstprincip som identifieras av dess program-ID:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

Den som tilldelats kan sedan autentisera och komma åt avbildningar i registret.

* Så här autentiserar du till ett register:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Så här listar du databaser:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Så här hämtar du en bild:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

Med hjälp av `AcrPull` endast `AcrPush` rollen eller har förvärvaren inte behörighet att hantera registerresursen i Azure. Till `az acr list` exempel, `az acr show -n myRegistry` eller kommer inte att visa registret.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Hur aktiverar jag automatisk avbildningskarantän för ett register?

Bildkarantän är för närvarande en förhandsgranskningsfunktion i ACR. Du kan aktivera karantänläget för ett register så att endast de avbildningar som har klarat säkerhetsgenomsökningen är synliga för normala användare. Mer information finns i [REPO-repoen ACR GitHub](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

### <a name="how-do-i-enable-anonymous-pull-access"></a>Hur aktiverar jag anonym pull-åtkomst?

Att konfigurera ett Azure-behållarregister för anonym (offentlig) pull-åtkomst är för närvarande en förhandsgranskningsfunktion. För att möjliggöra allmänhetens tillgång, https://aka.ms/acr/support/create-ticketvänligen öppna en supportbiljett på . Mer information finns i [Azure Feedback Forum](https://feedback.azure.com/forums/903958-azure-container-registry/suggestions/32517127-enable-anonymous-access-to-registries).


## <a name="diagnostics-and-health-checks"></a>Diagnostik och hälsokontroller

- [Kontrollera hälsa med`az acr check-health`](#check-health-with-az-acr-check-health)
- [docker pull misslyckas med fel: net/http: begäran avbröts i väntan på anslutning (Client.Timeout överskrids i väntan på rubriker)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker push lyckas men docker pull misslyckas med fel: obehörig: autentisering krävs](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login`lyckas, men docker-kommandon misslyckas med fel: obehörig: autentisering krävs](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [Aktivera och hämta debug loggarna för docker daemon](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [Nya användarbehörigheter kanske inte träder i kraft direkt efter uppdateringen](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Autentiseringsinformation ges inte i rätt format på direkta REST API-anrop](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Varför listar inte Azure-portalen alla mina databaser eller taggar?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Varför kan Azure-portalen inte hämta databaser eller taggar?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Varför misslyckas min pull- eller push-begäran med otillåten åtgärd?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Hur samlar jag in http-spår i Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Kontrollera hälsa med`az acr check-health`

Information om felsökning av vanliga miljö- och registerproblem finns [i Kontrollera hälsotillståndet för ett Azure-behållarregister](container-registry-check-health.md).

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull misslyckas med fel: net/http: begäran avbröts i väntan på anslutning (Client.Timeout överskrids i väntan på rubriker)

 - Om det här felet är ett tillfälligt problem kommer ett nytt försök att lyckas.
 - Om `docker pull` misslyckas kontinuerligt, kan det finnas ett problem med Docker demon. Problemet kan i allmänhet minskas genom att starta om Docker-demonen. 
 - Om du fortsätter att se det här problemet efter omstart av Docker-demon kan problemet bero på problem med nätverksanslutningen med datorn. Om du vill kontrollera om det allmänna nätverket på datorn är felfritt kör du följande kommando för att testa slutpunktsanslutningen. Den `az acr` minsta versionen som innehåller det här kommandot för anslutningskontroll är 2.2.9. Uppgradera din Azure CLI om du använder en äldre version.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - Du bör alltid ha en mekanism för återförsök på alla Docker-klientåtgärder.

### <a name="docker-pull-is-slow"></a>Docker drag är långsam
Använd [det här](http://www.azurespeed.com/Azure/Download) verktyget för att testa nedladdningshastigheten för maskinnätverket. Om datornätverket är långsamt kan du överväga att använda Azure VM i samma region som registret. Detta ger dig vanligtvis snabbare nätverkshastighet.

### <a name="docker-push-is-slow"></a>Docker push är långsam
Använd [det här](http://www.azurespeed.com/Azure/Upload) verktyget för att testa uppladdningshastigheten för maskinnätverket. Om datornätverket är långsamt kan du överväga att använda Azure VM i samma region som registret. Detta ger dig vanligtvis snabbare nätverkshastighet.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker-push lyckas men docker pull misslyckas med fel: obehörig: autentisering krävs

Det här felet kan inträffa med Red Hat-versionen `--signature-verification` av Docker-demonen, där är aktiverad som standard. Du kan kontrollera dockerdemonalternativen för Red Hat Enterprise Linux (RHEL) eller Fedora genom att köra följande kommando:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Fedora 28 Server har till exempel följande dockerdemonalternativ:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

Om `--signature-verification=false` det `docker pull` saknas misslyckas det med ett fel som liknar:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Så här löser du felet:
1. Lägg till `--signature-verification=false` alternativet i konfigurationsfilen `/etc/sysconfig/docker`för Docker-demon . Ett exempel:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Starta om docker-demontjänsten genom att köra följande kommando:
   
   ```bash
   sudo systemctl restart docker.service
   ```

Information `--signature-verification` om kan hittas genom att köra `man dockerd`.

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr-inloggning lyckas men docker misslyckas med fel: obehörig: autentisering krävs

Kontrollera att du använder en alla gemener server URL, till exempel, `docker push myregistry.azurecr.io/myimage:latest`även om `myRegistry`registret resursnamn är versaler eller blandade fall, till exempel .

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Aktivera och hämta felsökningsloggarna för Docker-demonen    

Börja `dockerd` med `debug` alternativet. Skapa först konfigurationsfilen dockerdemon`/etc/docker/daemon.json`( ) om den inte `debug` finns och lägg till alternativet:

```json
{    
    "debug": true    
}
```

Starta sedan om demonen. Till exempel med Ubuntu 14,04:

```bash
sudo service docker restart
```

Information finns i [Docker-dokumentationen](https://docs.docker.com/engine/admin/#enable-debugging).    

 * Loggarna kan genereras på olika platser, beroende på ditt system. Till exempel, för Ubuntu 14,04, det är `/var/log/upstart/docker.log`.    
Mer information finns i [Docker-dokumentationen.](https://docs.docker.com/engine/admin/#read-the-logs)    

 * För Docker för Windows genereras loggarna under %LOCALAPPDATA%/docker/. Men det kanske inte innehåller all felsökningsinformation ännu.    

   För att komma åt hela demonloggen kan du behöva några extra steg:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Nu har du tillgång till alla `dockerd`filer på den virtuella datorn som körs . Loggen är `/var/log/docker.log`på .

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Nya användarbehörigheter kanske inte träder i kraft direkt efter uppdateringen

När du beviljar nya behörigheter (nya roller) till ett tjänsthuvudnamn kanske ändringen inte börjar gälla omedelbart. Det finns två möjliga orsaker:

* Azure Active Directory-rolltilldelningsfördröjning. Normalt är det snabbt, men det kan ta minuter på grund av förökningsfördröjning.
* Behörighetsfördröjning på ACR-tokenserver. Det kan ta upp till 10 minuter. För att minska `docker logout` kan du och sedan autentisera igen med samma användare efter 1 minut:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

För närvarande stöder ACR inte borttagning av hemreplikering av användarna. Lösningen är att inkludera hemreplikeringsskapandet i mallen men hoppa över dess skapande genom att lägga till `"condition": false` som visas nedan:

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

Du kan `InvalidAuthenticationInfo` stöta på ett `curl` fel, `-L`särskilt `--location` med hjälp av verktyget med alternativet , (för att följa omdirigeringar).
Hämta bloben med `curl` `-L` alternativet och grundläggande autentisering:

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

Grundorsaken är `curl` att vissa implementeringar följer omdirigeringar med rubriker från den ursprungliga begäran.

För att lösa problemet måste du följa omdirigeringar manuellt utan rubrikerna. Skriv ut svarsrubrikerna med `-D -` alternativet `curl` att `Location` extrahera och extrahera sedan: sidhuvudet:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Varför listar inte Azure-portalen alla mina databaser eller taggar? 

Om du använder webbläsaren Microsoft Edge/IE kan du se högst 100 databaser eller taggar. Om registret har fler än 100 databaser eller taggar rekommenderar vi att du använder webbläsaren Firefox eller Chrome för att lista dem alla.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Varför kan Azure-portalen inte hämta databaser eller taggar?

Webbläsaren kanske inte kan skicka begäran om att hämta databaser eller taggar till servern. Det kan finnas olika skäl, t.ex.

* Brist på nätverksanslutning
* Brandvägg
* Annonsblockerare
* DNS-fel

Kontakta nätverksadministratören eller kontrollera nätverkskonfigurationen och anslutningen. Prova `az acr check-health -n yourRegistry` att köra med din Azure CLI för att kontrollera om din miljö kan ansluta till behållarregistret. Dessutom kan du också prova en inkognito eller privat session i din webbläsare för att undvika inaktuella webbläsarens cacheminne eller cookies.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Varför misslyckas min pull- eller push-begäran med otillåten åtgärd?

Här är några scenarier där åtgärder kanske inte tillåts:
* Klassiska register stöds inte längre. Uppgradera till [sku:er som](https://aka.ms/acr/skus) stöds med az [acr-uppdateringen](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-update) eller Azure-portalen.
* Bilden eller databasen kanske är låst så att den inte kan tas bort eller uppdateras. Du kan använda kommandot [az acr visa databas](https://docs.microsoft.com/azure/container-registry/container-registry-image-lock) för att visa aktuella attribut.
* Vissa åtgärder tillåts inte om avbildningen är i karantän. Läs mer om [karantän](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

### <a name="how-do-i-collect-http-traces-on-windows"></a>Hur samlar jag in http-spår i Windows?

#### <a name="prerequisites"></a>Krav

- Aktivera dekryptering https i spelman:<https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Gör det möjligt för Docker att använda en proxy via Docker-ui:<https://docs.docker.com/docker-for-windows/#proxies>
- Var noga med att återgå när du är klar.  Docker fungerar inte med den här aktiverade och spelmannen körs inte.

#### <a name="windows-containers"></a>Windows-containrar

Konfigurera Docker-proxy till 127.0.0.1:8888

#### <a name="linux-containers"></a>Linux-containrar

Hitta ip för den virtuella växeln Docker vm:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Konfigurera Docker-proxyn till utdata från föregående kommando och porten 8888 (till exempel 10.0.75.1:8888)

## <a name="tasks"></a>Aktiviteter

- [Hur batch avbryter jag körningar?](#how-do-i-batch-cancel-runs)
- [Hur tar jag med .git-mappen i az acr build-kommandot?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [Stöder Uppgifter GitLab för källutlösare?](#does-tasks-support-gitlab-for-source-triggers)
- [Vilken git-databashanteringstjänst stöder Uppgifter?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Hur batch avbryter jag körningar?

Följande kommandon avbryter alla aktiviteter som körs i det angivna registret.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Hur tar jag med .git-mappen i az acr build-kommandot?

Om du skickar en lokal `az acr build` källmapp `.git` till kommandot utesluts mappen som standard från det uppladdade paketet. Du kan `.dockerignore` skapa en fil med följande inställning. Det talar om för kommandot `.git` att återställa alla filer under i det uppladdade paketet. 

`!.git/**`

Den här inställningen `az acr run` gäller även för kommandot.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>Stöder Uppgifter GitLab för källutlösare?

Vi stöder för närvarande inte GitLab för source-utlösare.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Vilken git-databashanteringstjänst stöder Uppgifter?

| Git-tjänst | Källkontext | Manuell konstruktion | Automatisk version genom commit-utlösare |
|---|---|---|---|
| GitHub | https://github.com/user/myapp-repo.git#mybranch:myfolder | Ja | Ja |
| Azure-lagringsplatser | https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder | Ja | Ja |
| GitLab | https://gitlab.com/user/myapp-repo.git#mybranch:myfolder | Ja | Inga |
| BitBucket () | https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder | Ja | Inga |

## <a name="run-error-message-troubleshooting"></a>Kör felsökning av felmeddelanden

| Felmeddelande | Felsökningsguide |
|---|---|
|Ingen åtkomst har konfigurerats för den virtuella datorn, varför inga prenumerationer hittades|Detta kan inträffa om `az login --identity` du använder i din ACR-uppgift. Detta är ett tillfälligt fel och inträffar när rolltilldelningen av den hanterade identiteten inte har spridits. Väntar några sekunder innan försök fungerar igen.|

## <a name="cicd-integration"></a>CI/CD-integrering

- [CircleCI (CirCI)](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub-åtgärder](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Nästa steg

* [Läs mer](container-registry-intro.md) om Azure Container Registry.

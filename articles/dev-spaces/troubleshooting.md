---
title: Felsöka
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/25/2019
ms.topic: conceptual
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s '
ms.openlocfilehash: e145c234c7fc0bc7b9263f40f22d3fd90c1b7250
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064117"
---
# <a name="troubleshooting-guide"></a>Felsökningsguide

Den här guiden innehåller information om vanliga problem som kan uppstå när du använder Azure dev Spaces.

Om du har problem med att använda Azure dev Spaces kan du skapa ett [problem i Azure dev Spaces GitHub-lagringsplatsen](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Innan du börjar

För att felsöka problem på ett effektivare sätt kan det hjälpa att skapa mer detaljerade loggar för granskning.

För Visual Studio-tillägget anger du `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED`-miljövariabeln till 1. Se till att starta om Visual Studio för att miljö variabeln ska börja gälla. När den är aktive rad skrivs detaljerade loggar till din `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` katalog.

I CLI kan du mata ut mer information under kommando körningen med hjälp av växeln `--verbose`. Du kan också söka efter mer detaljerade loggar i `%TEMP%\Azure Dev Spaces`. På en Mac kan du hitta TEMP-katalogen genom att köra `echo $TMPDIR` från ett terminalfönster. På en Linux-dator är TEMP-katalogen vanligt vis `/tmp`.

Azure dev Spaces fungerar också bäst vid fel sökning av en enskild instans eller pod. `azds.yaml`-filen innehåller en inställning, *replicaCount*, som anger antalet poddar som Kubernetes körs för tjänsten. Om du ändrar *replicaCount* för att konfigurera programmet så att det kör flera poddar för en specifik tjänst, bifogas fel söknings programmet till de första pod, i alfabetisk ordning. Fel söknings programmet ansluts till en annan Pod när den ursprungliga Pod återanvänds, vilket kan resultera i ett oväntat beteende.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Vanliga problem när du aktiverar Azure dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Fel "Det gick inte att skapa Azure dev Spaces-kontrollant"

Du kanske ser det här felet när något går fel när du skapar kontrollanten. Om det är ett tillfälligt fel tar du bort och återskapar styrenheten för att åtgärda det.

Du kan också prova att ta bort kontroll enheten:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Använd Azure dev Spaces CLI för att ta bort en kontrollant. Det går inte att ta bort en kontrollant från Visual Studio. Du kan inte heller installera Azure dev Spaces CLI i Azure Cloud Shell så att du inte kan ta bort en kontrollant från Azure Cloud Shell.

Om du inte har installerat Azure dev Spaces CLI kan du först installera det med hjälp av följande kommando och sedan ta bort din styrenhet:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Du kan återskapa kontroll enheten från CLI eller Visual Studio. Se [team utvecklingen](quickstart-team-development.md) eller [utveckla med .net Core](quickstart-netcore-visualstudio.md) -snabb starter för exempel.

### <a name="controller-create-failing-because-of-controller-name-length"></a>Styrenheten kunde inte skapas på grund av kontrollantens namn längd

Ett namn på en Azure dev-enhet får inte vara längre än 31 tecken. Om namnet på din kontrollant överstiger 31 tecken när du aktiverar dev Spaces i ett AKS-kluster eller skapar en kontrollant får du ett fel meddelande. Exempel:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Åtgärda problemet genom att skapa en kontrollant med ett alternativt namn. Exempel:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Det går inte att aktivera dev-utrymmen när Windows-nodkonfigurationer läggs till i ett AKS-kluster

För närvarande är Azure dev Spaces endast avsett att köras på Linux-poddar och noder. När du har ett AKS-kluster med en Windows-adresspool måste du se till att Azure dev Spaces-poddar bara är schemalagda på Linux-noder. Om en Azure dev Spaces-pod är schemalagd att köras på en Windows-nod kommer Pod inte att starta och inte aktivera dev-utrymmen.

Åtgärda problemet genom att [lägga till en-utsmak](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) till ditt AKS-kluster så att Linux-poddar inte är schemalagda att köras på en Windows-nod.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Fel "hittade inga icke-förorenade Linux-noder i klart läge i klustret. Det måste finnas minst en azds Linux-nod i klar läge för att distribuera poddar i namn området.

Azure dev Spaces kunde inte skapa en kontrollant i ditt AKS-kluster eftersom det inte gick att hitta en ej förorenad nod i ett *klart* tillstånd för att schemalägga poddar på. Azure dev Spaces kräver minst en Linux-nod i ett *klart* tillstånd som gör det möjligt att schemalägga poddar utan att ange tolerera.

Åtgärda problemet genom att [Uppdatera din smak konfiguration](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) på ditt AKS-kluster för att säkerställa att minst en Linux-nod tillåter schemaläggning av poddar utan att ange tolererande. Se också till att minst en Linux-nod som tillåter schemaläggning av poddar utan att ange tolererar är *klar* . Om noden tar lång tid att komma *igång* kan du prova att starta om noden.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Fel "Azure dev Spaces-CLI har inte installerats korrekt" vid körning `az aks use-dev-spaces`

En uppdatering av Azure dev Spaces CLI ändrade installations Sök vägen. Om du använder en tidigare version av Azure CLI än 2.0.63 kan du se det här felet. Använd `az --version`om du vill visa din version av Azure CLI.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

Trots fel meddelandet när du kör `az aks use-dev-spaces` med en version av Azure CLI före 2.0.63, lyckas installationen. Du kan fortsätta att använda `azds` utan problem.

Åtgärda problemet genom att uppdatera installationen av [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) till 2.0.63 eller senare. Den här uppdateringen löser det fel meddelande som visas när du kör `az aks use-dev-spaces`. Alternativt kan du fortsätta att använda din aktuella version av Azure CLI och Azure dev Spaces CLI.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Vanliga problem vid förberedelse av ditt projekt för Azure dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Varning "Dockerfile kunde inte genereras på grund av ett språk som inte stöds"
Azure dev Spaces innehåller inbyggt stöd C# för och Node. js. När du kör `azds prep` i en katalog med kod skrivet på något av dessa språk skapar Azure dev Spaces automatiskt en lämplig Dockerfile åt dig.

Du kan fortfarande använda Azure dev Spaces med kod som skrivits på andra språk, men du måste skapa Dockerfile manuellt innan du kör `azds up` för första gången.

Om ditt program är skrivet på ett språk som Azure dev Spaces inte har inbyggt stöd för, måste du ange en lämplig Dockerfile för att skapa en behållar avbildning som kör din kod. Docker innehåller en [lista över metod tips för att skriva Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) och en [Dockerfile-referens](https://docs.docker.com/engine/reference/builder/) som kan hjälpa dig att skriva en Dockerfile som passar dina behov.

När du har en lämplig Dockerfile på plats kör du `azds up` för att köra programmet i Azure dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Vanliga problem vid start eller stopp av tjänster med Azure dev Spaces

### <a name="error-config-file-not-found"></a>Fel: det gick inte att hitta konfigurations filen:

När du kör `azds up`kan du se det här felet. Både `azds up` och `azds prep` måste köras från rot katalogen för det projekt som du vill köra i ditt dev-utrymme.

Så här åtgärdar du problemet:
1. Ändra din aktuella katalog till rotmappen som innehåller tjänst koden. 
1. Om du inte har en _azds. yaml_ -fil i kodvyn kör `azds prep` för att skapa Docker-, Kubernetes-och Azure dev Spaces-tillgångar.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Timeout vid "väntar på att behållar avbildningen byggs..." steg med virtuella AKS-noder

Denna tids gräns inträffar när du försöker använda dev Spaces för att köra en tjänst som är konfigurerad för att köras på en [virtuell AKS-nod](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Dev Spaces stöder för närvarande inte skapande eller fel sökning av tjänster på virtuella noder.

Om du kör `azds up` med växeln `--verbose` eller aktiverar utförlig loggning i Visual Studio visas ytterligare information:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Kommandot ovan visar att tjänstens Pod har tilldelats till *virtuell-Node-ACI-Linux*, som är en virtuell nod.

Åtgärda problemet genom att uppdatera Helm-diagrammet för tjänsten för att ta bort avsöknings *-eller* *tolerera* värden som tillåter att tjänsten körs på en virtuell nod. Dessa värden definieras vanligt vis i diagrammets `values.yaml`-fil.

Du kan fortfarande använda ett AKS-kluster som har funktionen virtuella noder aktiverade, om den tjänst som du vill bygga eller felsöka i dev Spaces körs på en VM-nod. Att köra en tjänst med dev Spaces på en VM-nod är standard konfigurationen.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Fel "Det gick inte att hitta en färdig till gång pod" vid start av dev Spaces

Det här felet uppstår om Helm-klienten inte längre kommunicerar med till-Pod som körs i klustret.

Åtgärda problemet genom att starta om agent-noderna i klustret.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Fel "release azds-\<Identifier\>-\<spacename\>-\<ServiceName\> misslyckades: tjänsterna\<ServiceName\>finns redan eller så nekas åtkomst åtkomst för \<ServiceName\>, lagrings platsen finns inte eller kan kräva "Docker inloggning" "

Felen kan uppstå om du blandar kör direkta Helm-kommandon (till exempel `helm install`, `helm upgrade`eller `helm delete`) med dev Spaces-kommandon (till exempel `azds up` och `azds down`) inuti samma dev-utrymme. De inträffar eftersom dev Spaces har sin egen till-instans, som står i konflikt med din egen till-instans som körs i samma dev-utrymme.

Det är bra att använda både Helm-kommandon och dev Spaces-kommandon mot samma AKS-kluster, men varje dev Spaces-aktiverad namnrymd bör använda antingen ett eller ett annat.

Anta till exempel att du använder ett Helm-kommando för att köra hela programmet i ett överordnat dev-utrymme. Du kan skapa underordnade dev-utrymmen från överordnad, använda dev Spaces för att köra enskilda tjänster i de underordnade dev Spaces och testa tjänsterna tillsammans. När du är redo att kontrol lera ändringarna använder du ett Helm-kommando för att distribuera den uppdaterade koden till det överordnade dev-utrymmet. Använd inte `azds up` för att köra den uppdaterade tjänsten i det överordnade dev-utrymmet, eftersom den kommer att stå i konflikt med att tjänsten ursprungligen kördes med Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Befintliga Dockerfile används inte för att bygga en behållare

Azure dev Spaces kan konfigureras så att de pekar på en viss _Dockerfile_ i ditt projekt. Om det verkar som om Azure dev Spaces inte använder den _Dockerfile_ som du förväntar dig att bygga dina behållare, kan du uttryckligen behöva berätta om Azure dev Spaces som Dockerfile ska använda. 

Lös problemet genom att öppna den _azds. yaml_ -fil som Azure dev-utrymmen genererar i projektet. Uppdatera *konfigurationer: utveckla: skapa: Dockerfile* för att peka på den Dockerfile som du vill använda. Exempel:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Fel "obehörig: autentisering krävs" vid försök att använda en Docker-avbildning från ett privat register

Du använder en Docker-avbildning från ett privat register som kräver autentisering.

Du kan åtgärda det här problemet genom att låta dev-utrymmen autentisera och hämta bilder från det privata registret med [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Om du vill använda imagePullSecrets [skapar du en Kubernetes-hemlighet](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) i namn området där du använder avbildningen. Ange sedan hemligheten som en imagePullSecret i `azds.yaml`.

Nedan visas ett exempel på hur du anger imagePullSecrets i `azds.yaml`.

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> Om du anger imagePullSecrets i `azds.yaml` åsidosätts imagePullSecrets som anges i `values.yaml`.

### <a name="error-service-cannot-be-started"></a>Fel "det går inte att starta tjänsten".

Du kanske ser det här felet när det inte går att starta en tjänst kod. Orsaken är ofta i användar kod. Om du vill ha mer diagnostikinformation aktiverar du mer detaljerad loggning när du startar tjänsten.

Använd `--verbose` för att aktivera mer detaljerad loggning från kommando raden. Du kan också ange ett format för utdata med `--output`. Exempel:

```cmd
azds up --verbose --output json
```

I Visual Studio:

1. Öppna **verktyg > alternativ** och under **projekt och lösningar**väljer du **skapa och kör**.
2. Ändra inställningarna för **MSBuild Project build-utdata utförligt** till **detaljerat** eller **diagnostik**.

    ![Skärm bild av dialog rutan verktyg alternativ](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Köra en tjänst igen efter att styrenheten har skapats på nytt

Du får ett fel meddelande om att *tjänsten inte kan startas* när du försöker köra om en tjänst när du har tagit bort och återskapat den Azure dev Spaces-styrenhet som är associerad med det här klustret. I den här situationen innehåller utförliga utdata följande text:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Det här felet uppstår eftersom borttagning av dev Spaces-styrenheten inte tar bort tjänster som tidigare installerats av kontrollanten. Om du återskapar kontrollanten och sedan försöker köra tjänsterna med hjälp av den nya styrenheten Miss lyckas det eftersom de gamla tjänsterna fortfarande är på plats.

Åtgärda problemet genom att använda kommandot `kubectl delete` för att manuellt ta bort de gamla tjänsterna från klustret, och kör sedan dev Spaces för att installera de nya tjänsterna.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Fel "det går inte att starta tjänsten". När du använder Dockerfiles med flera steg

Du får ett fel meddelande om att det *inte går att starta tjänsten* när du använder en Dockerfile med flera steg. I den här situationen innehåller utförliga utdata följande text:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Det här felet beror på att AKS-noder kör en äldre version av Docker som inte har stöd för flera stegs versioner. Om du vill undvika versioner av flera steg kan du skriva om din Dockerfile.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Vanliga problem med Visual Studio och Visual Studio Code med Azure dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Fel "nödvändiga verktyg och konfigurationer saknas"

Det här felet kan inträffa vid start av VS Code: "[Azure dev Spaces] verktyg och konfigurationer som krävs för att bygga och felsöka [projekt namn] saknas."
Felet innebär att azds. exe inte finns i miljövariabeln PATH, som visas i VS Code.

Försök att starta VS Code från en kommando tolk där miljövariabeln PATH är korrekt inställd.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Fel "verktyg som krävs för att bygga och felsöka ProjectName är inaktuella."

Du ser det här felet i Visual Studio Code om du har en nyare version av VS Code-tillägget för Azure dev Spaces, men en äldre version av Azure dev Spaces CLI.

Försök att hämta och installera den senaste versionen av Azure dev Spaces CLI:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Fel: "Det gick inte att hitta fel söknings tillägg för typ: coreclr"

Du kanske ser det här felet när du kör fel söknings programmet Visual Studio Code. Du kanske inte har VS Code-tillägget C# installerat på din utvecklings dator. C# Tillägget innehåller stöd för fel sökning av .net Core (CoreCLR).

Lös problemet genom att installera [vs Code-tillägget för C# ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Fel "konfigurerad fel söknings typ" coreclr "stöds inte"

Du kanske ser det här felet när du kör fel söknings programmet Visual Studio Code. Du kanske inte har VS Code-tillägget för Azure dev Spaces installerat på din utvecklings dator.

Lös problemet genom att installera [vs Code-tillägget för Azure dev Spaces](get-started-netcore.md).

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Fel "ogiltigt ' CWD '-värde '/src '. Det går inte att hitta den angivna filen i systemet. " eller "starta: program"/src/[sökväg till projektets binärfil] finns inte "

Du kanske ser det här felet när du kör fel söknings programmet Visual Studio Code. Som standard använder VS Code-tillägget `src` som arbets katalog för projektet på behållaren. Om du har uppdaterat `Dockerfile` för att ange en annan arbets katalog kan du se det här felet.

Åtgärda problemet genom att uppdatera `launch.json`-filen under katalogen `.vscode` i projektmappen. Ändra `configurations->cwd`-direktivet så att det pekar på samma katalog som den `WORKDIR` som definierats i projektets `Dockerfile`. Du kan också behöva uppdatera `configurations->program`-direktivet också.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Fel meddelandet "Pipe-program" azds "avslutades utan förvarning med koden 126".

Du kanske ser det här felet när du kör fel söknings programmet Visual Studio Code.

Åtgärda problemet genom att stänga och öppna Visual Studio-koden igen. Starta om fel söknings programmet.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Felet "intern Watch misslyckades: se ENOSPC" vid koppling av fel sökning till ett Node. js-program

Felet uppstår när noden som kör Pod med Node. js-programmet som du försöker ansluta till med en fel sökare har överskridit värdet *FS. inotify. Max _user_watches* . I vissa fall [kan standardvärdet *FS. inotify. Max _user_watches* vara för litet för att kunna hantera ett fel söknings program direkt till en POD](https://github.com/Azure/AKS/issues/772).

En tillfällig lösning på det här problemet är att öka värdet för *FS. inotify. Max _user_watches* på varje nod i klustret och starta om noden för att ändringarna ska börja gälla.

## <a name="other-common-issues"></a>Andra vanliga problem

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Felet "azds" känns inte igen som ett internt eller externt kommando, fungerande program eller en kommando fil

Det här felet kan inträffa om `azds.exe` inte är installerat eller korrekt konfigurerat.

Så här åtgärdar du problemet:

1. Kontrol lera platsen% ProgramFiles%/Microsoft SDKs\Azure\Azure dev Spaces CLI för `azds.exe`. Om det är där, lägger du till platsen i miljövariabeln PATH.
2. Om `azds.exe` inte är installerat kör du följande kommando:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Auktoriseringsfel "Microsoft. DevSpaces/register/Action"

Du behöver *ägar* -eller *deltagar* åtkomst i din Azure-prenumeration för att kunna hantera Azure dev Spaces. Om du försöker hantera dev Spaces och du inte har *ägare* eller *deltagar* åtkomst till den associerade Azure-prenumerationen kan du se ett auktoriseringsfel. Exempel:

```console
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Du kan åtgärda det här problemet genom att använda ett konto med *ägar* -eller *deltagar* åtkomst till Azure-prenumerationen genom att registrera `Microsoft.DevSpaces` namn rymden manuellt:

```console
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Nya poddar startar inte

Kubernetes-initieraren kan inte använda PodSpec för nya poddar på grund av RBAC-behörighet ändringar i rollen *kluster-admin* i klustret. Den nya Pod kan också ha en ogiltig PodSpec, till exempel det tjänst konto som är kopplat till Pod inte längre finns. Om du vill se poddar som är i ett *väntande* tillstånd på grund av problem med initieraren använder du kommandot `kubectl get pods`:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Det här problemet kan påverka poddar i *alla namn områden* i klustret, inklusive namn rymder där Azure dev Spaces inte är aktiverat.

Åtgärda problemet genom att [Uppdatera dev Spaces CLI till den senaste versionen](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) och sedan ta bort *azds-InitializerConfiguration* från Azure dev Spaces-styrenheten:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

När du har tagit bort *azds-InitializerConfiguration* från Azure dev Spaces-kontrollanten använder du `kubectl delete` för att ta bort eventuella poddar i ett *väntande* tillstånd. När alla väntande poddar har tagits bort distribuerar du om din poddar.

Om nya poddar fortfarande fastnar i ett *väntande* tillstånd efter en distribution, använder du `kubectl delete` för att ta bort eventuella poddar i ett *väntande* tillstånd. När alla väntande poddar har tagits bort tar du bort kontrollanten från klustret och installerar om den:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

När din styrenhet har installerats om distribuerar du din poddar.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Felaktiga RBAC-behörigheter för att anropa dev Spaces-styrenhet och API: er

Användaren som använder Azure dev Spaces-styrenheten måste ha åtkomst för att kunna läsa administratörs *kubeconfig* i AKS-klustret. Den här behörigheten är till exempel tillgänglig i den [inbyggda administratörs rollen för Azure Kubernetes service-klustret](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). Användaren som ansluter till Azure dev Spaces-kontrollanten måste också ha rollen *deltagare* eller *ägare* RBAC för kontrollanten. Mer information om hur du uppdaterar en användares behörigheter för ett AKS-kluster finns [här](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Så här uppdaterar du användarens RBAC-roll för kontrollanten:

1. Logga in på Azure Portal på https://portal.azure.com.
1. Navigera till resurs gruppen som innehåller kontrollanten, som vanligt vis är samma som ditt AKS-kluster.
1. Aktivera kryss rutan *Visa dolda typer* .
1. Klicka på kontroll enheten.
1. Öppna fönstret *Access Control (IAM)* .
1. Klicka på fliken *roll tilldelningar* .
1. Klicka på *Lägg till* och *Lägg sedan till roll tilldelning*.
    * För *roll*väljer du antingen *deltagare* eller *ägare*.
    * För *tilldela åtkomst till*väljer du *Azure AD-användare, grupp eller tjänstens huvud namn*.
    * För *Välj*söker du efter den användare som du vill ge behörighet.
1. Klicka på *Save* (Spara).

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>DNS-namnmatchning Miss lyckas för en offentlig URL som är associerad med en dev Spaces-tjänst

Du kan konfigurera en offentlig URL-slutpunkt för din tjänst genom att ange `--public` växla till kommandot `azds prep` eller genom att markera kryss rutan `Publicly Accessible` i Visual Studio. Det offentliga DNS-namnet registreras automatiskt när du kör tjänsten i dev Spaces. Om det här DNS-namnet inte är registrerat visas en *sida som inte kan visas* eller så *går det inte att nå platsen* i webbläsaren vid anslutning till den offentliga URL: en.

Så här åtgärdar du problemet:

* Kontrol lera status för alla URL: er som är kopplade till dina dev Spaces-tjänster:

  ```console
  azds list-uris
  ```

* Om en URL är i *vänte* läge väntar dev Spaces fortfarande på att DNS-registreringen ska slutföras. Ibland tar det några minuter innan registreringen är klar. I dev Spaces öppnas även en lokal värd för varje tjänst, som du kan använda när du väntar på DNS-registrering.
* Om en URL är i *vänte* läge i mer än 5 minuter kan det tyda på ett problem med den externa DNS-Pod som skapar den offentliga slut punkten eller nginx ingress-styrenhet Pod som hämtar den offentliga slut punkten. Använd följande kommandon för att ta bort dessa poddar och tillåta AKS att automatiskt återskapa dem:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Fel: fel vid anslutning till uppströms anslutning eller från koppling/återställning före huvuden

Du kanske ser det här felet när du försöker komma åt tjänsten. Till exempel när du går till tjänstens URL i en webbläsare. Det här felet innebär att container porten inte är tillgänglig. Detta kan bero på följande:

* Behållaren håller fortfarande på att skapas och distribueras. Det här problemet kan uppstå om du kör `azds up` eller startar fel söknings programmet och sedan försöker komma åt behållaren innan den har distribuerats.
* Port konfigurationen är inte konsekvent i _Dockerfile_, Helm-diagrammet och alla Server koder som öppnar en port.

Så här åtgärdar du problemet:

1. Om behållaren håller på att skapas/distribueras kan du vänta 2-3 sekunder och försöka komma åt tjänsten igen. 
1. Kontrol lera port konfigurationen. De angivna port numren måste vara **identiska** i samtliga följande till gångar:
    * **Dockerfile:** Anges av `EXPOSE` instruktionen.
    * **[Helm-diagram](https://docs.helm.sh):** Anges av `externalPort` och `internalPort` värden för en tjänst (ofta finns i en `values.yml` fil),
    * Alla portar som öppnas i program kod, till exempel i Node. js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Det gick inte att hitta typen eller namn områdets namn "Mina bibliotek"

Det går inte att hitta ett biblioteks projekt som du använder. Med dev Spaces är Bygg kontexten på projekt-/tjänst nivån som standard.  

Så här åtgärdar du problemet:

1. Ändra `azds.yaml`-filen för att ange Bygg kontexten till lösnings nivån.
2. Ändra `Dockerfile`-och `Dockerfile.develop`-filerna för att referera till projektfilerna, till exempel `.csproj`, på rätt sätt i förhållande till den nya bygg kontexten.
3. Lägg till en `.dockerignore` i samma katalog som `.sln`s filen.
4. Uppdatera `.dockerignore` med ytterligare poster efter behov.

Du kan hitta ett exempel [här](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Horisontell Pod-skalning fungerar inte i ett dev-utrymme

När du kör en tjänst i ett dev-utrymme injiceras tjänstens Pod [med ytterligare behållare för Instrumentation](how-dev-spaces-works.md#prepare-your-aks-cluster) och alla behållare i en POD måste ha resurs gränser och begär Anden som ställts in för horisontell Pod autoskalning.

Du kan åtgärda det här problemet genom att använda en resurs förfrågan och begränsa till de inmatade dev Spaces-behållare. Resurs begär Anden och gränser kan tillämpas för den inmatade behållaren (devspaces-proxy) genom att lägga till `azds.io/proxy-resources` kommentar i din POD-spec. Värdet ska anges till ett JSON-objekt som representerar avsnittet resurser i behållar specifikationen för proxyservern.

Nedan visas ett exempel på en proxy-resurs anteckning som ska tillämpas på pod-specifikationen.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Aktivera Azure dev Spaces i ett befintligt namn område med poddar som körs

Du kan ha ett befintligt AKS-kluster och-namnrymd med poddar där du vill aktivera Azure dev Spaces.

Om du vill aktivera Azure dev Spaces i ett befintligt namn område i ett AKS-kluster kör du `use-dev-spaces` och använder `kubectl` för att starta om alla poddar i namn området.

```console
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
kubectl -n my-namespace delete pod --all
```

När din poddar har startats om kan du börja använda din befintliga namnrymd med Azure dev Spaces.
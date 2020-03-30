---
title: Felsökning
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Lär dig hur du felsöker och löser vanliga problem när du aktiverar och använder Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, servicenät, routning av tjänstnät, kubectl, k8s '
ms.openlocfilehash: c12dfd385962d8dd7de8239a0d4ecd46746499c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239772"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Felsökning av Azure Dev Spaces

Den här guiden innehåller information om vanliga problem som du kan ha när du använder Azure Dev Spaces.

Om du har problem när du använder Azure Dev Spaces skapar du ett [problem i Azure Dev Spaces GitHub-databasen](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Innan du börjar

Om du vill felsöka problem mer effektivt kan det bidra till att skapa mer detaljerade loggar för granskning.

För Visual Studio-tillägget `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` ställer du in miljövariabeln till 1. Se till att starta om Visual Studio för att miljövariabeln ska börja gälla. När du har aktiverat skrivs `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` detaljerade loggar till din katalog.

I CLI kan du mata ut mer information `--verbose` under kommandokörning med hjälp av växeln. Du kan också bläddra i `%TEMP%\Azure Dev Spaces`mer detaljerade loggar i . På en Mac kan *TEMP-katalogen* hittas genom att köra `echo $TMPDIR` från ett terminalfönster. På en Linux-dator är `/tmp` *TEMP-katalogen* vanligtvis . Kontrollera dessutom att loggning är aktiverat i konfigurationsfilen för [Azure CLI](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables).

Azure Dev Spaces fungerar också bäst när du felsöker en enskild instans eller pod. Filen `azds.yaml` innehåller en inställning, *replicaCount*, som anger antalet poddar som Kubernetes kör för din tjänst. Om du ändrar *replicaCount* för att konfigurera programmet för att köra flera poddar för en viss tjänst, bifogas felsökaren till den första podden, när den visas alfabetiskt. Felsökaren fäster vid en annan pod när den ursprungliga pod återvinns, vilket kan resultera i oväntat beteende.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Vanliga problem när du aktiverar Azure Dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Fel "Det gick inte att skapa Azure Dev Spaces-styrenheten"

Du kan se det här felet när något går fel med skapandet av styrenheten. Om det är ett tillfälligt fel tar du bort och återskapar styrenheten för att åtgärda det.

Du kan också prova att ta bort handkontrollen:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Använd Azure Dev Spaces CLI för att ta bort en styrenhet. Det går inte att ta bort en handkontroll från Visual Studio. Du kan inte heller installera Azure Dev Spaces CLI i Azure Cloud Shell så att du inte kan ta bort en styrenhet från Azure Cloud Shell.

Om du inte har installerat Azure Dev Spaces CLI kan du först installera det med följande kommando och sedan ta bort handkontrollen:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Återskapa handkontrollen kan göras från CLI eller Visual Studio. Mer om du vill se [snabbstarterna teamutveckling](quickstart-team-development.md) eller [Utveckla med .NET Core.](quickstart-netcore-visualstudio.md)

### <a name="controller-create-failing-because-of-controller-name-length"></a>Controller skapa misslyckas på grund av controller namnlängd

Namnet på Azure Dev Spaces-styrenheten kan inte vara längre än 31 tecken. Om handkontrollens namn överskrider 31 tecken när du aktiverar Dev Spaces i ett AKS-kluster eller skapar en styrenhet visas ett felmeddelande. Ett exempel:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Lös problemet genom att skapa en styrenhet med ett alternativt namn. Ett exempel:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Aktivera dev spaces misslyckas när Windows-nodpooler läggs till i ett AKS-kluster

Azure Dev Spaces är endast avsett att köras på Linux-poddar och noder. När du har ett AKS-kluster med en Windows-nodpool måste du se till att Azure Dev Spaces-poddar endast schemaläggs på Linux-noder. Om en Azure Dev Spaces-pod är schemalagd att köras på en Windows-nod startar inte den podden och aktivera Dev Spaces misslyckas.

Lös problemet genom [att lägga till en färg i](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) AKS-klustret för att säkerställa att Linux-poddar inte är schemalagda att köras på en Windows-nod.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Fel "Hittade inga obefläckade Linux-noder i läget Klar i klustret. Det måste finnas minst en obefläckad Linux-nod i redo tillstånd för att distribuera poddar i "azds" namnområde."

Azure Dev Spaces kunde inte skapa en styrenhet i AKS-klustret eftersom den inte kunde hitta en obefläckad nod i ett *färdigt* tillstånd att schemalägga poddar på. Azure Dev Spaces kräver minst en Linux-nod i ett *färdigt* tillstånd som gör det möjligt att schemalägga poddar utan att ange toleranser.

För att åtgärda problemet [uppdaterar du din färgkonfiguration](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) på AKS-klustret för att säkerställa att minst en Linux-nod tillåter schemaläggning av poddar utan att ange toleranser. Se också till att minst en Linux-nod som tillåter schemaläggning poddar utan att ange toleranser är i *redo* tillstånd. Om det tar lång tid att nå tillståndet *Klar* kan du prova att starta om noden.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Fel "Azure Dev Spaces CLI inte installerat korrekt" när az aks use-dev-spaces körs

En uppdatering av Azure Dev Spaces CLI ändrade sin installationssökväg. Om du använder en version av Azure CLI tidigare än 2.0.63 kan det här felet visas. Om du vill visa din `az --version`version av Azure CLI använder du .

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

Trots felmeddelandet när `az aks use-dev-spaces` du kör med en version av Azure CLI före 2.0.63 lyckas installationen. Du kan fortsätta `azds` att använda utan problem.

Lös problemet genom att uppdatera installationen av [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) till 2.0.63 eller senare. Den här uppdateringen löser felmeddelandet `az aks use-dev-spaces`som visas när du kör . Alternativt kan du fortsätta att använda din nuvarande version av Azure CLI och Azure Dev Spaces CLI.

### <a name="error-unable-to-reach-kube-apiserver"></a>Fel "Det gick inte att nå kube-apiserver"

Det här felet kan visas när Azure Dev Spaces inte kan ansluta till AKS-klustrets API-server. 

Om åtkomsten till AKS-kluster-API-servern är låst eller om du har [behöriga IP-adressintervall för API-servern](../aks/api-server-authorized-ip-ranges.md) aktiverade för AKS-klustret, måste du också [skapa](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) eller [uppdatera](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) klustret så att ytterligare [intervall tillåts baserat på din region](https://github.com/Azure/dev-spaces/tree/master/public-ips).

Kontrollera att API-servern är tillgänglig genom att köra kubectl-kommandon. Om API-servern inte är tillgänglig kontaktar du AKS-supporten och försöker igen när API-servern fungerar.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Vanliga problem när du förbereder projektet för Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Varning "Dockerfile kunde inte genereras på grund av språk som inte stöds"
Azure Dev Spaces ger inbyggt stöd för C# och Node.js. När du `azds prep` kör i en katalog med kod skriven på något av dessa språk skapar Azure Dev Spaces automatiskt en lämplig Dockerfile åt dig.

Du kan fortfarande använda Azure Dev Spaces med kod skriven på andra språk, men `azds up` du måste skapa Dockerfile manuellt innan du kör för första gången.

Om ditt program är skrivet på ett språk som Azure Dev Spaces inte stöder internt måste du ange en lämplig Dockerfile för att skapa en behållaravbildning som kör koden. Docker innehåller en [lista över metodtips för att skriva Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) och en [Dockerfile-referens](https://docs.docker.com/engine/reference/builder/) som kan hjälpa dig att skriva en Dockerfile som passar dina behov.

När du har en lämplig Dockerfile `azds up` på plats kör du för att köra ditt program i Azure Dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Vanliga problem när du startar eller stoppar tjänster med Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Fel "Config-filen hittades inte:"

När `azds up`du kör kan det här felet visas. Båda `azds up` `azds prep` och måste köras från rotkatalogen för det projekt som du vill köra i utvecklingsutrymmet.

Så här åtgärdar du problemet:
1. Ändra den aktuella katalogen till rotmappen som innehåller servicekoden. 
1. Om du inte har en _azds.yaml-fil_ i `azds prep` kodmappen körs du för att generera Docker-, Kubernetes- och Azure Dev Spaces-tillgångar.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Timeout på "Väntar på containeravbildningsversion..." steg med AKS virtuella noder

Den här timeouten inträffar när du försöker använda Dev Spaces för att köra en tjänst som är konfigurerad för att köras på en [aks-virtuell nod](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Dev Spaces stöder för närvarande inte att skapa eller felsöka tjänster på virtuella noder.

Om du `azds up` kör `--verbose` med växeln eller aktiverar utförlig loggning i Visual Studio visas ytterligare information:

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Kommandot ovan visar att tjänstens pod har tilldelats *virtual-nod-aci-linux*, som är en virtuell nod.

Lös problemet genom att uppdatera Helm-diagrammet för tjänsten för att ta bort *alla nodSelector-* eller *tolerationsvärden* som gör att tjänsten kan köras på en virtuell nod. Dessa värden definieras vanligtvis i `values.yaml` diagrammets fil.

Du kan fortfarande använda ett AKS-kluster som har funktionen virtuella noder aktiverad, om tjänsten du vill skapa eller felsöka via Dev Spaces körs på en VM-nod. Köra en tjänst med Dev Spaces på en VM-nod är standardkonfigurationen.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Fel "kunde inte hitta en färdig rorkult pod" när du startar Dev Spaces

Det här felet uppstår om Helm-klienten inte längre kan prata med Tiller-podden som körs i klustret.

Lös problemet genom att starta om agentnoderna i klustret.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Felet "release\<azds-\>-\<identifier spacename\>-\<\> servicename failed: services '\<servicename\>' \<already\>exists" eller "Pull access som nekats för servicenamn , databasen finns inte eller kan kräva 'docker login'"

Dessa fel kan uppstå om du blandar direkt `helm install` `helm upgrade`Helm-kommandon (till exempel , eller `helm delete`) med dev spaces-kommandon (till exempel `azds up` och `azds down`) inuti samma utvecklingsutrymme. De uppstår eftersom Dev Spaces har sin egen Tiller-instans, som står i konflikt med din egen Tiller-instans som körs i samma dev-utrymme.

Det är bra att använda både Helm-kommandon och Dev Spaces-kommandon mot samma AKS-kluster, men varje Dev Spaces-aktiverad namnområde bör använda antingen det ena eller det andra.

Anta till exempel att du använder ett Helm-kommando för att köra hela programmet i ett överordnat utvecklingsutrymme. Du kan skapa underordnade dev-utrymmen från den överordnade, använda Dev Spaces för att köra enskilda tjänster i de underordnade utvecklingsutrymmena och testa tjänsterna tillsammans. När du är redo att checka in ändringarna använder du ett Helm-kommando för att distribuera den uppdaterade koden till det överordnade utvecklingsutrymmet. Använd `azds up` inte för att köra den uppdaterade tjänsten i det överordnade utvecklingsutrymmet, eftersom den kommer att stå i konflikt med tjänsten som ursprungligen kördes med Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Befintlig Dockerfile används inte för att skapa en behållare

Azure Dev Spaces kan konfigureras så att den pekar på en specifik _Dockerfile_ i projektet. Om det verkar som om Azure Dev Spaces inte använder den _Dockerfile_ som du förväntar dig att skapa dina behållare, kan du behöva uttryckligen tala om för Azure Dev Spaces vilka Dockerfile som ska användas. 

Lös problemet genom att öppna filen _azds.yaml_ som Azure Dev Spaces har genererat i projektet. *Uppdateringskonfigurationer: utveckla: skapa: dockerfile* för att peka på den Dockerfile som du vill använda. Ett exempel:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Fel "obehörig: autentisering krävs" när du försöker använda en Docker-avbildning från ett privat register

Du använder en Docker-avbildning från ett privat register som kräver autentisering.

Du kan åtgärda problemet genom att tillåta dev spaces att autentisera och hämta avbildningar från det här privata registret med [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Om du vill använda imagePullSecrets [skapar du en Kubernetes-hemlighet](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) i namnområdet där du använder avbildningen. Ange sedan hemligheten som en imagePullSecret i `azds.yaml`.

Nedan visas ett exempel på en angende `azds.yaml`imagePullSecrets i .

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
> Om du ställer in `azds.yaml` imagePullSecrets i åsidosätts `values.yaml`avbildningenPullSecrets som anges i .

### <a name="error-service-cannot-be-started"></a>Fel "Tjänsten kan inte startas".

Det här felet kan visas när servicekoden inte startar. Orsaken finns ofta i användarkoden. Om du vill ha mer diagnostikinformation aktiverar du mer detaljerad loggning när du startar tjänsten.

På kommandoraden `--verbose` använder du för att aktivera mer detaljerad loggning. Du kan också ange `--output`ett utdataformat med . Ett exempel:

```cmd
azds up --verbose --output json
```

I Visual Studio:

1. Öppna **verktyg > alternativ** och under Projekt och **lösningar**väljer du Bygg **och kör**.
2. Ändra inställningarna för **MSBuild-projektbyggeringsverbositet** till **Detaljerad** eller **Diagnostisk**.

    ![Skärmbild av dialogrutan Verktygsalternativ](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Köra om en tjänst efter att styrenheten återskapats

En *tjänst kan inte startas* när du försöker köra en tjänst igen när du har tagit bort den och återskapade sedan Azure Dev Spaces-styrenheten som är associerad med det här klustret. I det här fallet innehåller den utförliga utdata följande text:

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Det här felet beror på att det inte tar bort tjänster som tidigare installerats av styrenheten om du tar bort styrenheten. Det går inte att återskapa styrenheten och sedan försöka köra tjänsterna med den nya styrenheten eftersom de gamla tjänsterna fortfarande är på plats.

Om du vill lösa `kubectl delete` problemet använder du kommandot för att manuellt ta bort de gamla tjänsterna från klustret och kör sedan Dev Spaces igen för att installera de nya tjänsterna.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Fel "Tjänsten kan inte startas". när du använder dockerfiler i flera steg

En *tjänst kan inte startas* när du använder en Dockerfile i flera steg. I det här fallet innehåller den utförliga utdata följande text:

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

Det här felet uppstår eftersom Azure Dev Spaces för närvarande inte stöder flerstegsversioner. Om du vill undvika flerstegsversioner skriver du om Dockerfile.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>Nätverkstrafik vidarebefordras inte till AKS-klustret när du ansluter utvecklingsdatorn

När du använder [Azure Dev Spaces för att ansluta AKS-klustret till utvecklingsdatorn](how-to/connect.md)kan du stöta på ett problem där nätverkstrafik inte vidarebefordras mellan utvecklingsdatorn och AKS-klustret.

När du ansluter utvecklingsmaskinen till AKS-klustret vidarebefordrar Azure Dev Spaces nätverkstrafik mellan AKS-klustret och utvecklingsmaskinen genom att ändra utvecklingsmaskinens `hosts` fil. Azure Dev Spaces skapar en `hosts` post i med adressen till kubernetes-tjänsten som du ersätter som värdnamn. Den här posten används med portbefordring för direkt nätverkstrafik mellan utvecklingsmaskinen och AKS-klustret. Om en tjänst på utvecklingsdatorn står i konflikt med porten för den Kubernetes-tjänst som du ersätter, kan Azure Dev Spaces inte vidarebefordra nätverkstrafik för Kubernetes-tjänsten. *Windows BranchCache-tjänsten* är till exempel vanligtvis bunden till *0.0.0.0:80*, vilket konflikter orsakar en konflikt för port 80 på alla lokala IPs.

För att åtgärda problemet måste du stoppa alla tjänster eller processer som står i konflikt med porten för Kubernetes-tjänsten som du försöker ersätta. Du kan använda verktyg, till exempel *netstat,* för att granska vilka tjänster eller processer på utvecklingsdatorn som är i konflikt.

Så här stoppar och inaktiverar du *Windows BranchCache-tjänsten:*
* Kör `services.msc` från kommandotolken.
* Högerklicka på *BranchCache* och välj *Egenskaper*.
* Klicka på *Stoppa*.
* Du kan också inaktivera den genom att ange *starttyp* till *Inaktiverad*.
* Klicka på *OK*.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Vanliga problem med Att använda Visual Studio- och Visual Studio-kod med Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Fel "Nödvändiga verktyg och konfigurationer saknas"

Det här felet kan uppstå när vs-kod startas: "[Azure Dev Spaces] Nödvändiga verktyg och konfigurationer för att skapa och felsöka [projektnamn]' saknas."
Felet innebär att azds.exe inte finns i miljövariabeln PATH, vilket visas i VS-kod.

Prova att starta VS-kod från en kommandotolk där PATH-miljövariabeln är korrekt inställd.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Fel "Obligatoriska verktyg för att skapa och felsöka "projektnamn" är inaktuella."

Det här felet visas i Visual Studio-kod om du har en nyare version av VS-kodtillägget för Azure Dev Spaces, men en äldre version av Azure Dev Spaces CLI.

Prova att hämta och installera den senaste versionen av Azure Dev Spaces CLI:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Fel: "Det gick inte att hitta felsökningstillägget för typ:coreclr"

Det här felet kan visas när du kör felsökningen av Visual Studio-kod. Du kanske inte har VS-kodtillägget för C# installerat på utvecklingsmaskinen. C#-tillägget innehåller felsökningsstöd för .NET Core (CoreCLR).

Lös problemet genom att installera [VS-kodtillägget för C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Fel "Konfigurerad felsökningstyp 'coreclr' stöds inte"

Det här felet kan visas när du kör felsökningen av Visual Studio-kod. Du kanske inte har VS-kodtillägget för Azure Dev Spaces installerat på din utvecklingsdator.

Lös problemet genom att installera [VS-kodtillägget för Azure Dev Spaces](get-started-netcore.md).

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Fel "Ogiltigt cwd-värde" '/src'. Det går inte att hitta den angivna filen." eller "starta: programmet '/src/[path to project binary]' finns inte"

Det här felet kan visas när du kör felsökningen av Visual Studio-kod. Som standard används `src` vs-kodtillägget som arbetskatalog för projektet i behållaren. Om du har `Dockerfile` uppdaterat din för att ange en annan arbetskatalog kan det här felet visas.

Lös problemet genom att `launch.json` uppdatera `.vscode` filen under underkatalogen för projektmappen. Ändra `configurations->cwd` direktivet så att det pekar `WORKDIR` på samma katalog `Dockerfile`som den som definieras i projektets . Du kan också behöva `configurations->program` uppdatera direktivet också.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Fel "Pipe-programmet "azds" avslutades oväntat med kod 126."

Det här felet kan visas när du kör felsökningen av Visual Studio-kod.

Lös problemet genom att stänga och öppna Visual Studio Code igen. Starta om felsökningen.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Fel "Intern bevakning misslyckades: titta på ENOSPC" när du bifogar felsökning till ett Node.js-program

Det här felet uppstår när noden som kör podden med nod.js-programmet som du försöker koppla till med en felsökare har överskridit värdet *fs.inotify.max_user_watches.* I vissa fall [kan standardvärdet för *fs.inotify.max_user_watches* vara för litet för att hantera att bifoga en felsökare direkt till en pod](https://github.com/Azure/AKS/issues/772).

En tillfällig lösning för det här problemet är att öka värdet för *fs.inotify.max_user_watches* på varje nod i klustret och starta om noden för att ändringarna ska börja gälla.

## <a name="other-common-issues"></a>Andra vanliga frågor

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Fel "azds" känns inte igen som ett internt eller externt kommando, funktionsdugligt program eller kommandofil

Det här felet `azds.exe` kan inträffa om det inte är installerat eller konfigurerat korrekt.

Så här åtgärdar du problemet:

1. Kontrollera platsen %ProgramFiles%/Microsoft SDK\Azure\Azure Dev `azds.exe`Spaces CLI för . Om den finns där lägger du till den platsen i miljövariabeln PATH.
2. Om `azds.exe` det inte är installerat kör du följande kommando:

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Auktoriseringsfel "Microsoft.DevSpaces/register/åtgärd"

Du behöver åtkomst till *ägare* eller *deltagare* i din Azure-prenumeration för att hantera Azure Dev Spaces. Om du försöker hantera Dev Spaces och du inte har åtkomst till *ägare* eller *deltagare* till den associerade Azure-prenumerationen kan du se ett auktoriseringsfel. Ett exempel:

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Om du vill åtgärda problemet genom att använda ett konto med `Microsoft.DevSpaces` *ägares-* eller *deltagaråtkomst* till Azure-prenumerationen registrerar du namnområdet manuellt:

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Nya poddar startar inte

Kubernetes initializer kan inte tillämpa PodSpec för nya poddar på grund av RBAC-behörighetsändringar i *klusteradministratörsrollen* i klustret. Den nya podden kan också ha en ogiltig PodSpec, till exempel att tjänstkontot som är associerat med podden inte längre finns. Om du vill se poddar som är i *väntande* tillstånd `kubectl get pods` på grund av initializer-problemet använder du kommandot:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Det här problemet kan påverka poddar i alla namnområden i *klustret,* inklusive namnområden där Azure Dev Spaces inte är aktiverat.

Lös problemet [genom att uppdatera DEV Spaces CLI till den senaste versionen](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) och sedan ta bort *azds InitializerConfiguration* från Azure Dev Spaces-styrenheten:

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

När du har tagit bort *azds InitializerConfiguration* från `kubectl delete` Azure Dev Spaces-styrenheten använder du för att ta bort alla poddar i *väntande* tillstånd. När alla väntande poddar har tagits bort, distribuera om dina poddar.

Om nya poddar fortfarande har fastnat i *väntande* tillstånd efter `kubectl delete` en omdistribution använder du för att ta bort alla poddar i *väntande* tillstånd. När alla väntande poddar har tagits bort tar du bort styrenheten från klustret och installerar om den:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

När handkontrollen har installerats om distribuerar du om poddarna.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Felaktiga RBAC-behörigheter för att anropa Dev Spaces-styrenhet och API:er

Användaren som använder Azure Dev Spaces-styrenheten måste ha åtkomst för att läsa *administratörskubeconfig* i AKS-klustret. Den här behörigheten är till exempel tillgänglig i den [inbyggda Azure Kubernetes Service Cluster Admin Role](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). Användaren som använder Azure Dev Spaces-styrenheten måste också ha *rollen Contributor* eller *Owner* RBAC för styrenheten. Mer information om hur du uppdaterar en användares behörigheter för ett AKS-kluster finns [här](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Så här uppdaterar du användarens RBAC-roll för styrenheten:

1. Logga in på Azure Portal på https://portal.azure.com.
1. Navigera till resursgruppen som innehåller styrenheten, vilket vanligtvis är samma som AKS-klustret.
1. Aktivera kryssrutan *Visa dolda typer.*
1. Klicka på handkontrollen.
1. Öppna fönstret *IAM (Access Control).*
1. Klicka på fliken *Rolltilldelningar.*
1. Klicka på *Lägg till* och sedan Lägg *till rolltilldelning*.
    * För *Roll*väljer du *antingen Deltagare* eller *Ägare*.
    * För *Tilldela åtkomst till*väljer du Azure *AD-användare, grupp eller tjänsthuvudnamn*.
    * För *Välj*söker du efter den användare som du vill ge behörighet.
1. Klicka på *Spara*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>DNS-namnmatchning misslyckas för en offentlig URL som är associerad med en Dev Spaces-tjänst

Du kan konfigurera en offentlig URL-slutpunkt `--enable-ingress` för tjänsten `azds prep` genom att ange `Publicly Accessible` växeln till kommandot eller genom att markera kryssrutan i Visual Studio. Det offentliga DNS-namnet registreras automatiskt när du kör tjänsten i Dev Spaces. Om det här DNS-namnet inte är registrerat visas inte en *sida* eller *så kan inte webbplatsen nås* i webbläsaren när du ansluter till den offentliga webbadressen.

Så här åtgärdar du problemet:

* Kontrollera status för alla webbadresser som är associerade med dev spaces-tjänsterna:

  ```console
  azds list-uris
  ```

* Om en URL är i *väntande* tillstånd väntar Dev Spaces fortfarande på att DNS-registreringen ska slutföras. Ibland tar det några minuter innan registreringen är klar. Dev Spaces öppnar också en localhost-tunnel för varje tjänst, som du kan använda i väntan på DNS-registrering.
* Om en URL förblir i *väntande* tillstånd i mer än 5 minuter kan det tyda på ett problem med den externa DNS-podden som skapar den offentliga slutpunkten eller nginx-ingress-styrenhetsenheten som hämtar den offentliga slutpunkten. Använd följande kommandon för att ta bort dessa poddar och låta AKS automatiskt återskapa dem:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Fel "upstream connect error or disconnect/reset before headers"

Det här felet kan visas när du försöker komma åt din tjänst. Till exempel när du går till tjänstens WEBBADRESS i en webbläsare. Det här felet innebär att behållarporten inte är tillgänglig. Detta kan av följande skäl:

* Behållaren håller fortfarande på att byggas och distribueras. Det här problemet kan `azds up` uppstå om du kör eller startar felsökningsprogrammet och sedan försöker komma åt behållaren innan den har distribuerats.
* Portkonfigurationen är inte konsekvent i _Dockerfile,_ Helm-diagram och all serverkod som öppnar en port.

Så här åtgärdar du problemet:

1. Om behållaren håller på att byggas/distribueras kan du vänta 2-3 sekunder och försöka komma åt tjänsten igen. 
1. Kontrollera portkonfigurationen i följande tillgångar:
    * **[Helm-diagram](https://docs.helm.sh):** Angivet `deployment.containerPort` av `azds prep` `service.port` kommandot och i values.yaml.
    * Alla portar som öppnas i programkod, till exempel i Node.js:`var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Det gick inte att hitta namnet "MyLibrary"

Det går inte att hitta ett biblioteksprojekt som du använder. Med Dev Spaces är byggkontexten som standard på projekt-/servicenivå.  

Så här åtgärdar du problemet:

1. Ändra `azds.yaml` filen för att ställa in byggkontexten till lösningsnivån.
2. Ändra `Dockerfile` filerna `Dockerfile.develop` och filerna så att de `.csproj`refererar till projektfilerna, till exempel korrekt i förhållande till den nya build-kontexten.
3. Lägg `.dockerignore` till en i `.sln` samma katalog som filen.
4. Uppdatera `.dockerignore` med ytterligare poster efter behov.

Du hittar ett exempel [här](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Automatisk automatisk skalning av vågrät pod fungerar inte i ett utvecklingsutrymme

När du kör en tjänst i ett dev-utrymme injiceras den tjänstens pod [med ytterligare behållare för instrumentering](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster) och alla behållare i en pod måste ha resursgränser och begäranden inställda för Automatisk skalning av vågrät pod.

Lös problemet genom att använda en resursbegäran och begränsa den till de inskjutna Dev Spaces-behållarna. Resursbegäranden och -begränsningar kan användas för den injicerade behållaren `azds.io/proxy-resources` (devspaces-proxy) genom att lägga till anteckningen i pod-specifiken. Värdet ska anges till ett JSON-objekt som representerar resursavsnittet i behållarspecifikationen för proxyn.

Nedan visas ett exempel på en proxy-resurser-anteckning som ska tillämpas på pod-specifikationen.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Aktivera Azure Dev Spaces på ett befintligt namnområde med poddar som körs

Du kan ha ett befintligt AKS-kluster och namnområde med poddar som körs där du vill aktivera Azure Dev Spaces.

Om du vill aktivera Azure Dev Spaces på ett `use-dev-spaces` befintligt namnområde i ett AKS-kluster kör och använder du `kubectl` för att starta om alla poddar i namnområdet.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

När poddarna har startats om kan du börja använda ditt befintliga namnområde med Azure Dev Spaces.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Aktivera Azure Dev Spaces i AKS-kluster med begränsad utgående trafik för klusternoder

Om du vill aktivera Azure Dev Spaces i ett AKS-kluster för vilket utgående trafik från klusternoder är begränsad måste du tillåta följande FQDN:To enable Azure Dev Spaces on an AKS cluster for which the egress traffic from cluster nodes is restricted, youly have to allow following FQDNs:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Så här hämtar du linux alpine- och andra Azure Dev Spaces-avbildningar |
| gcr.io | HTTP:443 | Så här drar du helm/rorkultbilder|
| storage.googleapis.com | HTTP:443 | Så här drar du helm/rorkultbilder|
| azds-<guid>. <location>.azds.io | HTTPS:443 | Så här kommunicerar du med Azure Dev Spaces-backend-tjänster för din styrenhet. Den exakta FQDN finns i "dataplaneFqdn" i %USERPROFILE%\.azds\settings.json|

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Fel "Det gick \<inte\> att \<hitta\>klusterklustret i prenumerationsprenumerationenId"

Det här felet kan visas om kubeconfig-filen är inriktad på ett annat kluster eller en annan prenumeration än du försöker använda med Azure Dev Spaces-klientsidan. Azure Dev Spaces-klientens sidoverktyg replikerar *kubectls*beteende , som använder [en eller flera kubeconfig-filer](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) för att välja och kommunicera med klustret.

Så här åtgärdar du problemet:

* Används `az aks use-dev-spaces -g <resource group name> -n <cluster name>` för att uppdatera den aktuella kontexten. Det här kommandot aktiverar också Azure Dev Spaces i AKS-klustret om det inte redan är aktiverat. Du kan också `kubectl config use-context <cluster name>` använda för att uppdatera den aktuella kontexten.
* Används `az account show` för att visa den aktuella Azure-prenumerationen som du riktar in dig på och verifiera att detta är korrekt. Du kan ändra prenumerationen `az account set`som du riktar dig till med .

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Fel vid användning av Dev Spaces efter att AKS-certifikat har roterats

När [du har roterat certifikaten i AKS-klustret](../aks/certificate-rotation.md)misslyckas vissa åtgärder, till exempel `azds space list` och `azds up` misslyckas. Du måste också uppdatera certifikaten på azure dev spaces-styrenheten när du har roterat certifikaten i klustret.

Lös problemet genom att se till *att kubeconfig* har de uppdaterade certifikaten med och `az aks get-credentials` kör `azds controller refresh-credentials` kommandot. Ett exempel:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```

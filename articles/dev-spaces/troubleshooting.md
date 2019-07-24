---
title: Felsökning
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: conceptual
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s '
ms.openlocfilehash: 2434507ac89d631bb96ae9633403075801879a37
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277402"
---
# <a name="troubleshooting-guide"></a>Felsökningsguide

Den här guiden innehåller information om vanliga problem som du kan ha när du använder Azure Dev blanksteg.

Om du har problem med att använda Azure dev Spaces kan du skapa ett [problem i Azure dev Spaces GitHub](https://github.com/Azure/dev-spaces/issues)-lagringsplatsen.

## <a name="enabling-detailed-logging"></a>Aktivera detaljerad loggning

För att felsöka problem på ett effektivare sätt kan det hjälpa att skapa mer detaljerade loggar för granskning.

Visual Studio-tillägg, ange den `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` miljövariabeln till 1. Glöm inte att starta om Visual Studio för miljövariabeln ska börja gälla. När den är aktive rad skrivs detaljerade loggar `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` till din katalog.

I CLI, kan du mata ut mer information under Kommandokörning med hjälp av den `--verbose` växla. Du kan också gå mer detaljerade loggar i `%TEMP%\Azure Dev Spaces`. På en Mac till TEMP-katalogen kan hittas genom att köra `echo $TMPDIR` från ett terminalfönster. På en Linux-dator till TEMP-katalogen är vanligtvis `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Felsökning av tjänster med flera instanser

För närvarande fungerar Azure dev Spaces bäst vid fel sökning av en enskild instans eller pod. Filen azds. yaml innehåller en inställning, *replicaCount*, som anger antalet poddar som Kubernetes körs för din tjänst. Om du ändrar replicaCount för att konfigurera appen så att den kör flera poddar för en specifik tjänst, bifogas fel söknings programmet till de första pod, i alfabetisk ordning. Fel söknings programmet ansluts till en annan Pod när den ursprungliga Pod återanvänds, vilket kan resultera i ett oväntat beteende.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Felet ”Det gick inte att skapa Azure Dev blanksteg controller'

### <a name="reason"></a>Reason
Det här felet kan uppstå när något går fel med skapandet av kontrollanten. Om det är ett tillfälligt fel tar du bort och återskapar styrenheten för att åtgärda det.

### <a name="try"></a>Testa

Ta bort kontrollanten:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Du måste använda Azure dev Spaces CLI för att ta bort en kontrollant. Det går inte att ta bort en kontrollant från Visual Studio. Du kan inte heller installera Azure dev Spaces CLI i Azure Cloud Shell så att du inte kan ta bort en kontrollant från Azure Cloud Shell.

Om du inte har installerat Azure dev Spaces CLI kan du först installera det med hjälp av följande kommando och sedan ta bort styrenheten:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Återskapa kontrollanten kan du göra detta från CLI eller Visual Studio. Se [team utvecklingen](quickstart-team-development.md) eller [utveckla med .net Core](quickstart-netcore-visualstudio.md) -snabb starter för exempel.

## <a name="error-service-cannot-be-started"></a>Fel-tjänsten kan inte startas ”.

Det här felet kan uppstå när koden för tjänsten inte startar. Orsaken är ofta i användarkod. För att få diagnostisk information, göra följande ändringar i dina kommandon och inställningar:

### <a name="try"></a>Prova:

På kommandoraden:

När du använder _azds.exe_, använda--utförlig kommandoradsalternativet och använda kommandoradsalternativet--utdata att ange formatet för utdata.
 
```cmd
azds up --verbose --output json
```

I Visual Studio:

1. Öppna **Verktyg > alternativ** och under **produkter och lösningar**, Välj **skapa och köra**.
2. Ändra inställningarna för **MSBuild projekt build utdata utförlighet** till **detaljerat** eller **diagnostiska**.

    ![Skärmbild av alternativ för dialogrutan](media/common/VerbositySetting.PNG)
    
### <a name="multi-stage-dockerfiles"></a>Flera steg Dockerfiles:
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

### <a name="rerunning-a-service-after-controller-re-creation"></a>Köra en tjänst igen efter att styrenheten har skapats på nytt
Du får ett fel meddelande om att *tjänsten inte kan startas* när du försöker köra om en tjänst när du har tagit bort och återskapat den Azure dev Spaces-styrenhet som är associerad med det här klustret. I den här situationen innehåller utförliga utdata följande text:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Det här felet uppstår eftersom borttagning av dev Spaces-styrenheten inte tar bort tjänster som tidigare installerats av kontrollanten. Återskapande av kontrollanten och sedan försöka att köra tjänster som använder den nya domänkontrollanten misslyckas eftersom de gamla tjänsterna fortfarande är kvar.

Åtgärda problemet genom att använda `kubectl delete` kommandot för att manuellt ta bort de gamla tjänsterna från klustret, och kör sedan dev Spaces för att installera de nya tjänsterna.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>DNS-namnmatchningen misslyckas för en offentlig URL som är associerade med en tjänst för utveckling blanksteg

Du kan konfigurera en offentlig URL-slutpunkt för din tjänst genom att `--public` ange växeln `azds prep` till kommandot eller genom att markera `Publicly Accessible` kryss rutan i Visual Studio. Det offentliga DNS-namnet registreras automatiskt när du kör tjänsten i dev Spaces. Om det här DNS-namnet inte är registrerat visas en *sida som inte kan visas* eller så *går det inte att nå platsen* i webbläsaren vid anslutning till den offentliga URL: en.

### <a name="try"></a>Prova:

Du kan använda följande kommando för att lista ut alla URL: er som är associerade med dina Dev blanksteg tjänster:

```cmd
azds list-uris
```

Om en Webbadress finns i den *väntande* tillstånd, som innebär att Dev blanksteg väntar fortfarande för DNS-registrering att slutföra. Ibland kan tar det några minuter för registrering för att slutföra. Dev blanksteg öppnas även en localhost-tunnel för varje tjänst, som du kan använda under väntan på DNS-registrering.

Om en URL är i *vänte* läge i mer än 5 minuter kan det tyda på ett problem med den externa DNS-Pod som skapar den offentliga slut punkten eller nginx ingress-styrenhet Pod som hämtar den offentliga slut punkten. Du kan använda följande kommandon för att ta bort dessa poddar. AKS återskapar automatiskt de borttagna poddar.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>Fel krävs verktyg och konfigurationer som saknas

Det här felet kan uppstå när du startar VS Code ”: [Azure Dev blanksteg] nödvändiga verktyg och konfigurationer för att skapa och Felsök '[projekt namn]' saknas”.
Felet innebär att azds.exe inte är i miljövariabeln PATH som visas i VS Code.

### <a name="try"></a>Prova:

Starta VS Code från en kommandotolk där PATH-miljövariabeln är korrekt.

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Fel "verktyg som krävs för att bygga och felsöka ProjectName är inaktuella."

Du ser det här felet i Visual Studio Code om du har en nyare version av VS Code-tillägget för Azure dev Spaces, men en äldre version av Azure dev Spaces CLI.

### <a name="try"></a>Testa

Hämta och installera den senaste versionen av Azure dev Spaces CLI:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Fel 'azds' identifieras inte som ett internt eller externt kommando, ett körbart program eller en batchfil
 
Du kan se det här felet om azds.exe inte är installerat eller korrekt konfigurerat.

### <a name="try"></a>Prova:

1. Kontrol lera platsen% ProgramFiles%/Microsoft SDKs\Azure\Azure dev Spaces CLI för azds. exe. Om det är det lägger du till den platsen att miljövariabeln PATH.
2. Om azds.exe inte är installerad, kör du följande kommando:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Varning ”Dockerfile kunde inte genereras på grund av språket stöds inte.
Azure Dev blanksteg har inbyggt stöd för C# och Node.js. När du kör *azds prep* i en katalog som innehåller kod som skrivs i ett av dessa språk, Azure Dev blanksteg automatiskt skapar en lämplig Dockerfile åt dig.

Du kan fortfarande använda Azure dev Spaces med kod som skrivits på andra språk, men du måste skapa Dockerfile manuellt innan du kör *azds* för första gången.

### <a name="try"></a>Prova:
Om ditt program är skrivet på ett språk som inte stöds internt i Azure dev Spaces, måste du ange en lämplig Dockerfile för att skapa en behållar avbildning som kör din kod. Docker innehåller en [lista över metod tips för att skriva Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) och en [Dockerfile-referens](https://docs.docker.com/engine/reference/builder/) som kan hjälpa dig att skriva en Dockerfile som passar dina behov.

När du har en lämplig Dockerfile på plats kan du fortsätta med körs *azds upp* att köra ditt program i Azure Dev blanksteg.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Fel ”uppströms anslutningsfel eller koppla från/reset innan rubriker”
Du kan se det här felet när du försöker komma åt tjänsten. Till exempel när du går till tjänstens URL i en webbläsare. 

### <a name="reason"></a>Orsak 
Behållare-port är inte tillgängligt. Det här problemet kan inträffa därför att: 
* Behållaren håller fortfarande som skapat och distribuerat. Det här problemet kan uppstå om du kör `azds up` eller startar felsökningsprogrammet och försök sedan att få åtkomst till behållaren innan den har distribuerats.
* Portkonfiguration är inte konsekventa i din _Dockerfile_, Helm-diagrammet och eventuella serverkod som öppnar en port.

### <a name="try"></a>Prova:
1. Om behållaren håller på att bygger/distribueras, kan du vänta 2-3 sekunder och testa åtkomst till tjänsten igen. 
1. Kontrollera i portkonfigurationen. De angivna port numren måste vara **identiska** i samtliga följande till gångar:
    * **Dockerfile** Anges av `EXPOSE` instruktionen.
    * **[Helm-diagram](https://docs.helm.sh):** Anges av `externalPort` -och `internalPort` -värden för en tjänst (som ofta finns `values.yml` i en fil),
    * Alla portar som öppnas i programkoden, till exempel i Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Det gick inte att hitta konfigurationsfilen
Du kör `azds up` och följande felmeddelande: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Orsak
Du måste köra `azds up` från rotkatalogen för kod som du vill köra och måste du initiera mappen kod att köra Azure Dev blanksteg.

### <a name="try"></a>Prova:
1. Ändra katalogen till rotmappen som innehåller koden för tjänsten. 
1. Om du inte har en _azds.yaml_ filen i mappen kod kör `azds prep` att generera Docker, Kubernetes och Azure Dev blanksteg tillgångar.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Fel: Pipe-programmet azds avslutades utan förvarning med koden 126.
Starta VS Code-felsökare kan ibland resultera i det här felet.

### <a name="try"></a>Prova:
1. Stäng och öppna VS Code.
2. Tryck på F5 igen.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Felmeddelandet ”Det gick inte att hitta felsökare tillägg för typen: coreclr'-felsökning
Köra felsökaren för VS Code rapporterar felet: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Orsak
Du har inte det VS Code-tillägget för C#-installerat på utvecklingsdatorn. C# Tillägget innehåller stöd för fel sökning av .net Core (CoreCLR).

### <a name="try"></a>Prova:
Installera den [VS Code-tillägg för C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Felsöka fel 'konfigurerad debug typen 'coreclr' stöds inte ”
Köra felsökaren för VS Code rapporterar felet: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Orsak
Du har inte det VS Code-tillägget för Azure Dev blanksteg är installerat på utvecklingsdatorn.

### <a name="try"></a>Prova:
Installera den [VS Code-tillägg för Azure Dev blanksteg](get-started-netcore.md).

## <a name="debugging-error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Felsöka fel ”ogiltig cwd värdet ' / src'. Det går inte att hitta den angivna filen ”. eller ”starta: programmet '/ src / [sökväg till projektet binär]' finns inte”
Köra felsökaren för VS Code rapporterar felet `Invalid 'cwd' value '/src'. The system cannot find the file specified.` och/eller `launch: program '/src/[path to project executable]' does not exist`

### <a name="reason"></a>Orsak
Som standard använder VS Code-tillägg `src` som arbetskatalog för projektet för behållaren. Om du har uppdaterat din `Dockerfile` för att ange en annan arbetskatalog, kanske du ser det här felet.

### <a name="try"></a>Prova:
Uppdatera den `launch.json` filen under den `.vscode` underkatalog i projektmappen. Ändra den `configurations->cwd` direktiv så att den pekar till samma katalog som den `WORKDIR` definieras i projektets `Dockerfile`. Du kan också behöva uppdatera den `configurations->program` direktiv samt.

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Det gick inte att hitta typen eller namnrymd namnet ”MyLibrary”

### <a name="reason"></a>Reason 
Bygg kontexten är på projekt-/tjänst nivå som standard, vilket innebär att det inte går att hitta ett biblioteks projekt som du använder.

### <a name="try"></a>Prova:
Vad som behöver göras:
1. Ändra den _azds.yaml_ filen för att ange build-kontext till lösningen nivå.
2. Ändra den _Dockerfile_ och _Dockerfile.develop_ filer att referera till projektet ( _.csproj_) filer på rätt sätt, i förhållande till den nya skapa kontexten.
3. Plats en _.dockerignore_ filen bredvid SLN-filen och ändra vid behov.

Du kan se ett exempel på https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>'Microsoft.DevSpaces/register/action' auktoriseringsfel
Du behöver *ägar* -  eller deltagar åtkomst i din Azure-prenumeration för att kunna hantera Azure dev Spaces. Du kan se det här felet om du försöker hantera dev Spaces och du inte har *ägare* eller *deltagar* åtkomst till den associerade Azure-prenumerationen.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Reason
Den valda Azure-prenumerationen har inte registrerats i `Microsoft.DevSpaces` namnområde.

### <a name="try"></a>Prova:
Någon ägare eller deltagare åtkomst till Azure-prenumeration kan köra följande Azure CLI-kommando för att manuellt registrera den `Microsoft.DevSpaces` namnområde:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Tids gränsen nåddes för dev-utrymmen vid väntan på behållar *avbildnings avbildning...* steg med virtuella AKS-noder

### <a name="reason"></a>Reason
Denna tids gräns inträffar när du försöker använda dev Spaces för att köra en tjänst som är konfigurerad för att köras på en [virtuell AKS-nod](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Dev Spaces stöder för närvarande inte skapande eller fel sökning av tjänster på virtuella noder.

Om du kör `azds up` `--verbose` med växeln eller aktiverar utförlig loggning i Visual Studio visas ytterligare information:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Kommandot ovan visar att tjänstens Pod har tilldelats till *virtuell-Node-ACI-Linux*, som är en virtuell nod.

### <a name="try"></a>Prova:
Uppdatera Helm-diagrammet för tjänsten för att ta bort avsökeror *och/* eller *tolerera* värden som tillåter att tjänsten körs på en virtuell nod De här värdena definieras vanligt vis i diagrammets `values.yaml` fil.

Du kan fortfarande använda ett AKS-kluster som har funktionen virtuella noder aktiverade, om tjänsten som du vill bygga/felsöka via dev Spaces körs på en VM-nod. Detta är standard konfigurationen.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>”Fel: kunde inte hitta en klar tiller pod” när du startar Dev blanksteg

### <a name="reason"></a>Orsak
Det här felet uppstår om Helm-klienten kan inte längre kommunicera med din Tiller pod körs i klustret.

### <a name="try"></a>Prova:
Starta om agentnoder i klustret vanligtvis löser problemet.

## <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>"Fel: release azds-\<Identifier\>-\<spacename\>ServiceNamemisslyckades\> : Services 'ServiceName\<-\<\>finns redan eller så har åtkomst nekats för \<ServiceName\>, databasen finns inte eller så kan det krävas Docker inloggning "

### <a name="reason"></a>Reason
Felen kan uppstå om du blandar kör direkta Helm-kommandon (till exempel `helm install`, `helm upgrade`eller `helm delete` `azds up` ) med dev Spaces-kommandon (till exempel `azds down`och) inuti samma dev-utrymme. De inträffar eftersom dev Spaces har sin egen till-instans, som står i konflikt med din egen till-instans som körs i samma dev-utrymme.

### <a name="try"></a>Prova:
Det är bra att använda både Helm-kommandon och dev Spaces-kommandon mot samma AKS-kluster, men varje dev Spaces-aktiverad namnrymd bör använda antingen ett eller ett annat.

Anta till exempel att du använder ett Helm-kommando för att köra hela programmet i ett överordnat dev-utrymme. Du kan skapa underordnade dev-utrymmen från överordnad, använda dev Spaces för att köra enskilda tjänster i de underordnade dev Spaces och testa tjänsterna tillsammans. När du är redo att kontrol lera ändringarna använder du ett Helm-kommando för att distribuera den uppdaterade koden till det överordnade dev-utrymmet. Använd `azds up` inte för att köra den uppdaterade tjänsten i det överordnade dev-utrymmet, eftersom den kommer att stå i konflikt med att tjänsten ursprungligen körs med Helm.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Azure Dev blanksteg proxy kan störa andra poddar som körs i en dev-utrymme

### <a name="reason"></a>Orsak
När du aktiverar Dev blanksteg för ett namnområde i AKS-kluster kan ytterligare en behållare kallas _mindaro proxy_ är installerat i var och en av poddarna som körs i det här namnområdet. Den här behållaren fångar upp anrop till tjänsterna i pod, som är integrerad med utvecklings utrymmen ' team utvecklings funktioner; Det kan dock störa vissa tjänster som körs i dessa poddar. Det är känt att störa poddar som kör Azure cache för Redis, vilket orsakar anslutnings fel och fel i primär/sekundär kommunikation.

### <a name="try"></a>Prova:
Du kan flytta den berörda poddar till ett namn område i klustret som _inte_ har några aktiverade dev-utrymmen. Resten av programmet kan fortsätta att köras inuti ett namn område med dev-aktiverat. Dev blanksteg kan inte installeras på _mindaro proxy_ behållare i Dev blanksteg aktiverade namnområden.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev blanksteg verkar inte använda min befintliga Dockerfile för att skapa en behållare

### <a name="reason"></a>Orsak
Azure Dev blanksteg kan konfigureras för att peka på en specifik _Dockerfile_ i projektet. Om det verkar som om Azure dev Spaces inte använder den _Dockerfile_ som du förväntar dig att bygga dina behållare, kan du uttryckligen behöva berätta om Azure dev Spaces som Dockerfile ska använda. 

### <a name="try"></a>Prova:
Öppna filen _azds. yaml_ som Azure dev-Spaces skapade i projektet. Använd de *konfigurationer-> utveckla-> build-> Dockerfile-* direktivet för att peka på den Dockerfile som du vill använda:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

## <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Felet "intern Watch misslyckades: se ENOSPC" vid koppling av fel sökning till ett Node. js-program

### <a name="reason"></a>Reason

Noden som kör Pod med Node. js-programmet som du försöker ansluta till med en fel sökare har överskridit värdet *FS. inotify. Max _user_watches* . I vissa fall [kan standardvärdet *FS. inotify. Max _user_watches* vara för litet för att kunna hantera ett fel söknings program direkt till en POD](https://github.com/Azure/AKS/issues/772).

### <a name="try"></a>Testa
En tillfällig lösning på det här problemet är att öka värdet för *FS. inotify. Max _user_watches* på varje nod i klustret och starta om noden för att ändringarna ska börja gälla.

## <a name="new-pods-are-not-starting"></a>Nya poddar startar inte

### <a name="reason"></a>Reason

Kubernetes-initieraren kan inte använda PodSpec för nya poddar på grund av RBAC-behörighet ändringar i rollen *kluster-admin* i klustret. Den nya Pod kan också ha en ogiltig PodSpec, till exempel det tjänst konto som är kopplat till Pod inte längre finns. Om du vill se poddar som är i  ett väntande tillstånd på grund av ett problem med initieraren använder du `kubectl get pods` kommandot:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Det här problemet kan påverka poddar i *alla namn områden* i klustret, inklusive namn rymder där Azure dev Spaces inte är aktiverat.

### <a name="try"></a>Testa

[Uppdatera dev Spaces CLI till den senaste versionen](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) och ta sedan bort *azds-InitializerConfiguration* från Azure dev Spaces-styrenheten:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

När du har tagit bort *azds-InitializerConfiguration* från Azure dev Spaces-kontrollanten använder `kubectl delete` du för att ta bort eventuella poddar i ett *väntande* tillstånd. När alla väntande poddar har tagits bort distribuerar du om din poddar.

Om nya poddar fortfarande fastnar i ett *väntande* tillstånd efter en distribution, använder `kubectl delete` du för att ta bort eventuella poddar i ett *väntande* tillstånd. När alla väntande poddar har tagits bort tar du bort kontrollanten från klustret och installerar om den:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

När din styrenhet har installerats om distribuerar du din poddar.

## <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Felaktiga RBAC-behörigheter för att anropa dev Spaces-styrenhet och API: er

### <a name="reason"></a>Reason
Användaren som använder Azure dev Spaces-styrenheten måste ha åtkomst för att kunna läsa administratörs *kubeconfig* i AKS-klustret. Den här behörigheten är till exempel tillgänglig i den [inbyggda administratörs rollen för Azure Kubernetes service-klustret](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). Användaren som ansluter till Azure dev Spaces-kontrollanten måste också ha rollen *deltagare* eller *ägare* RBAC för kontrollanten.

### <a name="try"></a>Testa
Mer information om hur du uppdaterar en användares behörigheter för ett AKS-kluster finns [här](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Så här uppdaterar du användarens RBAC-roll för kontrollanten:

1. Logga in på Azure Portal på https://portal.azure.com.
1. Navigera till resurs gruppen som innehåller kontrollanten, som vanligt vis är samma som ditt AKS-kluster.
1. Aktivera kryss rutan *Visa dolda typer* .
1. Klicka på kontroll enheten.
1. Öppna fönstret *Access Control (IAM)* .
1. Klicka på fliken *roll tilldelningar* .
1. Klicka på *Lägg till* och *Lägg sedan till roll tilldelning*.
    * För *roll* väljer du antingen *deltagare* eller *ägare*.
    * För *tilldela åtkomst till* Välj *Azure AD-användare,-grupp eller-tjänstens huvud namn*.
    * För *Välj* Sök efter den användare som du vill ge behörighet.
1. Klicka på *Spara*.

## <a name="controller-create-failing-due-to-controller-name-length"></a>Styrenheten kunde inte skapas på grund av kontrollantens namn längd

### <a name="reason"></a>Reason
Ett namn på en Azure dev Spaces-enhet får inte vara längre än 31 tecken. Om kontroll enhetens namn överskrider 31 tecken när du aktiverar dev Spaces i ett AKS-kluster eller skapar en kontrollant, får du ett fel meddelande som:

*Det gick inte att skapa en dev Spaces-kontrollant för klustrets "a-Controller-Name-on-Way-AKS-öst-US": Azure dev Spaces-kontrollantens namn "a-Controller-Name-on-Way-AKS-öst-US" är ogiltigt. Begränsning (er) har överskridits: Namn på Azure dev Spaces-kontrollanter får innehålla högst 31 tecken*

### <a name="try"></a>Testa

Skapa en kontrollant med ett alternativt namn:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

## <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Det går inte att aktivera dev-utrymmen när Windows-nodkonfigurationer läggs till i ett AKS-kluster

### <a name="reason"></a>Reason
För närvarande är Azure dev Spaces endast avsett att köras på Linux-poddar och noder. När du har ett AKS-kluster med en Windows-adresspool måste du se till att Azure dev Spaces-poddar bara är schemalagda på Linux-noder. Om en Azure dev Spaces-pod är schemalagd att köras på en Windows-nod, kommer Pod inte att starta och inte aktivera dev-utrymmen.

### <a name="try"></a>Testa
[Lägg till en smak](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) till ditt AKS-kluster för att säkerställa att Linux-poddar inte är schemalagda att köras på en Windows-nod.

## <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Fel "hittade inga icke-förorenade Linux-noder i klart läge i klustret. Det måste finnas minst en azds Linux-nod i klar läge för att distribuera poddar i namn området.

### <a name="reason"></a>Reason

Det gick inte att skapa en kontrollant i AKS-klustret i Azure dev Spaces eftersom det inte gick att hitta en icke-förorenad nod i ett *klart* tillstånd för att schemalägga poddar på. Azure dev Spaces kräver minst en Linux-nod i ett *klart* tillstånd som gör det möjligt att schemalägga poddar utan att ange tolerera.

### <a name="try"></a>Testa
[Uppdatera din smak konfiguration](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) på ditt AKS-kluster för att se till att minst en Linux-nod tillåter schemaläggning av poddar utan att ange tolererande. Se också till att minst en Linux-nod som tillåter schemaläggning av poddar utan att ange tolererar är *klar* . Om noden tar lång tid att komma *igång* kan du prova att starta om noden.

## <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Fel "Azure dev Spaces-CLI har inte installerats korrekt" vid körning`az aks use-dev-spaces`

### <a name="reason"></a>Reason
En uppdatering av Azure dev Spaces CLI ändrade installations Sök vägen. Om du använder en tidigare version av Azure CLI än 2.0.63 kan du se det här felet. Använd `az --version`om du vill visa din version av Azure CLI.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

Trots att fel meddelandet körs `az aks use-dev-spaces` med en version av Azure CLI före 2.0.63, lyckas installationen. Du kan fortsätta att använda `azds` utan problem.

### <a name="try"></a>Testa
Uppdatera installationen av [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) till 2.0.63 eller senare. Detta löser det fel meddelande som visas när du kör `az aks use-dev-spaces`. Alternativt kan du fortsätta att använda din aktuella version av Azure CLI och Azure dev Spaces CLI.


## <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Horisontell Pod-skalning fungerar inte i ett dev-utrymme

### <a name="reason"></a>Reason

När du kör en tjänst i ett dev-utrymme injiceras tjänstens Pod [med ytterligare behållare för instrumentering](how-dev-spaces-works.md#prepare-your-aks-cluster). Dessa behållare har inte några resurs begär Anden eller gränser, vilket gör att den vågräta Pod-autoskalning är inaktive rad för pod.

### <a name="try"></a>Testa
Kör autoskalning för horisontell Pod i ett namn område där inga dev-rymder har Aktiver ATS.

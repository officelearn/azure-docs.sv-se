---
title: Felsökning | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: article
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: d3fbc8e5b6595b52fe5ab9e766a108d271f2f448
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104602"
---
# <a name="troubleshooting-guide"></a>Felsökningsguide

Den här guiden innehåller information om vanliga problem som du kan ha när du använder Azure Dev blanksteg.

## <a name="enabling-detailed-logging"></a>Aktivera detaljerad loggning

Det kan hjälpa för att skapa mer detaljerade loggar för granskning för att felsöka problem med mer effektivt.

Visual Studio-tillägg, ange den `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` miljövariabeln till 1. Glöm inte att starta om Visual Studio för miljövariabeln ska börja gälla. När du har aktiverat, detaljerade loggar skrivs till din `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` directory.

I CLI, kan du mata ut mer information under Kommandokörning med hjälp av den `--verbose` växla. Du kan också gå mer detaljerade loggar i `%TEMP%\Azure Dev Spaces`. På en Mac till TEMP-katalogen kan hittas genom att köra `echo $TMPDIR` från ett terminalfönster. På en Linux-dator till TEMP-katalogen är vanligtvis `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Felsökning av tjänster med flera instanser

Just nu fungerar Azure Dev blanksteg bäst när du felsöker en enda instans (pod). Azds.yaml-filen innehåller en inställning, replicaCount, som anger antalet poddar som ska köras för din tjänst. Om du ändrar replicaCount för att konfigurera din app för att köra flera poddar för en viss tjänst, ska felsökningsprogrammet kopplas till den första pod (när det visas i alfabetisk ordning). Om den pod återvinns av någon anledning, till oväntade resultat eftersom felsökningsprogrammet ska kopplas till en annan pod.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Felet ”Det gick inte att skapa Azure Dev blanksteg controller'

Det här felet kan uppstå när något går fel med skapandet av kontrollanten. Om det är ett tillfälligt fel, korrigerar ta bort och återskapa kontrollanten den.

### <a name="try"></a>Prova:

Använda Azure Dev blanksteg CLI för att ta bort kontrollanten. Det går inte att göra det i Visual Studio eller Cloud Shell. Om du vill installera CLI AZDS först installera Azure CLI och kör sedan det här kommandot:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Och kör sedan detta kommando för att ta bort kontrollanten:

```cmd
azds remove -g <resource group name> -n <cluster name>
```

Återskapa kontrollanten kan du göra detta från CLI eller Visual Studio. Följ instruktionerna i självstudier som om startar för första gången.


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
Du kan se det här felet när du försöker använda en flerstegs Dockerfile. Utdata ser ut så här:

```cmd
$ azds up
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

Det beror på att skapar AKS-noder som kör en äldre version av Docker som inte stöder flera steg. Du behöver skriva om din Dockerfile för att undvika flera steg versioner.

### <a name="re-running-a-service-after-controller-re-creation"></a>Köra en tjänst efter skapandet av domänkontrollanten igen
Du kan se det här felet när du försöker köra en tjänst när du har tagit bort och återskapas kontrollanten Azure Dev blanksteg som är associerade med det här klustret. Utdata ser ut så här:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Det beror på att ta bort kontrollanten Dev blanksteg tas inte bort tidigare installerade av den controller. Återskapande av kontrollanten och sedan försöka att köra tjänster som använder den nya domänkontrollanten misslyckas eftersom de gamla tjänsterna fortfarande är kvar.

För att lösa det, Använd den `kubectl delete` kommando för att manuellt ta bort de gamla tjänsterna från ditt kluster och kör Dev blanksteg för att installera de nya tjänsterna.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>DNS-namnmatchningen misslyckas för en offentlig URL som är associerade med en tjänst för utveckling blanksteg

När DNS-namnmatchningen misslyckas visas ett ”sidan kan inte visas” eller ”den här platsen kan inte nås” fel i webbläsaren när du försöker ansluta till en offentlig URL som är associerade med en tjänst för utveckling blanksteg.

### <a name="try"></a>Prova:

Du kan använda följande kommando för att lista ut alla URL: er som är associerade med dina Dev blanksteg tjänster:

```cmd
azds list-uris
```

Om en Webbadress finns i den *väntande* tillstånd, som innebär att Dev blanksteg väntar fortfarande för DNS-registrering att slutföra. Ibland kan tar det några minuter för registrering för att slutföra. Dev blanksteg öppnas även en localhost-tunnel för varje tjänst, som du kan använda under väntan på DNS-registrering.

Om en URL som finns kvar i den *väntande* tillstånd under mer än 5 minuter, det kan bero på problem med den externa DNS-pod som skapar den offentliga slutpunkten och/eller nginx ingående controller pod som hämtar den offentliga slutpunkten. Du kan använda följande kommandon för att ta bort dessa poddar. De kommer att återskapas automatiskt.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>Fel krävs verktyg och konfigurationer som saknas

Det här felet kan uppstå när du startar VS Code ”: [Azure Dev blanksteg] nödvändiga verktyg och konfigurationer för att skapa och Felsök '[projekt namn]' saknas”.
Felet innebär att azds.exe inte är i miljövariabeln PATH som visas i VS Code.

### <a name="try"></a>Prova:

Starta VS Code från en kommandotolk där PATH-miljövariabeln är korrekt.

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Fel 'azds' identifieras inte som ett internt eller externt kommando, ett körbart program eller en batchfil
 
Du kan se det här felet om azds.exe inte är installerat eller korrekt konfigurerat.

### <a name="try"></a>Prova:

1. Kontrollera plats-%ProgramFiles%/Microsoft SDKs\Azure\Azure Dev blanksteg CLI (förhandsversion) för azds.exe. Om det är det lägger du till den platsen att miljövariabeln PATH.
2. Om azds.exe inte är installerad, kör du följande kommando:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Varning ”Dockerfile kunde inte genereras på grund av språket stöds inte.
Azure Dev blanksteg har inbyggt stöd för C# och Node.js. När du kör *azds prep* i en katalog som innehåller kod som skrivs i ett av dessa språk, Azure Dev blanksteg automatiskt skapar en lämplig Dockerfile åt dig.

Du kan fortfarande använda Azure Dev blanksteg med kod som skrivits på andra språk, men du kommer att behöva skapa Dockerfile själv innan du kör *azds upp* för första gången.

### <a name="try"></a>Prova:
Om ditt program är skrivet på ett språk att Azure Dev blanksteg inte har inbyggt stöd, måste du ange en lämplig Dockerfile för att skapa en behållaravbildning som kör koden. Docker innehåller en [lista över bästa praxis för att skriva Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) samt en [Dockerfile-referensen](https://docs.docker.com/engine/reference/builder/) som hjälper dig att skriva en Dockerfile som passar dina behov.

När du har en lämplig Dockerfile på plats kan du fortsätta med körs *azds upp* att köra ditt program i Azure Dev blanksteg.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Fel ”uppströms anslutningsfel eller koppla från/reset innan rubriker”
Du kan se det här felet när du försöker komma åt tjänsten. Till exempel när du går till tjänstens URL i en webbläsare. 

### <a name="reason"></a>Orsak 
Behållare-port är inte tillgängligt. Det här problemet kan inträffa därför att: 
* Behållaren håller fortfarande som skapat och distribuerat. Det här problemet kan uppstå om du kör `azds up` eller startar felsökningsprogrammet och försök sedan att få åtkomst till behållaren innan den har distribuerats.
* Portkonfiguration är inte konsekventa i din _Dockerfile_, Helm-diagrammet och eventuella serverkod som öppnar en port.

### <a name="try"></a>Prova:
1. Om behållaren håller på att bygger/distribueras, kan du vänta 2-3 sekunder och testa åtkomst till tjänsten igen. 
1. Kontrollera i portkonfigurationen. De angivna portnummer måste vara **identiska** i alla tillgångar som nedan:
    * **Dockerfile:** anges av den `EXPOSE` instruktion.
    * **[Helm-diagrammet](https://docs.helm.sh):** anges av den `externalPort` och `internalPort` värden för en tjänst (ofta finns i en `values.yml` fil),
    * Alla portar som öppnas i programkoden, till exempel i Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Det gick inte att hitta konfigurationsfilen
Du kör `azds up` och följande felmeddelande: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Orsak
Du måste köra `azds up` från rotkatalogen för kod som du vill köra och måste du initiera mappen kod att köra Azure Dev blanksteg.

### <a name="try"></a>Prova:
1. Ändra katalogen till rotmappen som innehåller koden för tjänsten. 
1. Om du inte har en _azds.yaml_ filen i mappen kod kör `azds prep` att generera Docker, Kubernetes och Azure Dev blanksteg tillgångar.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Fel: ”det pipe programmet 'azds' avslutades oväntat med koden 126”.
Starta VS Code-felsökare kan ibland resultera i det här felet.

### <a name="try"></a>Prova:
1. Stäng och öppna VS Code.
2. Tryck på F5 igen.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Felmeddelandet ”Det gick inte att hitta felsökare tillägg för typen: coreclr'-felsökning
Köra felsökaren för VS Code rapporterar felet: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Orsak
Du har inte det VS Code-tillägget för C#-installerat på utvecklingsdatorn. C#-tillägget innehåller felsökning för .net Core (CoreCLR).

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

### <a name="reason"></a>Orsak 
Build-kontexten är på projekt/tjänstnivå som standard, därför hittas ett biblioteksprojekt som du använder inte.

### <a name="try"></a>Prova:
Vad som behöver göras:
1. Ändra den _azds.yaml_ filen för att ange build-kontext till lösningen nivå.
2. Ändra den _Dockerfile_ och _Dockerfile.develop_ filer att referera till projektet (_.csproj_) filer på rätt sätt, i förhållande till den nya skapa kontexten.
3. Plats en _.dockerignore_ filen bredvid SLN-filen och ändra vid behov.

Du kan se ett exempel på https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>'Microsoft.DevSpaces/register/action' auktoriseringsfel
Följande fel kan uppstå när du hanterar ett adressutrymme för utveckling av Azure och du arbetar i en Azure-prenumeration som du inte har ägare eller deltagare som har åtkomst till.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Orsak
Den valda Azure-prenumerationen har inte registrerats i `Microsoft.DevSpaces` namnområde.

### <a name="try"></a>Prova:
Någon ägare eller deltagare åtkomst till Azure-prenumeration kan köra följande Azure CLI-kommando för att manuellt registrera den `Microsoft.DevSpaces` namnområde:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>”Fel: kunde inte hitta en klar tiller pod” när du startar Dev blanksteg

### <a name="reason"></a>Orsak
Det här felet uppstår om Helm-klienten kan inte längre kommunicera med din Tiller pod körs i klustret.

### <a name="try"></a>Prova:
Starta om agentnoder i klustret vanligtvis löser problemet.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Azure Dev blanksteg proxy kan störa andra poddar som körs i en dev-utrymme

### <a name="reason"></a>Orsak
När du aktiverar Dev blanksteg för ett namnområde i AKS-kluster kan ytterligare en behållare kallas _mindaro proxy_ är installerat i var och en av poddarna som körs i det här namnområdet. Den här behållaren spärras anrop till tjänsterna i pod, som är väsentlig Dev blanksteg team utvecklingsmöjligheter.

Tyvärr kan det påverka vissa tjänster som körs i de poddarna. Mer specifikt kan störa det poddar som kör Azure Cache för Redis, orsaka anslutningsfel och misslyckade begäranden i master/slave kommunikation.

### <a name="try"></a>Prova:
Du kan flytta den berörda pod(s) till ett namnområde i det kluster som har _inte_ har Dev blanksteg aktiverat när du fortsätter att köra resten av ditt program i ett namnområde för Dev blanksteg-aktiverade. Dev blanksteg kan inte installeras på _mindaro proxy_ behållare i Dev blanksteg aktiverade namnområden.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev blanksteg verkar inte använda min befintliga Dockerfile för att skapa en behållare 

### <a name="reason"></a>Orsak
Azure Dev blanksteg kan konfigureras för att peka på en specifik _Dockerfile_ i projektet. Om den visas Azure Dev blanksteg inte använder den _Dockerfile_ du förväntar dig att bygga dina behållare kan du behöva meddelar Azure Dev blanksteg där det är uttryckligen. 

### <a name="try"></a>Prova:
Öppna den _azds.yaml_ -fil som har genererats av Azure Dev blanksteg i projektet. Använd den `configurations->develop->build->dockerfile` direktiv så att den pekar till Dockerfile som du vill använda:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

---
title: Felsökning | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: b2ef450a429b26843cf770a6243c6f4de932de43
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247337"
---
# <a name="troubleshooting-guide"></a>Felsökningsguide

Den här guiden innehåller information om vanliga problem som du kan ha när du använder Azure Dev blanksteg.

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

1. Öppna **Verktyg > alternativ** och under **produkter och lösningar**, Välj och **skapa och köra**.
2. Ändra inställningarna för **MSBuild projekt build utdata utförlighet** till **detaljerat** eller **diagnostiska**.

    ![Skärmbild av alternativ för dialogrutan](media/common/VerbositySetting.PNG)

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
Starta VS Code-felsökare kan ibland resultera i det här felet. Det här är ett känt problem.

### <a name="try"></a>Prova:
1. Stäng och öppna VS Code.
2. Tryck på F5 igen.


## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Felsöka fel 'konfigurerad debug typen 'coreclr' stöds inte ”
Köra felsökaren för VS Code rapporterar felet: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Orsak
Du har inte det VS Code-tillägget för Azure Dev blanksteg är installerat på utvecklingsdatorn.

### <a name="try"></a>Prova:
Installera den [VS Code-tillägg för Azure Dev blanksteg](get-started-netcore.md).

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
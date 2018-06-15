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
description: Snabb utveckling av Kubernetes med behållare och mikrotjänster på Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare
manager: douge
ms.openlocfilehash: 371bb9195266f3511d115de2532e6b64f49ef26f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199304"
---
# <a name="troubleshooting-guide"></a>Felsökningsguide

Den här guiden innehåller information om vanliga problem du kan ha när du använder Azure Dev blanksteg.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Fel ”uppströms anslutningsfel eller koppla från/reset innan huvuden”
Du kan se det här felet när du försöker komma åt tjänsten. Till exempel när du går till tjänstens URL i en webbläsare. 

### <a name="reason"></a>Orsak 
Behållaren porten är inte tillgänglig. Det kan bero på: 
* Behållaren håller fortfarande på som skapats och distribuerats. Detta kan vara fallet om du kör `azds up` eller starta felsökningsprogrammet och försök sedan att få åtkomst till behållaren innan den har distribuerats.
* Portkonfiguration är inte konsekvent över din Dockerfile, Helm diagram och eventuella serverkod som öppnar en port.

### <a name="try"></a>Försök:
1. Om behållaren håller som byggts/distribueras du vänta 2-3 sekunder och försök nå tjänsten igen. 
1. Kontrollera portkonfigurationen av. Den angivna portnummer måste vara **identiska** i alla tillgångar nedan:
    * **Dockerfile:** anges av den `EXPOSE` instruktion.
    * **[Helm diagram](https://docs.helm.sh):** anges av den `externalPort` och `internalPort` värden för en tjänst (ofta finns i en `values.yml` fil),
    * Alla portar som öppnas i programkoden, till exempel i Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Det gick inte att hitta konfigurationsfilen
Du kör `azds up` och följande felmeddelande: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Orsak
Du måste köra `azds up` från rotkatalogen på kod som du vill köra och måste du initiera code-mappen att köra Azure Dev blanksteg.

### <a name="try"></a>Försök:
1. Ändra katalogen till rotmappen som innehåller koden för tjänsten. 
1. Om du inte har en azds.yaml-fil i mappen code kör `azds prep` att generera Docker, Kubernetes och Azure Dev blanksteg tillgångar.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Fel: 'pipe programmet avslutades oväntat med koden 126 ' azds'.'
Startar felsökningsprogrammet VS kod kan ibland resultera i det här felet. Detta är ett känt problem.

### <a name="try"></a>Försök:
1. Stäng och öppna VS-kod.
2. Tryck på F5 igen.


## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Felsökning fel 'konfigurerad debug 'coreclr' stöds inte:
Kör Felsökaren VS koden rapporterar felet: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Orsak
Du har inte filnamnstillägget VS-kod för Azure Dev blanksteg är installerat på utvecklingsdatorn.

### <a name="try"></a>Försök:
Installera den [VS kod tillägget för Azure Dev blanksteg](get-started-netcore.md).

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Det gick inte att hitta namnet typ eller namnområdet 'MyLibrary'

### <a name="reason"></a>Orsak 
Build-kontexten är på projekt/tjänstnivå som standard, därför hittas ett biblioteksprojekt som du använder inte.

### <a name="try"></a>Försök:
Vad som behöver göras:
1. Ändra filen azds.yaml om du vill ange build-kontext till nivån lösning.
2. Ändra Dockerfile och Dockerfile.develop filer att referera till csproj filer på rätt sätt, i förhållande till den nya build-kontexten.
3. Placera en fil .dockerignore bredvid SLN-filen och ändra vid behov.

Du hittar ett exempel på https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftconnectedenvironmentregisteraction-authorization-error"></a>'Microsoft.ConnectedEnvironment/register/action' Behörighetsfel
Följande fel kan uppstå när du hanterar ett utrymme för utveckling av Azure och du arbetar i en Azure-prenumeration som du inte har ägare eller deltagare som har åtkomst till.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.ConnectedEnvironment/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Orsak
Den valda Azure-prenumerationen har inte registrerats Microsoft.ConnectedEnvironment namnområde.

### <a name="try"></a>Försök:
Någon ägare eller deltagare åtkomst till Azure-prenumeration kan köra följande Azure CLI-kommando för att registrera manuellt Microsoft.ConnectedEnvironment namnområde:

```cmd
az provider register --namespace Microsoft.ConnectedEnvironment
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev blanksteg verkar använda min befintliga Dockerfile för att skapa en behållare 

### <a name="reason"></a>Orsak
Azure Dev blanksteg kan konfigureras för att peka på en specifik Dockerfile i projektet. Om det verkar Azure Dev blanksteg inte är med Dockerfile som du förväntar dig att skapa en behållare kan behöva du uttryckligen begära Azure Dev blanksteg där det är. 

### <a name="try"></a>Försök:
Öppna den `azds.yaml` -fil som har genererats av Azure Dev blanksteg i projektet. Använd den `configurations->develop->build->dockerfile` direktiv att peka mot Dockerfile som du vill använda:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```
---
title: Azure Service Fabric Docker Compose distribution förhandsversion
description: Azure Service Fabric accepterar Docker Compose-format för att göra det lättare att dirigera befintliga behållare med Service Fabric. Det här stödet förhandsvisas just nu.
services: service-fabric
documentationcenter: .net
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
origin.date: 02/23/2018
ms.date: 04/29/2019
ms.author: v-yeche
ms.openlocfilehash: da86ed9a3e6979bd1dc05aef6ef70c7b8533a8c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60948842"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Docker Compose-distributionsstöd i Azure Service Fabric (förhandsversion)

Docker används den [docker-compose.yml](https://docs.docker.com/compose) till att definiera program med flera behållare. Om du vill göra det enklare för kunder vana vid Docker att orkestrera befintliga behållarprogram i Azure Service Fabric, har vi inkluderat förhandsversion av stöd för Docker Compose-distributionen har inbyggt i plattformen. Service Fabric kan acceptera version 3 eller senare av `docker-compose.yml` filer. 

Eftersom det här stödet finns i förhandsversion, stöds endast en delmängd av Compose. Till exempel stöds programuppgraderingar inte. Du kan alltid ta bort och distribuera program i stället för att uppgradera dem.

För att använda den här förhandsversionen måste du skapa ditt kluster med version 5.7 eller högre av Service Fabric-körningen via Azure-portalen tillsammans med motsvarande SDK. 

> [!NOTE]
> Den här funktionen är en förhandsversion och stöds inte i produktion.
> I exemplen nedan baseras på runtime version 6.0 och SDK-version 2.8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Distribuera en Docker Compose-fil i Service Fabric

Följande kommandon för att skapa ett Service Fabric-program (med namnet `fabric:/TestContainerApp`), som du kan övervaka och hantera precis som alla andra Service Fabric-program. Du kan använda det angivna programnamnet för hälsoförfrågningar.
Service Fabric identifierar ”DeploymentName” som identifierare för Compose-distributionen.

### <a name="use-powershell"></a>Använd PowerShell

Skapa ett Service Fabric Compose-distributionen från en docker-compose.yml-fil genom att köra följande kommando i PowerShell:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` och `RegistryPassword` avser container registreringsanvändarnamn och lösenord. När du har slutfört distributionen, kan du kontrollera dess status med hjälp av följande kommando:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Om du vill ta bort Compose-distributionen via PowerShell, använder du följande kommando:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Om du vill starta en uppgradering av Compose-distributionen via PowerShell använder du följande kommando:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Uppgradera via PowerShell, Använd följande kommando för att återställa Compose-distributionen:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

När uppgraderingen har godkänts kan följa spåras med hjälp av följande kommando:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Använda Azure Service Fabric CLI (sfctl)

Du kan också använda följande Service Fabric CLI-kommando:

```azurecli
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

När du har skapat distributionen, kan du kontrollera dess status med hjälp av följande kommando:

```azurecli
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Om du vill ta bort Compose-distributionen, använder du följande kommando:

```azurecli
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Starta en uppgradering av Compose-distributionen med följande kommando:

```azurecli
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Uppgradera, Använd följande kommando för att återställa Compose-distributionen:

```azurecli
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

När uppgraderingen har godkänts kan följa spåras med hjälp av följande kommando:

```azurecli
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Stöds Compose-direktiven

Den här förhandsversionen stöder en delmängd av konfigurationsalternativ från Skriv version 3-format, inklusive följande primitiver:

* Tjänster > Distribuera > repliker
* Tjänster > Distribuera > placering > begränsningar
* Tjänster > Distribuera > resurser > gränser
    * -cpu-shares
    * -minne
    * -memory-swap
* Tjänster > kommandon
* Tjänster > miljö
* Tjänster > portar
* Tjänster > bild
* Tjänster > isolering (endast för Windows)
* Tjänster > loggning > drivrutin
* Tjänster > loggning > drivrutinen > Alternativ
* Volym & Distribuera > volym

Konfigurera klustret för att genomdriva resursbegränsningar, enligt beskrivningen i [Service Fabric resursstyrning](service-fabric-resource-governance.md). Alla andra Docker Compose direktiv stöds inte för den här förhandsversionen.

### <a name="ports-section"></a>Portar avsnittet

Ange antingen http eller https-protokollet i avsnittet portar som används av Service Fabric service-lyssnare. Detta säkerställer att protokollet som slutpunkten har publicerats korrekt med namngivningstjänsten att omvänd proxyserver som vidarebefordrar begäranden:
* För att dirigera till oskyddade Service Fabric Compose-tjänster, ange **/http**. Till exempel- **”80:80 / http”**.
* För att dirigera till säkra Service Fabric Compose-tjänster, ange **/https**. Till exempel- **”443:443 / https”**.

> [!NOTE]
> /Http och /https portar avsnittet syntax är specifika för Service Fabric för att registrera rätt URL för Service Fabric-lyssnare.  Om Docker compose file-syntax verifieras programmässigt, kan det orsaka ett valideringsfel.

## <a name="servicednsname-computation"></a>ServiceDnsName beräkning

Om tjänstnamnet som du anger i en Compose-fil är ett fullständigt kvalificerat domännamn (dvs, den innehåller en punkt [.]), DNS-namnet som registrerats av Service Fabric är `<ServiceName>` (inklusive punkten). Om inte, varje vägsegment i namnet på programmet blir en domänetiketten i tjänsten DNS-namn med den första vägsegment blir toppnivådomänen etiketten.

Exempel: om det angivna programnamnet är `fabric:/SampleApp/MyComposeApp`, `<ServiceName>.MyComposeApp.SampleApp` skulle vara registrerat DNS-namn.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Compose-distributionen (definition av instans) kontra appmodellen för Service Fabric (typdefinition)

En docker-compose.yml-filen beskriver en distribuerbar uppsättning behållare, inklusive egenskaper och konfigurationer.
Filen kan exempelvis innehålla miljövariabler och portar. Du kan även ange parametrar för distribution, till exempel placeringsbegränsningar, resursgränser och DNS-namn, i filen docker-compose.yml.

Den [Service Fabric-programmodellen](service-fabric-application-model.md) använder tjänsten typer och programtyper, där du kan använda många instanser av samma typ av programmet. Du kan till exempel ha en programinstans per kund. Den här typen-baserade modellen har stöd för flera versioner av samma programtyp som har registrerats med körningsmiljön.

Till exempel kund A kan ha ett program som instansierats med AppTypeA 1.0 och kunden B kan ha ett annat program instantieras med samma typ och version. Du definierar programtyperna i applikationsmanifesten och du anger programparametrar namn och en distribution när du skapar programmet.

Även om den här modellen erbjuder flexibilitet, planerar vi också att stödja en enklare, instans-baserad distributionsmodell där typer är implicit från manifestfilen. I den här modellen får varje applikation en egen oberoende manifest. Vi förhandsgranskar den här insatsen genom att lägga till stöd för docker-compose.yml, vilket är ett instans-baserade distributionen-format.

## <a name="next-steps"></a>Nästa steg

* Läs igenom den [Service Fabric-programmodellen](service-fabric-application-model.md)
* [Kom igång med Service Fabric CLI](service-fabric-cli.md)

<!-- Update_Description: wording update -->

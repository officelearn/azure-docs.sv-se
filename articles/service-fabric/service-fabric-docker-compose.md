---
title: Azure Service Fabric Docker Compose distribution Preview
description: "Azure Service Fabric accepterar Docker Compose format för att göra det lättare att dirigera befintliga behållare med hjälp av Service Fabric. Detta stöd är för närvarande under förhandsgranskning."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 6c4e29a9d7976f5f18d3cf825b22cdef04c0c16d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Docker Compose distributionen stöd i Azure Service Fabric (förhandsgranskning)

Docker använder den [docker-compose.yml](https://docs.docker.com/compose) filen för att definiera flera behållare program. Om du vill göra det enklare för kunder bekant med Docker att samordna befintliga behållarprogram på Azure Service Fabric, har vi inkluderat preview stöd för distribution av Docker Compose internt i plattformen. Service Fabric kan acceptera version 3 och senare av `docker-compose.yml` filer. 

Eftersom detta stöd i förhandsgranskningen stöds bara en del av Skriv direktiven. Till exempel stöds programuppgraderingar inte. Du kan alltid ta bort och distribuera program i stället för att uppgradera dem.

Om du vill använda den här förhandsversionen du skapa ett kluster med version 5.7 eller en större Service Fabric Runtime via Azure portal tillsammans med motsvarande SDK. 

> [!NOTE]
> Den här funktionen är i förhandsvisning och stöds inte i produktion.
> Exemplen nedan baseras på runtime version 6.0 och SDK version 2.8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Distribuera en Docker Compose fil på Service Fabric

Följande kommandon skapar ett Service Fabric-program (med namnet `fabric:/TestContainerApp`), som du kan övervaka och hantera som alla andra Service Fabric-program. Du kan använda det angivna programnamnet för hälsoförfrågningar.
Service Fabric identifieras ”DeploymentName” som identifierare för Skriv distributionen.

### <a name="use-powershell"></a>Använd PowerShell

Skapa en distribution av Service Fabric utgöra från en docker-compose.yml-fil genom att köra följande kommando i PowerShell:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` och `RegistryPassword` referera till behållaren registret användarnamn och lösenord. När du har slutfört distributionen, kan du kontrollera dess status med hjälp av följande kommando:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Om du vill ta bort distributionen Skriv via PowerShell, använder du följande kommando:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Om du vill starta en uppgradering för distribution av Skriv via PowerShell använder du följande kommando:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

När uppgraderingen har accepterats kan uppgradering pågår spåras med följande kommando:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -Deployment TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Använd Azure Service Fabric CLI (sfctl)

Alternativt kan du använda följande kommando för Service Fabric CLI:

```azurecli
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

När du har skapat distributionen kan kontrollera du dess status med hjälp av följande kommando:

```azurecli
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Om du vill ta bort Skriv distributionen, använder du följande kommando:

```azurecli
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Om du vill starta en uppgradering för distribution av Skriv använder du följande kommando:

```azurecli
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

När uppgraderingen har accepterats kan uppgradering pågår spåras med följande kommando:

```azurecli
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Skriv-direktiv som stöds

Den här förhandsversionen stöder en delmängd av konfigurationsalternativ från Skriv version 3-format, inklusive följande primitiver:

* Tjänster > Distribuera > repliker
* Tjänster > Distribuera > placering > begränsningar
* Tjänster > Distribuera > resurser > gränser
    * cpu - resurser
    * -minne
    * -minne-byte
* Tjänster > kommandon
* Services > Environment
* Tjänster > portar
* Tjänster > bild
* Tjänster > isolering (endast för Windows)
* Tjänster > loggning > drivrutin
* Tjänster > loggning > drivrutinen > Alternativ
* Volymen & Distribuera > volym

Konfigurera kluster för att genomdriva gränserna, enligt beskrivningen i [Service Fabric resurs styrning](service-fabric-resource-governance.md). Alla andra Docker Compose direktiv stöds inte för den här förhandsversionen.

## <a name="servicednsname-computation"></a>ServiceDnsName beräkning

Om tjänstnamnet som du anger i en Skriv-filen är ett fullständigt kvalificerat domännamn (dvs, den innehåller en punkt [.]), DNS-namn som registrerats av Service Fabric är `<ServiceName>` (inklusive punkten). Om inte, varje sökvägssegmentet i namnet på programmet blir en domänetikett i tjänsten DNS-namn, med det första sökvägssegmentet blir toppnivådomänen etiketten.

Om det angivna programnamnet är till exempel `fabric:/SampleApp/MyComposeApp`, `<ServiceName>.MyComposeApp.SampleApp` skulle vara registrerade DNS-namn.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Skapa distribution (definition av instans) jämfört med Service Fabric-appmodell (typdefinition)

En filen docker-compose.yml beskrivs distribuerbara behållare, inklusive deras egenskaper och konfigurationer.
Filen kan innehålla miljövariabler och portar. Du kan också ange distribution parametrar, till exempel placeringen och gränserna för DNS-namn, i filen docker-compose.yml.

Den [Service Fabric programmodell](service-fabric-application-model.md) använder tjänsten typer och programtyper, där du kan ha många programinstanser av samma typ. Du kan till exempel ha en programinstansen per kund. Den här typen baserat modellen har stöd för flera versioner av samma programtyp som har registrerats med körningsmiljön.

Till exempel kund A kan ha ett program som instansieras med AppTypeA 1.0 och kunden B kan ha ett annat program, skapa en instans av samma typ och version. Du definierar programtyperna i manifest för programmet och när du skapar programmet anger applikationsparametrarna namn och distribution.

Även om den här modellen ger flexibilitet, planerar vi också att stödja en enklare, instans-baserade distributionsmodell där typer är implicit från manifestfilen. I den här modellen får varje program egna oberoende manifest. Vi förhandsgranskar detta arbete genom att lägga till stöd för docker-compose.yml, vilket är ett instans-baserad distribution-format.

## <a name="next-steps"></a>Nästa steg

* Läsa på den [programmodell för Service Fabric](service-fabric-application-model.md)
* [Kom igång med Service Fabric CLI](service-fabric-cli.md)

---
title: Förhandsversion av Azure Service Fabric Docker Compose Deployment
description: Azure Service Fabric accepterar Docker Compose-format för att göra det enklare att dirigera befintliga behållare med Hjälp av Service Fabric. Det här stödet är för närvarande i förhandsversion.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: f84dd0ecb7a4002182c8455bfd86354d794a6f7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282463"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Support för Docker Compose-distribution i Azure Service Fabric (förhandsversion)

Docker använder [docker-compose.yml-filen](https://docs.docker.com/compose) för att definiera program med flera behållare. För att göra det enkelt för kunder som är bekanta med Docker att orkestrera befintliga behållarprogram på Azure Service Fabric har vi inkluderat förhandssupport för Docker Compose-distribution internt i plattformen. Service Fabric kan acceptera version `docker-compose.yml` 3 och senare av filer. 

Eftersom det här stödet är i förhandsversion stöds bara en delmängd av Compose-direktiven.

Om du vill använda den här förhandsversionen skapar du ditt kluster med version 5.7 eller mer av Service Fabric-körningen via Azure-portalen tillsammans med motsvarande SDK. 

> [!NOTE]
> Den här funktionen är i förhandsversion och stöds inte i produktionen.
> Exemplen nedan är baserade på runtime version 6.0 och SDK version 2.8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Distribuera en Docker Compose-fil på Service Fabric

Följande kommandon skapar ett Service Fabric-program (med namnet `fabric:/TestContainerApp`), som du kan övervaka och hantera som alla andra Service Fabric-program. Du kan använda det angivna programnamnet för hälsofrågor.
Service Fabric känner igen "DeploymentName" som identifierare för Compose-distributionen.

### <a name="use-powershell"></a>Använd PowerShell

Skapa en service fabric compose-distribution från en docker-compose.yml-fil genom att köra följande kommando i PowerShell:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName`och `RegistryPassword` se behållarens register användarnamn och lösenord. När du har slutfört distributionen kan du kontrollera dess status med hjälp av följande kommando:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Om du vill ta bort compose-distributionen via PowerShell använder du följande kommando:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Så här startar du en compose-distributionsuppgradering via PowerShell:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Om du vill återställa uppgraderingen av Compose-distributionen via PowerShell använder du följande kommando:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

När uppgraderingen har accepterats kan uppgraderingsstatusen spåras med följande kommando:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Använda AZURE Service Fabric CLI (sfctl)

Du kan också använda följande Service Fabric CLI-kommando:

```shell
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

När du har skapat distributionen kan du kontrollera dess status med hjälp av följande kommando:

```shell
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Så här tar du bort compose-distributionen:

```shell
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Så här startar du en compose-distributionsuppgradering:

```shell
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Om du vill återställa uppgraderingen av Compose-distributionen använder du följande kommando:

```shell
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

När uppgraderingen har accepterats kan uppgraderingsstatusen spåras med följande kommando:

```shell
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Skriv direktiv som stöds

Den här förhandsgranskningen stöder en delmängd av konfigurationsalternativen från formatet Skriv version 3, inklusive följande primitiver:

* Tjänster > distribuera > repliker
* Tjänster > distribuera > placeringsbegränsningar >
* > distribuera > resurser > gränser
    * -CPU-aktier
    * -minne
    * -minnesswap
* Tjänster > kommandon
* Tjänster > miljö
* Tjänster > portar
* Tjänster > Bild
* Tjänster > isolering (endast för Windows)
* Drivrutin för > loggning >
* Alternativ för > loggning > > drivrutin
* Volym & distribuera > volym

Ställ in klustret för att tillämpa resursgränser, enligt beskrivningen i [Resursstyrning för Tjänst fabric](service-fabric-resource-governance.md). Alla andra Docker Compose-direktiv stöds inte för den här förhandsversionen.

### <a name="ports-section"></a>Avsnittet Portar

Ange antingen http- eller https-protokollet i avsnittet Portar som ska användas av tjänstavlysssnaren Service Fabric. Detta säkerställer att slutpunktsprotokollet publiceras korrekt med namngivningstjänsten så att omvänd proxy kan vidarebefordra begäranden:
* Om du vill dirigera till osäkra service fabric compose-tjänster anger du **/http**. Till exempel - **"80:80/http"**.
* Om du vill dirigera till säkra Service Fabric Compose-tjänster anger du **/https**. Till exempel - **"443:443/https"**.

> [!NOTE]
> Syntaxen för avsnittet /http och /https Portar är specifik för Service Fabric för att registrera rätt URL för Service Fabric-lyssnare.  Om syntaxen för Docker-sammansättningsfil är programmässigt validerad kan det orsaka ett valideringsfel.

## <a name="servicednsname-computation"></a>ServiceDnsName beräkning

Om tjänstnamnet som du anger i en Compose-fil är ett fullständigt kvalificerat domännamn (det vill säga det `<ServiceName>` innehåller en punkt [.]), är DNS-namnet registrerat av Service Fabric (inklusive punkten). Om inte, blir varje sökvägssegment i programnamnet en domänetikett i tjänstens DNS-namn, där det första sökvägssegmentet blir domänetiketten på den översta nivån.

Om det angivna programnamnet till `fabric:/SampleApp/MyComposeApp` `<ServiceName>.MyComposeApp.SampleApp` exempel är , skulle det registrerade DNS-namnet vara.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Komponera distribution (instansdefinition) jämfört med Appmodell för Service Fabric (typdefinition)

En docker-compose.yml-fil beskriver en distributionsbar uppsättning behållare, inklusive deras egenskaper och konfigurationer.
Filen kan till exempel innehålla miljövariabler och portar. Du kan också ange distributionsparametrar, till exempel placeringsbegränsningar, resursbegränsningar och DNS-namn, i filen docker-compose.yml.

[Programmodellen Service Fabric](service-fabric-application-model.md) använder tjänsttyper och programtyper, där du kan ha många programinstanser av samma typ. Du kan till exempel ha en programinstans per kund. Den här typbaserade modellen stöder flera versioner av samma programtyp som har registrerats med körningen.

Kund A kan till exempel få ett program instansierat med typ 1.0 i AppTypeA, och kund B kan få ett annat program instansierat med samma typ och version. Du definierar programtyperna i programmanifesten och anger programnamn och distributionsparametrar när du skapar programmet.

Även om den här modellen erbjuder flexibilitet planerar vi också att stödja en enklare, instansbaserad distributionsmodell där typerna är implicita från manifestfilen. I den här modellen får varje program ett eget oberoende manifest. Vi förhandsgranskar den här insatsen genom att lägga till stöd för docker-compose.yml, som är ett instansbaserat distributionsformat.

## <a name="next-steps"></a>Nästa steg

* Läs upp på [Service Fabric-programmodellen](service-fabric-application-model.md)
* [Kom igång med Service Fabric CLI](service-fabric-cli.md)

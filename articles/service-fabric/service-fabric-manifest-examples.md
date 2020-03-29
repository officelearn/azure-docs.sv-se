---
title: Exempel på Azure Service Fabric-programmanifest
description: Lär dig hur du konfigurerar inställningar för program- och tjänstmanifest för ett Service Fabric-program.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 6f44d2ddaac11545a3a3dc52fd20e02263c6ad67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451649"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Exempel på program- och tjänstmanifest för Service Fabric
Det här avsnittet innehåller exempel på program- och tjänstmanifest. Dessa exempel är inte avsedda att visa viktiga scenarier, men att visa de olika inställningar som är tillgängliga och hur du använder dem. 

Följande är ett index över de funktioner som visas och exempelmanifesten som de är en del av.

|Funktion|Manifest|
|---|---|
|[Resursstyrning](service-fabric-resource-governance.md)|[Reliable Services Application manifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest), [Behållare Application manifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Köra en tjänst som ett lokalt administratörskonto](service-fabric-application-runas-security.md)|[Reliable Services Application manifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Tillämpa en standardprincip på alla servicekodpaket](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Reliable Services Application manifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Skapa användar- och grupphuvudnamn](service-fabric-application-runas-security.md)|[Reliable Services Application manifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|dela ett datapaket mellan tjänstinstanser|[Reliable Services Application manifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Åsidosätt tjänstslutpunkter](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Reliable Services Application manifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Köra ett skript vid start av tjänsten](service-fabric-run-script-at-service-startup.md)|[Röstweb-tjänstmanifest](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Definiera en HTTPS-slutpunkt](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[Röstweb-tjänstmanifest](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Deklarera ett config-paket](service-fabric-application-and-service-manifests.md)|[Tjänstmanifest för VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Deklarera ett datapaket](service-fabric-application-and-service-manifests.md)|[Tjänstmanifest för VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Åsidosätta miljövariabler](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Behållare programmanifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Konfigurera mappning av behållarport till värd](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Behållare programmanifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Konfigurera autentisering av containerregister](service-fabric-get-started-containers.md#configure-container-repository-authentication)|[Behållare programmanifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Ange isoleringsläge](service-fabric-get-started-containers.md#configure-isolation-mode)|[Behållare programmanifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Ange byggspecifika behållaravbildningar för operativsystemet](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Behållare programmanifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Ange miljövariabler](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Tjänstmanifest för behållarfrontEndService](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [tjänstmanifest för Container BackEndService](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[Konfigurera en slutpunkt](service-fabric-get-started-containers.md#configure-communication)|[Tjänstmanifest för behållarfrontEndService](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [tjänstmanifest för Container BackEndService](service-fabric-manifest-example-container-app.md#backendservice-service-manifest), [VotingData-tjänstmanifest](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|skicka kommandon till behållaren|[Tjänstmanifest för behållarfrontEndService](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Importera ett certifikat till en behållare](service-fabric-securing-containers.md)|[Tjänstmanifest för behållarfrontEndService](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Konfigurera volymdrivrutin](service-fabric-containers-volume-logging-drivers.md)|[Tjänstmanifest för behållaren BackEndService](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|


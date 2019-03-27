---
title: Azure Service Fabric-behållaren application manifest-exempel | Microsoft Docs
description: Lär dig hur du konfigurerar programmet och tjänstinställningar manifestet för en Service Fabric-program.
services: service-fabric
documentationcenter: na
author: peterpogorski
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 552ff326ed38175f711924873daab4fcc48f0d09
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500397"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Service Fabric-program och tjänsten manifest exempel
Det här avsnittet innehåller exempel på program och tjänstens manifest. De här exemplen är inte avsedda att visa viktiga scenarier, men för att visa de olika inställningar som är tillgängliga och hur de används. 

Följande är ett index över de funktioner som visas och exempel manifest(s) de är en del av.

|Funktion|Manifest|
|---|---|
|[Resurs-styrning](service-fabric-resource-governance.md)|[Reliable Services programmanifestet](service-fabric-manifest-example-reliable-services-app.md#application-manifest), [behållare programmanifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Köra tjänster som ett lokalt administratörskonto](service-fabric-application-runas-security.md)|[Reliable Services programmanifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Gäller en standardprincip för alla kodpaket för tjänsten](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Reliable Services programmanifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Skapa huvudnamn för användare och grupper](service-fabric-application-runas-security.md)|[Reliable Services programmanifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|dela ett paket av data mellan instanser av tjänsten|[Reliable Services programmanifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Åsidosätt tjänstslutpunkter](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Reliable Services programmanifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Köra ett skript när tjänsten startar](service-fabric-run-script-at-service-startup.md)|[VotingWeb tjänstmanifestet](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Definiera en HTTPS-slutpunkt](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[VotingWeb tjänstmanifestet](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Deklarera ett konfigurationspaket](service-fabric-application-and-service-manifests.md)|[VotingData tjänstmanifestet](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Deklarera ett paket för data](service-fabric-application-and-service-manifests.md)|[VotingData tjänstmanifestet](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Åsidosätt miljövariabler](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Behållaren programmanifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Konfigurerar du mappning av behållare till värd](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Behållaren programmanifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Konfigurera autentisering av behållarregister](service-fabric-get-started-containers.md#configure-container-registry-authentication)|[Behållaren programmanifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Anger isoleringsläge](service-fabric-get-started-containers.md#configure-isolation-mode)|[Behållaren programmanifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Ange OS build-specifika behållaravbildningar](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Behållaren programmanifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Ange miljövariabler](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Behållaren FrontEndService tjänstmanifestet](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [behållare BackEndService tjänstmanifestet](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[Konfigurera en slutpunkt](service-fabric-get-started-containers.md#configure-communication)|[Behållaren FrontEndService tjänstmanifestet](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [behållare BackEndService tjänstmanifestet](service-fabric-manifest-example-container-app.md#backendservice-service-manifest), [VotingData tjänstmanifestet](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|skicka kommandon till behållaren|[Behållaren FrontEndService tjänstmanifestet](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Importera ett certifikat till en behållare](service-fabric-securing-containers.md)|[Behållaren FrontEndService tjänstmanifestet](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Konfigurera drivrutiner för volym](service-fabric-containers-volume-logging-drivers.md)|[Behållaren BackEndService tjänstmanifestet](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|


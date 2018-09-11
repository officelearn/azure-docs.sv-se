---
title: Hantera program för flera miljöer i Azure Service Fabric | Microsoft Docs
description: Azure Service Fabric-program kan köras på kluster intervallet i storlek från en dator till tusentals datorer. I vissa fall kommer du vill konfigurera ditt program på olika sätt för dessa olika miljöer. Den här artikeln beskriver hur du definierar parametrar för olika program per miljö.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: dac96ef6fce38a0557444e181fa6eccb649cfb9a
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44298343"
---
# <a name="manage-applications-for-multiple-environments"></a>Hantera program för flera miljöer

Azure Service Fabric-kluster kan du skapa kluster med var som helst från en till många tusentals datorer. I de flesta fall du märker att behöva distribuera ditt program i flera klusterkonfigurationer: det lokala utvecklingsklustret, ett kluster för delade utveckling och produktion klustret. Alla dessa kluster anses olika miljöer som måste köras på din kod. Binärfilerna för programmet kan köras utan ändringar i den här brett spektrum, men du vill ofta konfigurera programmet på olika sätt.

Överväg två enkla exempel:
  - tjänsten lyssnar på en definierad port, men du måste den porten vara olika för miljöer
  - Du måste ange autentiseringsuppgifter för olika bindning för en databas över miljöer

## <a name="specifying-configuration"></a>Ange konfiguration

Den konfiguration som du anger kan delas i två kategorier:

- Konfiguration som gäller för hur dina tjänster körs
  - Till exempel portnumret för en slutpunkt eller antalet instanser av en tjänst
  - Den här konfigurationen har angetts i programmet eller tjänstmanifestfilen
- Konfiguration som gäller för din programkod
  - Till exempel bindningsinformation för en databas
  - Den här konfigurationen kan anges antingen via konfigurationsfiler eller miljövariabler

> [!NOTE]
> Inte alla attribut i programmet och service manifest filen support parametrar.
> I sådana fall kan behöva du förlitar sig på Ersätt strängar som en del av ditt arbetsflöde för distribution. Du kan använda ett tillägg som ersätter token i Azure DevOps: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens eller du kan köra ett skript-aktivitet för att ersätta värdena i Jenkins.
>

## <a name="specifying-parameters-during-application-creation"></a>Ange parametrar under skapa program

När du skapar en namngiven programinstanser i Service Fabric, har du möjlighet att skicka parametrar. Hur du gör det beror på hur du skapar instansen för programmet.

  - I PowerShell, den [ `New-ServiceFabricApplication` ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet tar applikationsparametrarna som en hash-tabell.
  - Med hjälp av sfctl, den [ `sfctl application create` ](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) kommandot tar parametrar som en JSON-sträng. Skriptet install.sh använder sfctl.
  - Du får en uppsättning parameterfiler i mappen parametrar i programprojektet i Visual Studio. De här parameterfilerna används när du publicerar från Visual Studio, med hjälp av Azure DevOps-tjänsterna eller Team Foundation Server. I Visual Studio är parameterfilerna överförs till skriptet Deploy-FabricApplication.ps1.

## <a name="next-steps"></a>Nästa steg
I följande artiklar visar hur du använder några av de koncept som beskrivs här:

- [Hur du anger miljövariabler för tjänster i Service Fabric](service-fabric-how-to-specify-environment-variables.md)
- [Så här anger du portnumret för en tjänst med parametrar i Service Fabric](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Så här Parameterisera konfigurationsfiler](service-fabric-how-to-parameterize-configuration-files.md)

- [Variabeln miljöreferens](service-fabric-environment-variables-reference.md)

---
title: "Hantera program i flera miljöer i Azure Service Fabric | Microsoft Docs"
description: "Azure Service Fabric-program kan köras på kluster intervallet i storlek från en dator till tusentals datorer. I vissa fall kommer du vill konfigurera ditt program på olika sätt för de olika miljöer. Den här artikeln beskriver hur du definierar parametrar för olika program per miljö."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/09/2017
ms.author: mikhegn
ms.openlocfilehash: 6bc986236d50106485dce151f1f74622f56400c5
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="manage-applications-for-multiple-environments"></a>Hantera program för flera miljöer

Azure Service Fabric-kluster kan du skapa kluster med var som helst från ett till många tusentals datorer. I de flesta fall måste du själv behöva distribuera programmet över flera klusterkonfigurationer: din lokal utveckling, ett kluster med delad utveckling och produktion-klustret. Alla dessa kluster betraktas som olika miljöer måste köras på din kod. Programmet binärfiler kan köras utan ändringar i den här brett spektrum, men du vill ofta konfigurera programmet på olika sätt.

Överväg att två enkla exempel:
  - tjänsten lyssnar på en angiven port, men du måste porten ska vara olika mellan miljöerna
  - Du måste ange autentiseringsuppgifter för olika bindning för en databas över miljöerna

## <a name="specifying-configuration"></a>Ange konfiguration

Den konfiguration som du anger kan delas i två kategorier:

- Konfigurationen som gäller för hur dina tjänster körs
  - Till exempel portnumret för en slutpunkt eller antalet instanser av en tjänst
  - Den här konfigurationen har angetts i programmet eller tjänstmanifestfilen
- Konfigurationen som gäller för din programkod
  - Till exempel bindningsinformation för en databas
  - Den här konfigurationen kan anges antingen via configuration-filer eller miljövariabler

> [!NOTE]
> Inte alla attribut i programmet och service manifest file stöd parametrar.
> I dessa fall måste ha du förlitar sig på att ersätta strängar som en del av arbetsflöde för distribution. I Visual Studio Team Services kan du använda ett tillägg som Ersätt token: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens eller Jenkins kan du köra ett skript-aktivitet för att ersätta värdena.
>

## <a name="specifying-parameters-during-application-creation"></a>Ange parametrar när programmet skulle skapas

När du skapar en namngiven programinstanser i Service Fabric har du möjlighet att skicka in-parametrar. Hur du gör det beror på hur du skapar programinstansen.

  - I PowerShell den [ `New-ServiceFabricApplication` ](https://docs.microsoft.com/en-us/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet tar parametrar för program som en hash-tabell.
  - Med hjälp av sfctl, den [ `sfctl application create` ](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) kommandot tar parametrar som en JSON-sträng. Skriptet install.sh använder sfctl.
  - Visual Studio ger en uppsättning parametern filer i mappen parametrar i projektet för konsolprogrammet. Parameterfilerna används vid publicering från Visual Studio med hjälp av Visual Studio Team Service eller Team Foundation Server. I Visual Studio som parametern-filer skickas till skriptet du distribuera FabricApplication.ps1.

## <a name="next-steps"></a>Nästa steg
I följande artiklar beskriver hur du använder vissa av de begrepp som beskrivs här:

- [Hur du anger miljövariabler för tjänster i Service Fabric](service-fabric-how-to-specify-environment-variables.md)
- [Så här anger du portnumret för en tjänst med parametrar i Service Fabric](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Parameteriserar konfigurationsfiler](service-fabric-how-to-parameterize-configuration-files.md)

- [Variabeln miljöreferens](service-fabric-environment-variables-reference.md)

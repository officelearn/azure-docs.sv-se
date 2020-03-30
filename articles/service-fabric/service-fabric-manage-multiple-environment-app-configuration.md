---
title: Hantera appar för flera miljöer
description: Azure Service Fabric-program kan köras på kluster som varierar i storlek från en dator till tusentals datorer. I vissa fall vill du konfigurera programmet på olika sätt för de olika miljöerna. Den här artikeln beskriver hur du definierar olika programparametrar per miljö.
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 0bca690fd585b288f15cbab21c1c951474390318
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196987"
---
# <a name="manage-applications-for-multiple-environments"></a>Hantera program för flera miljöer

Azure Service Fabric-kluster gör att du kan skapa kluster med allt från en till många tusen datorer. I de flesta fall befinner du dig med att distribuera ditt program över flera klusterkonfigurationer: ditt lokala utvecklingskluster, ett delat utvecklingskluster och ditt produktionskluster. Alla dessa kluster betraktas som olika miljöer som koden har att köra i. Programbinärer kan köras utan ändringar över det här breda spektrumet, men du vill ofta konfigurera programmet på ett annat sätt.

Tänk på två enkla exempel:
  - din tjänst lyssnar på en definierad port, men du behöver den porten för att vara annorlunda i alla miljöer
  - du behöver ange olika bindningsuppgifter för en databas i alla miljöer

## <a name="specifying-configuration"></a>Ange konfiguration

Konfigurationen du anger kan delas in i två kategorier:

- Konfiguration som gäller för hur dina tjänster körs
  - Portnumret för en slutpunkt eller antalet instanser av en tjänst
  - Den här konfigurationen anges i program- eller tjänstmanifestfilen
- Konfiguration som gäller för din programkod
  - Till exempel kan bindande information för en databas
  - Den här konfigurationen kan tillhandahållas antingen via konfigurationsfiler eller miljövariabler

> [!NOTE]
> Alla attribut i program- och tjänstmanifestens stödparametrar.
> I sådana fall måste du förlita dig på att ersätta strängar som en del av distributionsarbetsflödet. I Azure DevOps kan du använda ett https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens tillägg som Ersätt token: eller i Jenkins kan du köra en skriptuppgift för att ersätta värdena.
>

## <a name="specifying-parameters-during-application-creation"></a>Ange parametrar när programmet skapas

När du skapar en namngiven programinstans i Service Fabric har du möjlighet att skicka in parametrar. Hur du gör det beror på hur du skapar programinstansen.

  - I PowerShell [`New-ServiceFabricApplication`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) tar cmdleten programparametrarna som en hashtable.
  - Med hjälp av sfctl tar [`sfctl application create`](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) kommandot parametrar som en JSON-sträng. Det install.sh skriptet använder sfctl.
  - Visual Studio ger dig en uppsättning parameterfiler i mappen Parametrar i programprojektet. Dessa parameterfiler används vid publicering från Visual Studio med Hjälp av Azure DevOps Services eller Azure DevOps Server. I Visual Studio skickas parameterfilerna vidare till skriptet Deploy-FabricApplication.ps1.

## <a name="next-steps"></a>Nästa steg
Följande artiklar visar hur du använder några av de begrepp som beskrivs här:

- [Så här anger du miljövariabler för tjänster i Service Fabric](service-fabric-how-to-specify-environment-variables.md)
- [Så här anger du portnumret för en tjänst med hjälp av parametrar i Service Fabric](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Så härparar du konfigurationsfiler](service-fabric-how-to-parameterize-configuration-files.md)

- [Referens för variabel miljö](service-fabric-environment-variables-reference.md)

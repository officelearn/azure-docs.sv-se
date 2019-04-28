---
title: Konfigurera uppgraderingen av en Service Fabric-program | Microsoft Docs
description: Lär dig hur du konfigurerar inställningar för att uppgradera ett Service Fabric-program med hjälp av Microsoft Visual Studio.
services: service-fabric
documentationcenter: na
author: mikkelhegn
manager: mfussell
editor: tglee
ms.assetid: 1757ba85-0b7b-4f16-8a23-2ddaa61c86c6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/29/2017
ms.author: mikkelhegn
ms.openlocfilehash: 79120371ca2a62e5ef9f2bf38476635db12e9fcc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082861"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Konfigurera uppgraderingen av ett Service Fabric-program i Visual Studio
Visual Studio tools för Azure Service Fabric ger uppgradera support för att publicera lokala eller fjärranslutna kluster. Det finns tre scenarier där du vill uppgradera programmet till en nyare version istället för att ersätta programmet under testning och felsökning:

* Programmet data förloras inte under uppgraderingen.
* Tillgänglighet förblir hög så att det inte går avbrott i tjänsten under uppgraderingen, om det finns tillräckligt med instanser av tjänsten sprids uppgraderingsdomäner.
* Du kan köra testerna mot ett program medan den håller på att uppgraderas.

## <a name="parameters-needed-to-upgrade"></a>Parametrar som behövs för att uppgradera
Du kan välja mellan två typer av distribution: regelbundet eller uppgradera. En vanlig distribution raderas alla tidigare distributionsinformation och data på klustret, medan en uppgraderingen distribuerades bevarar den. När du uppgraderar en Service Fabric-program i Visual Studio, måste du ange programuppgraderingsparametrar och hälsa kontrollera principer. Programuppgraderingsparametrar bidrar till att kontrollera uppgraderingen, medan kontroll hälsoprinciper avgöra om uppgraderingen lyckades. Se [Service Fabric-Programuppgradering: Uppgraderingsparametrar](service-fabric-application-upgrade-parameters.md) för mer information.

Det finns tre lägen för uppgraderingen: *Övervakas*, *UnmonitoredAuto*, och *UnmonitoredManual*.

* En uppgradering av övervakade automatiserar uppgraderingen och hälsokontrollen för programmet.
* Uppgradering UnmonitoredAuto automatiserar uppgraderingen, men hoppar över hälsokontrollen för programmet.
* När du gör en uppgradering på UnmonitoredManual måste du manuellt uppgradera varje uppgraderingsdomän.

Varje Uppgraderingsläge kräver olika uppsättningar med parametrar. Se [programuppgraderingsparametrar](service-fabric-application-upgrade-parameters.md) mer information om alternativen för uppgradering.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Uppgradera ett Service Fabric-program i Visual Studio
Om du använder Visual Studio Service Fabric-verktygen för att uppgradera ett Service Fabric-program, kan du ange en Publicera-process för att vara en uppgradering i stället för en vanlig distribution genom att markera den **uppgradera programmet** markerar du kryssrutan.

### <a name="to-configure-the-upgrade-parameters"></a>Så här konfigurerar du Uppgraderingsparametrar
1. Klicka på den **inställningar** knappen bredvid kryssrutan. Den **redigera uppgradera parametrar** dialogrutan visas. Den **redigera uppgradera parametrar** dialogrutan har stöd för de övervakade och UnmonitoredAuto UnmonitoredManual uppgradera lägena.
2. Välj Uppgraderingsläge som du vill använda och sedan fylla i rutnätet för parametern.

    Varje parameter har standardvärden. Den valfria parametern *DefaultServiceTypeHealthPolicy* tar en hash-tabell som indata. Här är ett exempel på hash-tabell Indataformatet för *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* är en annan valfri parameter som tar en hash-tabell som indata i följande format:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Här är en verkliga exempel:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Om du väljer UnmonitoredManual Uppgraderingsläge måste du starta en PowerShell-konsol för att fortsätta och Slutför uppgraderingen manuellt. Referera till [Service Fabric-Programuppgradering: avancerade ämnen](service-fabric-application-upgrade-advanced.md) att lära dig hur manuell uppgradering fungerar.

## <a name="upgrade-an-application-by-using-powershell"></a>Uppgradera ett program med hjälp av PowerShell
Du kan använda PowerShell-cmdletar för att uppgradera ett Service Fabric-program. Se [självstudier för uppgradering av Service Fabric-program](service-fabric-application-upgrade-tutorial.md) och [Start ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) detaljerad information.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Ange en kontroll av hälsoprincip i program-manifestfilen
Varje tjänst i ett Service Fabric-program kan ha sin egen hälsotillstånd principparametrar som åsidosätter standardinställningarna. Du kan ange dessa parametervärden i program-manifestfilen.

I följande exempel visas hur du använder en unik hälsoprincip kontroll för varje tjänst i manifestet.

```xml
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Nästa steg
Mer information om hur du uppgraderar ett program finns i [uppgradera ett program med Visual Studio](service-fabric-application-upgrade-tutorial.md).

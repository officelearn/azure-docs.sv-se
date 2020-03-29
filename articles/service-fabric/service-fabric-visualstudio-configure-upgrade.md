---
title: Konfigurera uppgraderingen av ett Service Fabric-program
description: Lär dig hur du konfigurerar inställningarna för uppgradering av ett Service Fabric-program med hjälp av Microsoft Visual Studio.
author: mikkelhegn
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: mikhegn
ms.openlocfilehash: ef88a528dbb6d326e5cc742d14c27218eb7502f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464077"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Konfigurera uppgraderingen av ett Service Fabric-program i Visual Studio
Visual Studio-verktyg för Azure Service Fabric ger uppgraderingsstöd för publicering till lokala kluster eller fjärrkluster. Det finns tre scenarier där du vill uppgradera programmet till en nyare version i stället för att ersätta programmet under testning och felsökning:

* Programdata går inte förlorade under uppgraderingen.
* Tillgängligheten är fortfarande hög så att det inte blir några avbrott i tjänsten under uppgraderingen, om det finns tillräckligt många tjänstinstanser spridda över uppgraderingsdomäner.
* Tester kan köras mot ett program medan det uppgraderas.

## <a name="parameters-needed-to-upgrade"></a>Parametrar som behövs för att uppgradera
Du kan välja mellan två typer av distribution: vanlig eller uppgradering. En regelbunden distribution raderar all tidigare distributionsinformation och data i klustret, medan en uppgraderingsdistribution bevarar den. När du uppgraderar ett Service Fabric-program i Visual Studio måste du ange parametrar för uppgradering av program och hälsokontrollprinciper. Programuppgraderingsparametrar hjälper till att kontrollera uppgraderingen, medan hälsokontrollprinciper avgör om uppgraderingen lyckades. Se [Uppgradering av Programuppgraderingen](service-fabric-application-upgrade-parameters.md) för Service Fabric: uppgradera parametrar för mer information.

Det finns tre uppgraderingslägen: *Övervakad,* *UnmonitoredAuto*och *UnmonitoredManual*.

* En övervakad uppgradering automatiserar hälsokontrollen för uppgradering och program.
* En UnmonitoredAuto-uppgradering automatiserar uppgraderingen, men hoppar över programmets hälsokontroll.
* När du gör en unmonitoredManual uppgradering måste du uppgradera varje uppgraderingsdomän manuellt.

Varje uppgraderingsläge kräver olika uppsättningar parametrar. Se [Parametrar för uppgradering av program](service-fabric-application-upgrade-parameters.md) om du vill veta mer om tillgängliga uppgraderingsalternativ.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Uppgradera ett Service Fabric-program i Visual Studio
Om du använder verktygen Visual Studio Service Fabric för att uppgradera ett Service Fabric-program kan du ange en publiceringsprocess som en uppgradering i stället för en vanlig distribution genom att markera kryssrutan **Uppgradera programmet.**

### <a name="to-configure-the-upgrade-parameters"></a>Så här konfigurerar du uppgraderingsparametrar
1. Klicka på knappen **Inställningar** bredvid kryssrutan. Dialogrutan **Redigera uppgraderingsparametrar** visas. Dialogrutan **Redigera uppgraderingsparametrar** stöder lägena Övervakad, Oövervakad Auto och OövervakadManual.
2. Välj det uppgraderingsläge som du vill använda och fyll sedan i parameterrutnätet.

    Varje parameter har standardvärden. Den valfria parametern *DefaultServiceTypeHealthPolicy* tar en hash-tabellinmatning. Här är ett exempel på hash-tabellens inmatningsformat för *DefaultServiceTypeHealthPolicy:*

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* är en annan valfri parameter som tar en hash-tabell indata i följande format:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Här är ett verkligt exempel:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Om du väljer UnmonitoredManual upgrade mode måste du manuellt starta en PowerShell-konsol för att fortsätta och slutföra uppgraderingsprocessen. Se Uppgradering av [Service Fabric-programmet: avancerade avsnitt](service-fabric-application-upgrade-advanced.md) för att lära dig hur manuell uppgradering fungerar.

## <a name="upgrade-an-application-by-using-powershell"></a>Uppgradera ett program med PowerShell
Du kan använda PowerShell-cmdlets för att uppgradera ett Service Fabric-program. Mer information finns i [självstudiekursen](service-fabric-application-upgrade-tutorial.md) för uppgradering av programuppgradering av tjänst fabric och [Start-ServiceFabricApplicationUpgrade.](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade)

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Ange en hälsokontrollprincip i programmanifestfilen
Varje tjänst i ett Service Fabric-program kan ha sina egna hälsoprincipparametrar som åsidosätter standardvärdena. Du kan ange dessa parametervärden i programmanifestfilen.

I följande exempel visas hur du tillämpar en unik hälsokontrollprincip för varje tjänst i programmanifestet.

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
Mer information om hur du uppgraderar ett program finns i [Uppgradera ett program med Visual Studio](service-fabric-application-upgrade-tutorial.md).
